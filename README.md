# AI Image Studio V6.4.1


## Mật khẩu trước khi tạo ảnh (Password Gate)

V6.3 yêu cầu đăng nhập bằng mật khẩu trước khi gọi `/api/generate`. Mật khẩu được kiểm tra ở backend; không đặt mật khẩu hoặc API key trong HTML/GitHub Pages.

1. Trong thư mục `backend`, chạy `npm install`.
2. Chạy `npm run hash-password` trong terminal tương tác. Nhập mật khẩu dài ít nhất 12 ký tự; ký tự không hiển thị khi gõ.
3. Sao chép chuỗi bcrypt được in ra vào biến môi trường `AI_STUDIO_PASSWORD_HASH` của dịch vụ backend. Không đưa mật khẩu thật hoặc hash vào GitHub.
4. Đặt `OPENAI_API_KEY`, `GEMINI_API_KEY` và `ALLOWED_ORIGINS` trong biến môi trường backend.
5. Deploy lại backend. Mở app, URL backend mặc định là `http://localhost:8787` (có thể thay đổi), nhập mật khẩu rồi bấm **Mở khóa (Unlock)**. Sau đó mới bấm **Tạo ảnh (Generate Image)**.

Phiên đăng nhập là token ngẫu nhiên do backend cấp, lưu trong bộ nhớ của tab, hết hạn sau 8 giờ và có thể khóa thủ công. Khởi động lại backend sẽ xóa các phiên đang hoạt động. Backend giới hạn 5 lần đăng nhập sai mỗi địa chỉ IP trong cửa sổ 15 phút.

**Quan trọng:** Đây là một cổng mật khẩu dùng chung (shared password), không phải hệ thống tài khoản cá nhân. Khi triển khai công khai, vẫn nên dùng HTTPS, đặt giới hạn chi phí/lượt gọi ở nhà cung cấp, cấu hình CORS đúng domain và cân nhắc thêm giới hạn sử dụng. CORS không phải cơ chế xác thực.

Giao diện tiếng Việt kèm thuật ngữ tiếng Anh trong ngoặc. Frontend tĩnh ở `index.html` (hoặc `public/index.html`) có thể đưa lên GitHub Pages; backend Node.js riêng giữ API key và gọi OpenAI/Gemini.

## Tính năng
- Chọn nhà cung cấp: OpenAI API hoặc Gemini API.
- Chọn model, tỷ lệ khung hình và chế độ độ phân giải cao nhất được hỗ trợ.
- Tải tối đa 4 ảnh tham chiếu (PNG/JPEG/WebP, 10 MB/ảnh), xem trước và chọn mục đích: Identity, Outfit, Hairstyle, Pose, Style, Composition.
- Nút tạo ảnh thật, xem ảnh kết quả và tải dữ liệu ảnh trả về từ API (không phải ảnh preview).
- API keys chỉ ở backend, không nằm trong HTML/JavaScript/GitHub Pages.
- CORS giới hạn theo `ALLOWED_ORIGINS`.

## Kiến trúc
- `public/index.html`: frontend static, deploy GitHub Pages.
- `backend/server.js`: backend Node.js, deploy lên Render hoặc dịch vụ Node tương đương.
- `backend/.env.example`: mẫu biến môi trường. Không bao giờ commit `.env` có key thật.

## A. Deploy frontend lên GitHub Pages
1. Tạo repo GitHub mới.
2. Upload nội dung thư mục `public/` vào root repo (hoặc chỉnh GitHub Pages source trỏ vào `/public` nếu repo giữ nguyên cấu trúc).
3. Settings → Pages → Deploy from a branch → chọn `main` và `/ (root)` nếu `index.html` ở root.
4. Ghi lại URL Pages, ví dụ `https://TEN-USER.github.io/TEN-REPO/`.

**Lưu ý:** Nếu repo root chứa project đầy đủ, hãy copy `public/index.html` ra root hoặc cấu hình Pages dùng `/public`. Cách đơn giản nhất là đưa nội dung `public/` vào root repo frontend riêng.

## B. Deploy backend trên Render
1. Tạo Web Service mới từ repo project.
2. Root Directory: `backend`.
3. Runtime: Node.
4. Build Command: `npm install`.
5. Start Command: `npm start`.
6. Environment Variables:
   - `OPENAI_API_KEY`: API key từ OpenAI Platform (nếu dùng OpenAI).
   - `GEMINI_API_KEY`: API key từ Google AI Studio (nếu dùng Gemini).
   - `ALLOWED_ORIGINS`: URL chính xác của GitHub Pages, ví dụ `https://TEN-USER.github.io`. Nếu muốn siết chặt hơn, sửa CORS middleware để chỉ cho phép đúng origin Pages; origin không bao gồm đường dẫn repo.
   - `PORT`: để Render tự cấp, không cần tự đặt.
7. Sau khi deploy, kiểm tra `https://BACKEND-URL/api/health`. Kết quả cho biết service chạy và API key đã cấu hình hay chưa (chỉ trả boolean, không tiết lộ key).
8. Mở trang GitHub Pages, nhập `https://BACKEND-URL` vào ô Backend URL.

## C. Chạy thử backend trên Windows
Cài Node.js 20 trở lên. Mở Terminal/PowerShell trong thư mục `backend`:
```powershell
npm install
Copy-Item .env.example .env
notepad .env
npm start
```
Điền key thật vào `.env` trên máy local. Mở `http://localhost:8787/api/health`. Để thử frontend cục bộ, chạy một static server từ thư mục `public` (ví dụ VS Code Live Server) và thêm origin đó vào `ALLOWED_ORIGINS`.

## Model và chất lượng
- Gemini mặc định chọn `gemini-nano-banana-2.1`, yêu cầu 4K ở chế độ `maximum`; Gemini chỉ xuất được kích thước/tỷ lệ được model hỗ trợ.
- OpenAI mặc định chọn `gpt-image-2.5-sunburst`, yêu cầu `quality=max`, PNG. Model OpenAI có thể có giới hạn kích thước và tính năng tùy tài khoản/model.
- Chọn 1K/2K/4K sẽ gửi yêu cầu tương ứng nếu có thể. Kích thước thực tế phải được xác minh từ ảnh trả về; UI hiện phiên bản này ghi lại kích thước yêu cầu, chưa tự đọc pixel dimensions.
- API tính phí riêng; bật billing và kiểm tra giá model trước khi dùng thường xuyên.

## Ảnh tham chiếu và riêng tư
- Ảnh được gửi từ trình duyệt đến backend rồi đến nhà cung cấp API mà người dùng đã chọn.
- Backend giữ file ảnh trong bộ nhớ trong quá trình request, không lưu vào ổ đĩa.
- Không dùng ảnh mà bạn không có quyền sử dụng; tránh tải ảnh nhạy cảm.
- API provider có thể xử lý/lưu dữ liệu theo điều khoản và cài đặt của họ.

## Giới hạn bản V6
- Đây là bản triển khai đầu tiên, không có đăng nhập người dùng, giới hạn sử dụng theo tài khoản hoặc thanh toán nội bộ. Không public backend rộng rãi với API key cá nhân nếu chưa thêm xác thực/rate limiting.
- `ALLOWED_ORIGINS` chỉ là lớp bảo vệ CORS, không phải xác thực; người khác vẫn có thể gọi backend bằng công cụ riêng. Trước khi public, nên thêm bearer token/app login và rate limiting.
- OpenAI edit endpoint/model support for multiple reference files may vary by model; if API returns an error, thử một ảnh tham chiếu hoặc dùng model được tài khoản hỗ trợ.
- Gemini model availability and output options may vary by region/account/API version. Kiểm tra lỗi trả về nếu model không bật cho API key.


## Chạy thử backend (Local Test)

```bash
cd backend
npm install
npm run hash-password
```

Sau khi đã cấu hình các biến môi trường trong file `.env` (tạo từ `.env.example`), chạy:

```bash
npm start
```

Backend mặc định chạy tại `http://localhost:8787`. Mở `index.html` bằng một local static server hoặc deploy lên GitHub Pages, nhập địa chỉ backend và mật khẩu AI Studio. Để triển khai công khai, dùng dịch vụ backend có HTTPS và nhập biến môi trường trong phần cài đặt của dịch vụ, không upload file `.env`.

## GitHub Pages

Đưa `index.html` ở thư mục gốc lên repository. Trong **Settings → Pages**, chọn branch và thư mục root. Đặt `ALLOWED_ORIGINS` ở backend đúng origin GitHub Pages của repository (ví dụ `https://username.github.io` hoặc URL trang project tương ứng). Không dùng `*` cho site công khai.


## Nội dung mặc định của prompt (Default Prompt Structure)
V6.3 điền sẵn các trường Subject, Identity & Appearance, Action & Physical Interaction, Environment, Narrative Moment, Composition & Camera, Lighting, Visual Style, Material & Physical Realism, Color & Mood, Negative Constraints và Additional Notes. Ô Final Prompt cũng được tạo sẵn khi mở trang; người dùng có thể chỉnh sửa từng trường hoặc xóa nội dung không cần thiết.


## Ngôn ngữ mặc định (Default Language)
V6.3 đặt Tiếng Việt làm ngôn ngữ mặc định. Các nội dung mẫu và chữ gợi ý trong ô nhập được viết bằng tiếng Việt. Chỉ khi người dùng chọn Tiếng Anh hoặc Tiếng Anh kèm bản dịch tiếng Việt trong menu Ngôn ngữ prompt (Prompt Language), app mới tạo prompt theo lựa chọn đó.

V6.3 bổ sung: khi chọn Tiếng Việt, tiêu đề các mục và nhãn lựa chọn trong prompt được xuất bằng tiếng Việt; chọn Tiếng Anh mới xuất prompt tiếng Anh. Chế độ song ngữ tạo cả hai phiên bản.


## V6.4 — Ví dụ cụ thể trong mọi ô prompt

- Thay các câu hướng dẫn chung bằng nội dung ví dụ cụ thể về cô gái Việt Nam mặc áo dài trắng, cầm nón lá và dùng điện thoại trên ghế đá sân trường.
- Cập nhật chủ thể, ngoại hình, hành động, bối cảnh, khoảnh khắc, lớp không gian, tính chân thực, điều cần tránh, ghi chú bổ sung và ghi chú ảnh tham chiếu.
- Prompt cuối cùng vẫn được tạo từ các trường mặc định khi mở ứng dụng.
- Giữ nguyên các tính năng V6.3: menu, chọn ngôn ngữ prompt, ảnh tham chiếu, tạo ảnh qua backend, tải ảnh và đăng nhập bằng mật khẩu.


## V6.4.1 — Backend URL mặc định
- Ô Backend URL được điền sẵn `http://localhost:8787` để chạy backend trên chính máy đang mở trình duyệt.
- Vẫn giữ ô này có thể chỉnh sửa để dùng backend online; không thay đổi API key, xác thực mật khẩu hoặc tính năng tạo ảnh.
