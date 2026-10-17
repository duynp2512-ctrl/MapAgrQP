# Agribank Quỳnh Phụ — Bản đồ tọa độ VN-2000 & quản lý hồ sơ (bản mẫu)

> **DÙNG THỬ VỚI DỮ LIỆU GIẢ.** Chưa được thẩm định an toàn thông tin hoặc phê duyệt để lưu dữ liệu khách hàng Agribank trên cloud công cộng.

**BẮT ĐẦU TẠI [HUONG_DAN_UP_GITHUB.md](HUONG_DAN_UP_GITHUB.md)** – Hướng dẫn tải lên GitHub từng bước, cấu hình máy chủ online và bản đồ Google.

## Kiến trúc

- `index.html` — giao diện điện thoại, có thể chạy trên **GitHub Pages** (chỉ phần hiển thị).
- `backend/main.py` + `requirements.txt` — API FastAPI: đăng nhập, mật khẩu Argon2id, quyền sửa, import Excel, tra cứu, báo cáo.
- `Dockerfile` — dựng web service từ GitHub trên Railway hoặc máy chủ Docker tương đương.
- **SQLite nằm ở ổ lưu dữ liệu bền vững `/data/agribank.db` trên máy chủ** – không nằm trong GitHub.

### Có hai kiểu truy cập

**Đề xuất dễ nhất:** Chỉ cần upload code lên GitHub, dùng Railway triển khai cả app (giao diện + API) từ repo; chia sẻ URL HTTPS Railway. Không cần bật GitHub Pages.

**Tùy chọn:** Bật GitHub Pages cho `index.html`, kết nối tới địa chỉ API Railway qua mục *Địa chỉ máy chủ* khi đăng nhập hoặc URL có `?api=...`. Hai trang truy cập chung một cơ sở dữ liệu.

## Bảo mật

Không đưa `.env`, mật khẩu, file Excel đã điền tài khoản hoặc bất kỳ hồ sơ, ảnh, CCCD, dữ liệu khách hàng thật lên repo. Dùng tài khoản riêng, đặt mật khẩu quản trị trong **biến môi trường của máy chủ**, không viết vào mã HTML. Tài liệu và mã nguồn là bản mẫu chưa được đánh giá xâm nhập. Cần phê duyệt trước khi dùng dữ liệu thật.
