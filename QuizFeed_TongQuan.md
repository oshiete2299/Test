# 🔧 QuizFeed — Tổng quan kỹ thuật

Tài liệu dành cho người phát triển muốn hiểu, bảo trì, hoặc mở rộng ứng dụng QuizFeed.

---

## 1. Kiến trúc tổng quan

QuizFeed là một ứng dụng **single-file, client-side only**:

- **1 file HTML duy nhất** (`QuizFeed.html`) chứa toàn bộ HTML, CSS, và JavaScript.
- **Không backend, không server, không build step, không dependency ngoài** — mở file là chạy.
- **Không dùng framework** (không React/Vue) — JavaScript thuần (vanilla JS), style hàm khai báo (`function`), nối chuỗi bằng `+` thay vì template literals.
- **PWA inline**: manifest và service worker được tạo động bằng Blob URL ngay trong file, không cần file `manifest.json` hay `sw.js` riêng — cho phép cài đặt như app và chạy offline.
- **Lưu trữ**: toàn bộ dữ liệu (câu hỏi, tiến trình, cài đặt) nằm trong `localStorage` của trình duyệt, dưới dạng JSON.

### Vì sao thiết kế single-file?
Giúp việc chia sẻ, sao lưu, và deploy cực kỳ đơn giản — chỉ cần 1 file để mang đi, không lo version mismatch giữa HTML/CSS/JS, không cần bundler.

---

## 2. Các khóa localStorage

| Khóa | Nội dung |
|---|---|
| `quizfeed:data:v1` | Toàn bộ dữ liệu môn học/chương/câu hỏi (`subjects`) |
| `quizfeed:progress:v1` | Tiến trình học theo từng chương (lịch sử làm bài, câu sai, review queue) |
| `quizfeed:sound:v1` | Bật/tắt âm thanh |
| `quizfeed:streak:v1` | Dữ liệu streak học tập (ngày học liên tục) |
| `quizfeed:theme:v1` | Giao diện sáng/tối |
| `quizfeed:studymode:v1` | Chế độ học đang chọn |

Đọc/ghi qua 2 hàm helper dùng chung: `loadJSON(key, fallback)` và `saveJSON(key, val)` — cả hai đều bọc `try/catch` để không crash app khi localStorage đầy hoặc dữ liệu hỏng. `saveJSON` báo lỗi rõ ràng cho người dùng khi bộ nhớ đầy.

---

## 3. Schema dữ liệu

### Cấu trúc `subjects` (lưu tại `quizfeed:data:v1`)

```
subjects: [
  {
    id: string,          // sinh tự động khi thêm mới
    name: string,
    chapters: [
      {
        id: string,       // sinh tự động khi thêm mới
        name: string,
        questions: [ Question, ... ]
      }
    ]
  }
]
```

### Question — 4 loại được hỗ trợ (`type`)

| type | Trường bắt buộc | Kiểu dữ liệu `answer` |
|---|---|---|
| `mcq` (mặc định nếu bỏ trống) | `options` (mảng string, ≥2 phần tử) | số nguyên — chỉ số đáp án đúng trong `options` |
| `fill_blank` | — | string (đáp án text) |
| `true_false` | — | boolean |
| `matching` | `pairs` (mảng `{left, right}`, ≥2 cặp) | object ánh xạ `{ "leftIdx": rightIdx }` |

Mọi loại câu hỏi đều có thể có thêm `explain` (string, tùy chọn) — hiển thị sau khi trả lời.

`id` của mỗi câu hỏi **không bắt buộc có trong file import** — được tự sinh và gán ổn định theo công thức `subjectId + "-" + chapterId + "-" + index` bởi hàm `ensureQuestionIds()`, đảm bảo tính ổn định để tracking câu sai (`wrongIds`) qua các lần xáo trộn.

### Cấu trúc `progress` (lưu tại `quizfeed:progress:v1`, key = chapter id)

```
progress[chapterId] = {
  history: [ { date: timestamp, correct: number, total: number }, ... ],
  wrongIds: [ questionId, ... ],
  bestPct: number,
  reviewQueue: [ questionId, ... ]
}
```

Có cơ chế **migration tự động** (`migrateProgress()`) để chuyển đổi format cũ (`{done, correct, total}`) sang format mới khi phát hiện dữ liệu cũ trong localStorage — đảm bảo tương thích ngược khi nâng cấp app.

---

## 4. Luồng Import dữ liệu

1. `validateImportedData(data)` — validate nghiêm ngặt cấu trúc, trả về danh sách lỗi chi tiết theo từng subject/chapter/question nếu có.
2. Chấp nhận 3 dạng đầu vào tương đương:
   - `{ "subjects": [...] }`
   - `[...]` (mảng subjects trực tiếp)
   - `{ "name": ..., "chapters": [...] }` (1 subject đơn)
3. `mergeImportedData(data)` — gộp dữ liệu mới vào `subjects` hiện có (merge theo tên subject/chapter, không tạo bản trùng).
4. `ensureQuestionIds()` — đảm bảo mọi câu hỏi có `id` ổn định.
5. `saveJSON(STORE_KEY, subjects)` — lưu và cập nhật UI (`renderDrawer()`, `showHome()`).

Import hỗ trợ cả từ **file local** (`FileReader` + input file) và **URL trực tiếp** (`fetch`), dùng chung logic validate/merge qua `processImportedSubjectData()`.

---

## 5. Cơ chế xáo trộn (Shuffle)

- `shuffleArray()` — thuật toán **Fisher-Yates** chuẩn (không dùng `sort(() => Math.random())` vốn có bias).
- `shuffleQuestion(q)` — với `mcq`: xáo trộn thứ tự `options`, đồng thời cập nhật lại `answer` cho khớp vị trí mới, giữ `originalAnswerIndex` để đối chiếu khi cần. Với `fill_blank`/`true_false`/`matching`: **không** xáo trộn nội bộ (không có khái niệm "vị trí đáp án" cần xáo), chỉ thứ tự các câu hỏi trong chương được xáo trộn ở tầng cao hơn.

---

## 6. Hướng dẫn mở rộng

### Thêm loại câu hỏi mới
1. Thêm nhánh xử lý trong `validateImportedData()` (khối `if/else if` theo `qType`).
2. Thêm hàm render riêng (theo mẫu `renderMatchingQuestion`, khoảng dòng 2210+).
3. Thêm hàm chấm điểm riêng (theo mẫu `handleFillBlankSubmit`, `handleTrueFalse`).
4. Cập nhật `shuffleQuestion()` nếu loại câu hỏi mới cần logic xáo trộn riêng.
5. Cập nhật tài liệu `QuizFeed_HuongDan.md` với ví dụ JSON cho loại câu hỏi mới.

### Thêm trường thống kê mới
Mở rộng object `progress[chapterId]`, nhớ cập nhật `migrateProgress()` để dữ liệu cũ không bị lỗi khi load lên phiên bản mới.

---

## 7. Lưu ý khi phát triển

- ⚠️ **Không dùng template literals, arrow functions kiểu ES6+ tùy tiện** — codebase theo convention `function` declarations và nối chuỗi bằng `+`, giữ nhất quán để dễ maintain và tối đa khả năng tương thích trình duyệt cũ.
- ⚠️ **Luôn escape HTML khi render nội dung từ dữ liệu người dùng/import** (dùng hàm `escapeHtml()` có sẵn) để tránh XSS — đặc biệt quan trọng ở các hàm render nội dung câu hỏi và `renderDrawer()`.
- ⚠️ **Không phá vỡ tính ổn định của `id` câu hỏi** — vì `wrongIds`/`reviewQueue` dựa vào id để tracking; đổi công thức sinh id sẽ làm mất liên kết với dữ liệu tiến trình cũ của người dùng.
- ⚠️ **localStorage có giới hạn dung lượng** (~5-10MB tùy trình duyệt) — với người dùng có rất nhiều câu hỏi, cân nhắc cảnh báo sớm hoặc hỗ trợ nén dữ liệu.
- ⚠️ **Giữ nguyên tắc single-file** khi thêm tính năng mới — tránh chia nhỏ ra nhiều file trừ khi có lý do kỹ thuật thực sự bắt buộc, vì đây là giá trị cốt lõi giúp app dễ chia sẻ/deploy.
- ⚠️ **Test kỹ validate/merge logic** khi sửa liên quan đến import — đây là điểm dễ vỡ nhất khi người dùng đưa vào dữ liệu không đúng format.
