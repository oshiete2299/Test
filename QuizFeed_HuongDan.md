# 📖 Hướng dẫn sử dụng QuizFeed

Hướng dẫn dành cho người học — cách mở app, import câu hỏi, và tận dụng các tính năng ôn tập.

---

## 1. Cách mở ứng dụng

### Trên điện thoại
1. Mở link demo bằng trình duyệt (Chrome/Safari).
2. Để dùng như một app thật, không cần mở trình duyệt mỗi lần:
   - **Android (Chrome)**: bấm menu (⋮) → **"Thêm vào Màn hình chính"**.
   - **iPhone (Safari)**: bấm nút Chia sẻ (⬆️) → **"Thêm vào Màn hình chính"**.
3. Từ giờ có thể mở app trực tiếp từ màn hình chính, kể cả khi không có mạng.

### Trên máy tính
- Mở link demo bằng bất kỳ trình duyệt nào, hoặc
- Tải file `QuizFeed.html` về máy và mở trực tiếp (double-click), không cần cài gì thêm.

> 📌 Toàn bộ dữ liệu (câu hỏi đã import, tiến trình học, câu sai) được lưu ngay trên trình duyệt của bạn (localStorage). Nếu xóa dữ liệu trình duyệt hoặc đổi máy, hãy **Export** để sao lưu trước.

---

## 2. Cách import file câu hỏi

1. Mở QuizFeed, vào màn hình chính hoặc bấm mở ngăn kéo (drawer) môn học.
2. Bấm nút **Import**.
3. Chọn 1 trong 2 cách:
   - **Chọn file từ máy**: chọn file `.json` đã tải về (ví dụ `QuizFeed_Mau_ToanLy.json`).
   - **Import từ URL**: dán link trực tiếp tới file `.json` (ví dụ link "raw" của file trên GitHub) rồi bấm tải.
4. Nếu file đúng định dạng, câu hỏi sẽ được thêm vào danh sách môn học ngay lập tức.
5. Nếu file sai định dạng, app sẽ báo lỗi cụ thể (thiếu trường nào, sai kiểu dữ liệu nào) để bạn sửa lại.

> ⚠️ Import nhiều lần với cùng tên môn/chương sẽ **gộp** câu hỏi vào chương đã có, không tạo bản trùng — bạn có thể import bổ sung dần dần.

---

## 3. Cách tạo file câu hỏi mới bằng AI

QuizFeed nhận file JSON theo cấu trúc: **Môn (subject) → Chương (chapter) → Câu hỏi (question)**.

### Prompt mẫu để gửi cho AI (Claude, ChatGPT...)

```
Hãy tạo cho tôi một file JSON câu hỏi trắc nghiệm cho môn [TÊN MÔN HỌC],
chương [TÊN CHƯƠNG], gồm [SỐ LƯỢNG] câu hỏi, theo đúng cấu trúc sau
(dùng cho app QuizFeed):

{
  "subjects": [
    {
      "name": "Tên môn học",
      "chapters": [
        {
          "name": "Tên chương",
          "questions": [
            {
              "type": "mcq",
              "q": "Nội dung câu hỏi",
              "options": ["Đáp án A", "Đáp án B", "Đáp án C", "Đáp án D"],
              "answer": 0,
              "explain": "Giải thích vì sao đáp án đúng"
            }
          ]
        }
      ]
    }
  ]
}

Yêu cầu:
- Trộn đủ các loại câu hỏi: mcq, fill_blank, true_false, matching
- Mỗi câu đều có phần "explain" giải thích ngắn gọn
- Nội dung chính xác, đúng chương trình học
- Trả về đúng định dạng JSON, không thêm chữ thừa ngoài JSON
```

Sau khi AI trả lời, copy toàn bộ nội dung JSON, lưu thành file `.json`, rồi import vào QuizFeed như hướng dẫn ở mục 2.

### Cấu trúc chi tiết từng loại câu hỏi

**Trắc nghiệm (mcq)** — bắt buộc `options` (tối thiểu 2), `answer` là số nguyên (chỉ số đáp án đúng, bắt đầu từ 0):
```json
{
  "type": "mcq",
  "q": "2 + 2 = ?",
  "options": ["3", "4", "5", "6"],
  "answer": 1,
  "explain": "2 + 2 = 4"
}
```

**Điền khuyết (fill_blank)** — `answer` là chuỗi văn bản (không phân biệt hoa/thường, khoảng trắng thừa):
```json
{
  "type": "fill_blank",
  "q": "Thủ đô của Việt Nam là ___.",
  "answer": "Hà Nội",
  "explain": "Hà Nội là thủ đô của Việt Nam."
}
```

**Đúng/Sai (true_false)** — `answer` là `true` hoặc `false`:
```json
{
  "type": "true_false",
  "q": "Trái Đất quay quanh Mặt Trời.",
  "answer": true,
  "explain": "Đúng, theo mô hình nhật tâm."
}
```

**Ghép đôi (matching)** — `pairs` là mảng các cặp `left`/`right`; `answer` là object ánh xạ chỉ số vế trái → chỉ số vế phải đúng (mặc định vế trái khớp đúng vị trí trong `pairs`):
```json
{
  "type": "matching",
  "q": "Ghép quốc gia với thủ đô.",
  "pairs": [
    { "left": "Việt Nam", "right": "Hà Nội" },
    { "left": "Nhật Bản", "right": "Tokyo" }
  ],
  "answer": { "0": 0, "1": 1 },
  "explain": "Ghép đúng theo quốc gia - thủ đô tương ứng."
}
```

> 💡 Có thể import 1 file chỉ chứa **1 môn đơn** (không cần bọc trong `"subjects": [...]`), miễn có `"name"` và `"chapters"` ở cấp cao nhất. QuizFeed tự nhận diện cả 2 kiểu.

---

## 4. Các tính năng chính khi học

- **Ôn theo chương**: chọn môn → chọn chương → làm từng câu, có giải thích ngay sau khi trả lời.
- **Ôn câu sai**: hệ thống tự lưu ID các câu bạn từng trả lời sai; vào mục "Ôn câu sai" để luyện lại riêng nhóm này.
- **Test tổng hợp**: trộn ngẫu nhiên câu hỏi từ nhiều chương/môn để tự kiểm tra kiến thức tổng quát, mô phỏng bài thi thật.
- **Xáo trộn câu hỏi & đáp án**: mỗi lần làm lại, thứ tự câu hỏi và các lựa chọn (với MCQ) được xáo trộn để tránh học thuộc vị trí thay vì kiến thức.
- **Thống kê**: theo dõi số câu đã làm, tỷ lệ đúng theo từng chương/môn, và số ngày học liên tục (streak).

---

## 5. Sao lưu và phục hồi dữ liệu

- Vào mục cài đặt/quản lý dữ liệu, dùng **Export** để tải về 1 file JSON chứa toàn bộ câu hỏi + tiến trình học của bạn.
- Dùng **Import (tổng)** với file đó để khôi phục lại dữ liệu trên máy khác hoặc sau khi xóa cache trình duyệt.
- Nên sao lưu định kỳ, đặc biệt trước khi xóa dữ liệu trình duyệt hoặc đổi thiết bị.

---

## 6. Xử lý sự cố thường gặp

| Vấn đề | Nguyên nhân thường gặp | Cách khắc phục |
|---|---|---|
| Import báo lỗi cấu trúc | File JSON thiếu trường bắt buộc hoặc sai kiểu dữ liệu | Đọc kỹ thông báo lỗi (app chỉ rõ chương/câu hỏi nào lỗi), đối chiếu với mục 3 |
| Import từ URL thất bại | Link không phải file JSON trực tiếp, hoặc server chặn CORS | Dùng link "raw" (ví dụ raw.githubusercontent.com), hoặc tải file về rồi import từ máy |
| Mất dữ liệu sau khi xóa lịch sử trình duyệt | Dữ liệu lưu trong localStorage bị xóa cùng cache | Luôn Export sao lưu định kỳ trước khi dọn dẹp trình duyệt |
| App không hoạt động offline | Service worker chưa kịp cài đặt ở lần mở đầu tiên | Mở app khi có mạng ít nhất 1 lần trước khi dùng offline |
