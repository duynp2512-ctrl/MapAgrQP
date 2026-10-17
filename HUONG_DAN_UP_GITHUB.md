# HƯỚNG DẪN TẢI ỨNG DỤNG AGRIBANK LÊN GITHUB VÀ SỬ DỤNG CHUNG

**Mục tiêu:** Nhân viên mở 1 đường link HTTPS bằng điện thoại → đăng nhập → chụp/nhập X–Y → thêm hồ sơ → người khác có quyền thấy marker và tìm hồ sơ.

**QUAN TRỌNG:** GitHub chỉ lưu mã nguồn và có thể xuất bản trang HTML tĩnh. Để tài khoản, mật khẩu, hồ sơ và icon khách hàng dùng chung, **bắt buộc có một máy chủ API online với cơ sở dữ liệu lưu bền vững**. Không có máy chủ thì chỉ thấy màn hình đăng nhập và không thể đăng nhập.

## A. TẢI GÌ LÊN GITHUB?

Sau khi giải nén ZIP, bạn thấy:

```text
index.html                  # giao diện điện thoại, cần đặt Ở GỐC repo
backend/
    main.py                # máy chủ đăng nhập, lưu dữ liệu, import Excel
Dockerfile                 # Railway triển khai trực tiếp từ GitHub
requirements.txt           # thư viện máy chủ
.gitignore                 # ngăn commit dữ liệu/mật khẩu
.dockerignore              # không chép file nhạy cảm khi build
.env.example               # chỉ là file mẫu, không có mật khẩu thật
.nojekyll                  # tiện khi bật GitHub Pages
README.md
HUONG_DAN_UP_GITHUB.md
Mau_import_user.xlsx        # chỉ là bảng MẪU, không có tài khoản thật
```

**Hãy tải lên toàn bộ những file/thư mục ở trên**. Không đưa thư mục bọc `AGRIBANK_GITHUB_SAN_SANG` vào trong repo; `index.html` và `Dockerfile` phải nằm ngay trang gốc của repo.

**TUYỆT ĐỐI KHÔNG TẢI:** `.env`, `data/`, `.db`, `.sqlite`, `real_customer_data/`, Excel đã điền tài khoản thật, mật khẩu, CCCD/hồ sơ hoặc ảnh khách hàng. Nếu lỡ upload, xóa trên giao diện GitHub **không đủ** vì có thể còn lịch sử Git; phải thay đổi mọi bí mật đã lộ và xử lý lịch sử repo.

## B. TẠO REPOSITORY (KHÔNG CẦN DÙNG GIT COMMAND)

1. Vào https://github.com và đăng nhập.
2. Bấm dấu `+` ở góc trên phải → **New repository**.
3. Repository name: `agribank-vn2000-map` (ví dụ).
4. Nếu mã nguồn nghiệp vụ chưa được phê duyệt chia sẻ, **ưu tiên Private**. Lưu ý GitHub Pages trên private repo có thể phụ thuộc gói tài khoản; dùng Railway trực tiếp sẽ không cần GitHub Pages.
5. Nhấn **Create repository**.
6. Mở repo → **Add file → Upload files**; kéo thả **nội dung bên trong** thư mục đã giải nén (bao gồm cả thư mục `backend`). Đợi GitHub hiển thị đủ `index.html`, `backend/main.py`, `Dockerfile`, `requirements.txt`.
7. Điền Commit message: `Khoi tao ung dung VN2000` → nhấn **Commit changes** hoặc **Propose changes** (tùy giao diện repo).
8. Quay về trang repo, kiểm tra file `index.html` nằm ngay ở gốc, không ở trong thư mục con.

Nếu chọn nhiều file không thấy các file bắt đầu bằng dấu chấm (`.gitignore`, `.env.example`), hãy bật **View > Show > Hidden items** của Windows hoặc kéo thả file ẩn từ trình quản lý file. Nếu thiếu `.gitignore` thì thêm riêng bằng **Add file → Create new file**.

## C. TRIỂN KHAI MÁY CHỦ CÓ LƯU DỮ LIỆU — BẮT BUỘC

**Đề xuất dễ thao tác: Railway (ví dụ dịch vụ lưu trữ bên ngoài, có thể phát sinh chi phí).** Chỉ dùng dịch vụ đám mây để thử nghiệm với dữ liệu giả khi chưa được Agribank phê duyệt.

1. Vào https://railway.com và đăng nhập, kết nối tài khoản GitHub.
2. Tạo **New Project → Deploy from GitHub repo**; chọn repo `agribank-vn2000-map`. Nếu Railway yêu cầu cấp quyền truy cập repo, cấp quyền theo phạm vi tối thiểu.
3. Railway tự nhận diện `Dockerfile` ở gốc repo và xây dựng web service Python. Chờ build chạy xong.
4. Vào dịch vụ → **Variables** và thêm (điền giá trị thực ở Railway, không điền vào GitHub):

   | Tên biến | Giá trị hướng dẫn |
   | --- | --- |
   | `INITIAL_ADMIN_USERNAME` | `admin` |
   | `INITIAL_ADMIN_PASSWORD` | Mật khẩu quản trị **mới**, tối thiểu 10 ký tự, mạnh, riêng biệt |
   | `DATABASE_PATH` | `/data/agribank.db` |
   | `PRODUCTION` | `true` |
   | `VN2000_CENTRAL_MERIDIAN` | `105.5` (mặc định cho Thái Bình) |
   | `FRONTEND_ORIGINS` | `https://TEN-GITHUB.github.io` nếu bạn bật GitHub Pages; nếu chỉ dùng Railway cùng domain, để tên miền HTTPS thực tế của trang hoặc giá trị mặc định |
   | `GOOGLE_MAPS_BROWSER_KEY` | Khóa browser API giới hạn domain (có thể để trống lúc thử) |

5. **QUAN TRỌNG: Thêm persistent Volume**, vào phần **Volumes** → **Add Volume**, gắn vào dịch vụ backend với **Mount path `/data`**. Không dùng deployment tạm mà không có volume: file SQLite có thể bị xóa khi redeploy/restart. Volume/dịch vụ có thể cần trả phí. Cần sao lưu file DB định kỳ và thử khôi phục.
6. Trong dịch vụ chọn **Settings → Networking → Generate Domain** để lấy URL HTTPS, ví dụ `https://ban-do-vn2000.up.railway.app` (đây chỉ là URL ví dụ, không có máy chủ thật).
7. Thử mở `<URL-cua-ban>/api/health` bằng trình duyệt. Nếu trả `{"status":"ok"}` tức backend đã sẵn sàng phục vụ (chưa phải kiểm tra đăng nhập).
8. Mở thẳng `<URL-cua-ban>`: sẽ thấy giao diện đăng nhập. Đăng nhập `admin` với mật khẩu đã đặt ở Variables; ứng dụng yêu cầu đổi mật khẩu lần đầu.
9. Vào Quản trị → thêm user hoặc Import Excel mẫu cột A USER, B PASS, C PASS SỬA. Gửi cho nhân viên đường link HTTPS Railway; dùng chung dữ liệu từ một backend.

**Ghi chú:** Có thể không cần GitHub Pages. Nếu dùng **đường link Railway ở bước 8** thì giao diện và backend cùng địa chỉ, đơn giản nhất, ít lỗi kết nối nhất.

## D. TÙY CHỌN: ĐƯA GIAO DIỆN LÊN GITHUB PAGES

Nếu bạn muốn địa chỉ kiểu `https://TEN-GITHUB.github.io/agribank-vn2000-map/`:

1. Mở GitHub repo → **Settings → Pages**.
2. Trong **Build and deployment**, **Source = Deploy from a branch**.
3. Chọn **Branch: main**, folder **/(root)**, nhấn **Save**. Đợi GitHub xuất bản site.
4. Mở địa chỉ được GitHub Pages hiển thị.
5. Mở **⚙️ Địa chỉ máy chủ (khi dùng GitHub Pages)** tại màn hình đăng nhập → nhập URL HTTPS Railway *không có `/api`* → **Lưu địa chỉ**.
6. Thử đăng nhập. Nếu báo không kết nối, kiểm tra Railway chạy chưa và biến `FRONTEND_ORIGINS` **phải chứa đúng origin GitHub Pages** (chẳng hạn `https://TEN-GITHUB.github.io`, không thêm `/ten-repo/`). Restart/redeploy backend sau khi đổi biến.

**Tiện hơn cho nhiều điện thoại:** Có thể chia sẻ URL GH Pages kèm `?api=` như sau:

```
https://TEN-GITHUB.github.io/agribank-vn2000-map/?api=https%3A%2F%2FTEN-BACKEND.up.railway.app
```

Khi vào link này, trình duyệt tự lưu địa chỉ API của hệ thống và bỏ phần tham số khỏi thanh địa chỉ. Đây chỉ là **URL máy chủ công khai**, không được gắn mật khẩu, token hay dữ liệu CCCD vào link.

## E. BẬT MARKER TRÊN GOOGLE MAPS

Hiện tại có thể tìm hồ sơ và nhấn mở vị trí bằng Google Maps mà không cần Maps API key. Nhưng muốn nhiều **icon khách hàng** hiện đồng thời trên bản đồ kéo/zoom, cần Google Maps JavaScript API key.

1. Vào https://console.cloud.google.com/ trên project được phép của đơn vị; bật Maps JavaScript API (thường phải thiết lập thanh toán).
2. Tạo **browser key**, giới hạn website `https://YOUR-BACKEND-DOMAIN/*` và, nếu dùng GitHub Pages, `https://TEN-GITHUB.github.io/*` (tham khảo quy tắc referrer Google Cloud); giới hạn key chỉ dùng Maps JavaScript API, đặt quota/budget cảnh báo.
3. Điền key vào **Railway → Variables → GOOGLE_MAPS_BROWSER_KEY**, áp dụng deploy.
4. F5 ứng dụng. Bản đồ tương tác hiện các marker theo vùng đang xem; chạm marker xem thông tin khách hàng.

**Street View** chỉ có ở những đoạn đường Google cung cấp ảnh, không thể bảo đảm mọi thửa có ảnh.

## F. VẬN HÀNH, AN TOÀN VÀ LỖI THƯỜNG GẶP

| Hiện tượng | Kiểm tra |
| --- | --- |
| GitHub Pages có màn hình nhưng không đăng nhập | Chưa triển khai backend Railway, chưa nhập API URL hoặc cấu hình CORS sai |
| Railway báo Application error | Xem deploy logs, kiểm tra `INITIAL_ADMIN_PASSWORD`, volume `/data`, Dockerfile và PORT |
| Đăng nhập báo 401 | Sai username/mật khẩu; dùng admin khởi tạo qua biến môi trường lần đầu, không phải mật khẩu nằm trong repo |
| Thay mật khẩu trong Railway nhưng vẫn đăng nhập bằng mật khẩu cũ | Biến `INITIAL_ADMIN_PASSWORD` chỉ tạo admin khi DB **chưa có admin**; đổi sau đó phải qua ứng dụng |
| Marker không xuất hiện | Chưa có Google Maps key hợp lệ hoặc hồ sơ chưa có tọa độ lat/lon |
| Không tải được marker sau khi kéo bản đồ | Kiểm tra token, backend, `FRONTEND_ORIGINS`, console mạng/API, quota Google Maps |
| Dữ liệu biến mất sau deploy | Thiếu volume bền vững tại `/data`, cần kiểm tra sao lưu/khôi phục |

**Đây là phiên bản thử nghiệm chưa qua đánh giá an toàn thông tin, chưa mã hóa từng trường CCCD/thông tin khoản vay ở database.** Dùng thử bằng **dữ liệu giả**. Chỉ nhập dữ liệu khách hàng thật sau khi được đơn vị phê duyệt hạ tầng, xử lý thông tin cá nhân, phân quyền, nhật ký, sao lưu, mã hóa lưu trữ và đánh giá bảo mật. Không đặt thông tin khách hàng lên repo công khai kể cả khi có đăng nhập ở trang web.
