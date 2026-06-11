## 1. Clone repo
'''Bash
git clone https://github.com/Connectivity-services-ad-PT/lab-05-HPHung1007205.git
cd lab-05-HPHung1007205
'''
## 2. Cài đặt dependencies kiểm thử
'''
Để xuất được báo cáo HTML và XML, cần cài đặt các reporter với cờ tránh xung đột phiên bản:

Bash
npm install --legacy-peer-deps
'''
## 3. Build & chạy stack Docker Compose
'''Chuẩn bị môi trường: Copy file mẫu và đảm bảo đã điền đầy đủ các biến, đặc biệt là AUTH_TOKEN.

Bash
cp .env.example .env
Khởi động hệ thống: Build images và chạy các container trong nền.

Bash
docker compose up -d --build
Các container được tạo bao gồm:

fit4110-db-lab05: PostgreSQL 15 (Lưu trữ dữ liệu IoT).

fit4110-ai-lab05: AI Service (Cổng nội bộ 9000).

fit4110-api-lab05: FastAPI Ingestion (Cổng 8000).
'''

## 4. Kiểm tra trạng thái hệ thống (Health Check)
Sau khi khởi động, đợi khoảng 10-15 giây để Database sẵn sàng, sau đó kiểm tra:

Kiểm tra API chính:

'''Bash
curl http://localhost:8000/health
'''
# Kết quả mong đợi: {"status":"ok","service":"iot-ingestion",...}
Kiểm tra trạng thái Container:

Bash
docker compose ps
# Tất cả service phải hiển thị trạng thái "Up (healthy)"
## 5. Chạy Newman End-to-End Test
Chạy kịch bản kiểm thử tự động để xác nhận luồng logic API, Auth và Database:

'''Bash
npx newman run postman/collections/FIT4110_lab05.postman_collection.json \
  -e postman/environments/FIT4110_lab05_local.postman_environment.json \
  --reporters cli,junit,html \
  --reporter-html-export reports/newman-lab05-compose.html \
  --reporter-junit-export reports/newman-lab05-compose.xml
Báo cáo sẽ được sinh ra tại:

reports/newman-lab05-compose.html (Xem trên trình duyệt).

reports/newman-lab05-compose.xml (Dành cho CI/CD).
'''

## 6. Dừng và dọn dẹp
'''Bash
# Dừng container
docker compose down

# Dừng container và xóa dữ liệu Database
docker compose down -v
'''
## 7. Mẹo gỡ lỗi (Troubleshooting)
Lỗi 401 Unauthorized: Kiểm tra xem biến AUTH_TOKEN trong file .env có khớp với Token đang dùng trong Postman Environment không.

Lỗi 500 Internal Server Error: Đảm bảo container api đã nhận được biến AUTH_TOKEN trong phần environment của docker-compose.yml.

Lỗi ENOENT (File not found): Luôn sử dụng đường dẫn tương đối từ thư mục gốc của dự án khi chạy Newman (như lệnh ở mục 5).

Sau khi lưu file này, bạn nhớ đẩy lên GitHub nhé:
Bash
git add RUN_COMPOSE.md
git commit -m "docs: update run guide with correct paths and port configurations"
git push origin main
