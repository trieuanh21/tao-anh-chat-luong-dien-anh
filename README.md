# Prompt Studio — AI Local

Ứng dụng web tĩnh bằng HTML/CSS/JavaScript để xây dựng prompt hình ảnh và video từ các ô câu hỏi. Thiết kế dựa trên những kỹ thuật đã rút ra từ hai ví dụ prompt: chân dung điện ảnh giàu cảm xúc và ảnh tư liệu mùa gặt Sunburst.

## Chạy thử trên máy

1. Tải hoặc giải nén repository.
2. Mở `index.html` bằng Chrome, Edge hoặc Firefox.
3. Điền biểu mẫu rồi bấm **Tạo prompt**.
4. Chọn một trong ba tab: **Hướng dẫn cho AI**, **Prompt ảnh/video**, **Negative prompt**.
5. Sao chép hoặc tải kết quả dạng `.txt`.

Không cần cài Python/Node.js, không cần API key, không gửi dữ liệu ra ngoài.

## Đưa lên GitHub và bật GitHub Pages

1. Đăng nhập GitHub và tạo repository mới, ví dụ `prompt-studio`.
2. Chọn Public (nếu muốn trang công khai), sau đó tạo repository.
3. Bấm **Add file → Upload files**, kéo thả `index.html`, `README.md`, `LICENSE` và `.gitignore` vào repository; hoặc tải ZIP này lên máy, giải nén rồi dùng Git.
4. Mở **Settings → Pages**.
5. Trong **Build and deployment**, chọn **Deploy from a branch**.
6. Chọn branch `main`, folder `/(root)`, bấm **Save**.
7. Chờ GitHub build; đường dẫn trang sẽ hiện ở mục Pages, thường có dạng `https://TEN-TAI-KHOAN.github.io/prompt-studio/`.

## Cấu trúc

```text
prompt-studio/
├── index.html
├── README.md
├── LICENSE
└── .gitignore
```

## Kỹ thuật prompt được áp dụng

- **Chủ thể → hành động/tương tác vật lý → bối cảnh**: ưu tiên việc nhân vật đang làm và cách cơ thể/vật thể tương tác.
- **Bố cục theo lớp**: tiền cảnh, chủ thể ở lớp giữa, hậu cảnh; mô tả vị trí cụ thể.
- **Ánh sáng và bảng màu**: lựa chọn riêng, không chỉ dùng từ “cinematic”.
- **Ngôn ngữ máy ảnh có chọn lọc**: tiêu cự/DOF là tùy chọn; có cảnh báo khi một số lựa chọn có thể xung đột.
- **Tính chân thực có thể quan sát**: tay nắm vật, trọng lượng, nếp vải, chất liệu, phối cảnh và bóng đổ.
- **Cảm xúc/câu chuyện**: mô tả khoảnh khắc cần truyền tải.
- **Negative prompt có mục tiêu**: tập trung vào lỗi cụ thể thay vì chất đống từ khóa.
- **Khác nhau theo đích**: hướng dẫn cho chatbot, prompt ảnh tĩnh hoặc prompt video Wan 2.2.

## Lưu ý

Đây là bộ tạo prompt theo quy tắc, không phải mô hình AI và không tự gọi ChatGPT/Gemini. Tab “Hướng dẫn cho AI” tạo một brief có cấu trúc để bạn dán vào chatbot; chatbot sẽ giúp biên tập sâu hơn. Kết quả trực tiếp là bản nháp để bạn tự kiểm tra trước khi đưa vào ComfyUI.

## Giấy phép

MIT — xem `LICENSE`.
