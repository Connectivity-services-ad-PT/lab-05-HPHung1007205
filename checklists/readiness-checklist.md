# Readiness Checklist – Lab 05

Đây là danh sách kiểm tra (checklist) để đảm bảo stack Docker Compose của bạn đã sẵn sàng trước khi gửi bài. Hãy tick vào mỗi mục sau khi hoàn thành.

- [x] **Database ready:** container DB đã chạy và phản hồi `pg_isready`. Kiểm tra bằng `docker exec -it fit4110-db-lab05 pg_isready -U $POSTGRES_USER`.
- [x] **AI service ready:** container AI service trả về `200` cho endpoint `/health` và `/predict` hoạt động.
- [x] **API ready:** container API trả `200` cho `/health` và có thể tạo/lấy readings khi token hợp lệ.
- [x] **Environment variables:** `.env` đã được thiết lập đúng (APP_PORT, POSTGRES_USER, AUTH_TOKEN,…). Không sử dụng secret thật; lưu secret vào `.env` cục bộ, commit `.env.example`.
- [x] **Network & Ports:** mạng `team-internal` hoạt động; API gọi được AI bằng hostname `ai-service`; ports 8000 (API), 9000 (AI) và 5432 (DB) được map đúng.
- [x] **Image tags:** bạn đã build image với tag `v0.1.0-<team>` và push lên registry (ghcr.io hoặc Docker Hub). Xác nhận rằng tag xuất hiện trong registry.

Ghi chú thêm những vấn đề gặp phải hoặc điều chỉnh tại đây:
- Thiếu thư viện mở rộng của Newman
- Sai đường dẫn và cú pháp gọi file dữ liệu Postman
- Cấu hình sai cổng Healthcheck của dịch vụ API trong Docker Compose
- Lỗi sập hệ thống 500 Internal Server Error khi test Auth
```
- Mô tả…
- Khi chạy lệnh xuất báo cáo, Newman báo lỗi đỏ không tìm thấy reporter html và xml (trong Newman bản mới XML được quản lý độc lập)
- Ban đầu, lệnh gọi nhầm file Môi trường (environment.json) vào vị trí của file Bài test (collection.json).

    Sau đó, gặp lỗi ENOENT do file bài test nằm sâu trong thư mục con postman/collections/ chứ không nằm ở thư mục gốc postman/.
- Trong file docker-compose.yml, phần kiểm tra sức khỏe (healthcheck) của dịch vụ api cấu hình nhầm sang cổng 9000 (cổng của ai-service) thay vì cổng 8000 của chính nó, khiến container hoạt động không ổn định.
- Vấn đề: Khi chạy các kịch bản kiểm tra bảo mật (bỏ trống token hoặc sai token), thay vì trả về 401 Unauthorized, API lại bị sập và trả về lỗi 500. Nguyên nhân do hàm custom exception handler http_exception_handler gọi thuộc tính không tồn tại status.HTTP_STATUS_CODES, tạo ra lỗi AttributeError nội bộ.

