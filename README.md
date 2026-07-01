# 📚 QuizFeed

**Ứng dụng ôn tập trắc nghiệm theo chương — học và thi thử ngay trên trình duyệt, không cần cài đặt, không cần backend.**

QuizFeed là một single-file web app (HTML/CSS/JS thuần), chạy hoàn toàn offline sau lần tải đầu tiên, lưu toàn bộ dữ liệu và tiến trình học ngay trên máy của bạn (localStorage). Phù hợp cho học sinh, sinh viên tự ôn tập theo chương, theo môn, với dữ liệu câu hỏi do chính bạn tạo ra (hoặc nhờ AI tạo giúp).

🔗 **Demo:** _(điền link GitHub Pages của bạn vào đây sau khi deploy)_
`https://<tên-user>.github.io/quizfeed/`

---

## ✨ Tính năng

- ✅ **4 loại câu hỏi**: Trắc nghiệm (MCQ), Điền khuyết, Đúng/Sai, Ghép đôi
- ✅ **Tổ chức theo Môn → Chương → Câu hỏi**, dễ quản lý nhiều môn học cùng lúc
- ✅ **Ôn tập câu sai**: tự động lưu lại các câu bạn trả lời sai để ôn lại riêng
- ✅ **Test tổng hợp**: trộn câu hỏi từ nhiều chương / nhiều môn để kiểm tra tổng quát
- ✅ **Xáo trộn câu hỏi & đáp án** mỗi lần làm bài (MCQ), tránh học vẹt vị trí
- ✅ **Thống kê tiến trình**: theo dõi số câu đã làm, tỉ lệ đúng, streak học tập
- ✅ **Import dữ liệu** từ file JSON hoặc từ URL trực tiếp
- ✅ **Sao lưu / Phục hồi toàn bộ dữ liệu** (Import/Export tổng)
- ✅ **Chạy offline hoàn toàn** — hỗ trợ PWA (cài như app trên điện thoại)
- ✅ **Giao diện tối ưu di động**, hỗ trợ thao tác chạm, không cần internet sau khi tải

---

## 🚀 Cài đặt và sử dụng

### Cách 1: Dùng ngay qua GitHub Pages (khuyến nghị)
Chỉ cần mở link demo ở trên bằng trình duyệt (điện thoại hoặc máy tính). Không cần cài đặt gì thêm.

> 💡 Trên điện thoại, bạn có thể chọn **"Thêm vào màn hình chính"** để dùng như một ứng dụng thật (PWA), chạy offline hoàn toàn.

### Cách 2: Tải về và chạy cục bộ
1. Tải file `QuizFeed.html` về máy.
2. Mở trực tiếp file đó bằng trình duyệt (Chrome, Safari, Edge...).
3. Không cần server, không cần cài Node.js hay bất kỳ công cụ nào khác.

### Cách 3: Tự deploy lên GitHub Pages
1. Tạo một repository mới trên GitHub.
2. Upload toàn bộ các file trong thư mục này lên repo.
3. Vào **Settings → Pages**, chọn nhánh (thường là `main`) và thư mục gốc (`/`).
4. Sau vài phút, trang của bạn sẽ có tại `https://<tên-user>.github.io/<tên-repo>/`.

---

## 📥 Cách import câu hỏi vào QuizFeed

1. Mở QuizFeed, bấm nút **Import** (hoặc mở ngăn kéo chương trình).
2. Chọn file `.json` chứa dữ liệu câu hỏi theo đúng cấu trúc (xem file mẫu `QuizFeed_Mau_ToanLy.json`).
3. Hoặc dán **URL trực tiếp tới file .json** (ví dụ link raw trên GitHub) vào ô import từ URL.

Xem chi tiết cấu trúc dữ liệu và hướng dẫn dùng AI để tạo câu hỏi tại file [`QuizFeed_HuongDan.md`](./QuizFeed_HuongDan.md).

---

## 🤖 Tạo câu hỏi bằng AI

Bạn có thể nhờ Claude, ChatGPT hoặc bất kỳ AI nào tạo file JSON câu hỏi theo đúng format của QuizFeed. Prompt mẫu và hướng dẫn chi tiết có trong file [`QuizFeed_HuongDan.md`](./QuizFeed_HuongDan.md).

---

## 📂 Cấu trúc dự án

```
📁 quizfeed/
├── 📄 QuizFeed.html              ← Ứng dụng chính (mở file này để dùng)
├── 📄 index.html                 ← Trang chuyển hướng cho GitHub Pages
├── 📄 README.md                  ← File này
├── 📄 QuizFeed_Mau_ToanLy.json   ← Bộ câu hỏi mẫu (Toán 12 + Lý 12)
├── 📄 QuizFeed_HuongDan.md       ← Hướng dẫn sử dụng cho người học
├── 📄 QuizFeed_TongQuan.md       ← Tài liệu kỹ thuật cho người phát triển
└── 📄 .gitignore
```

---

## 🛠️ Công nghệ

Vanilla JavaScript thuần, không framework, không build step, không dependency ngoài. Toàn bộ ứng dụng nằm gọn trong 1 file HTML duy nhất. Chi tiết kiến trúc xem tại [`QuizFeed_TongQuan.md`](./QuizFeed_TongQuan.md).

---

## 📄 Giấy phép

Phát hành theo giấy phép **MIT License** — tự do sử dụng, chỉnh sửa, và phân phối lại.

```
MIT License

Copyright (c) 2026

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
