# 🛍️ Hệ thống Microservices Quản lý Bán Hàng (EProject - Phase 1)

Dự án mô phỏng một **hệ thống thương mại điện tử cơ bản** được phát triển theo kiến trúc **Microservices**, sử dụng **Node.js**, **Express**, **MongoDB**, và được **container hóa bằng Docker**.  
🎯 **Mục tiêu:** Giúp sinh viên làm quen với cách xây dựng và triển khai ứng dụng phân tán trong thực tế.

---

## 🧩 1. Kiến trúc hệ thống

Hệ thống bao gồm nhiều **service độc lập**, mỗi service chịu trách nhiệm cho một phần nghiệp vụ riêng biệt:

- **API Gateway:** Cổng vào duy nhất của toàn hệ thống. Nhận request từ client và điều hướng đến service tương ứng.  
- **Authentication Service:** Quản lý đăng ký, đăng nhập, xác thực người dùng qua **JWT (JSON Web Token)**.  
- **Product Service:** Cung cấp chức năng CRUD sản phẩm, quản lý tồn kho và giá bán.  
- **Order Service:** Xử lý việc tạo và quản lý đơn hàng, liên kết với các service khác thông qua **RabbitMQ**.

---

### 🔗 Giao tiếp giữa các service

- **Đồng bộ (Synchronous):** Thông qua các API RESTful, tất cả request đều đi qua **API Gateway**.  
- **Bất đồng bộ (Asynchronous):** Các service giao tiếp nội bộ qua **RabbitMQ**, ví dụ:  
  Khi **Order Service** tạo đơn hàng mới, nó gửi thông báo cho **Product Service** để cập nhật tồn kho.

---

### 🗄️ Cơ sở dữ liệu

Tuân thủ nguyên tắc **“Database per Service”** — mỗi service có một cơ sở dữ liệu riêng (container MongoDB riêng biệt), giúp đảm bảo tính độc lập và tách biệt dữ liệu.

---

## ⚙️ 2. Công nghệ sử dụng

| Thành phần | Công nghệ |
|-------------|-----------|
| **Backend** | Node.js, Express.js |
| **Database** | MongoDB (Mongoose ODM) |
| **Message Broker** | RabbitMQ (amqplib) |
| **Containerization** | Docker, Docker Compose |
| **Testing** | Mocha, Chai, Chai-Http |

---

## 🗂️ 3. Cấu trúc thư mục

```bash
.
├── api-gateway/         # API Gateway
├── auth/                # Authentication Service
├── order/               # Order Service
├── product/             # Product Service
├── .env                 # File môi trường chính (RabbitMQ)
├── .gitignore           # Bỏ qua các file không cần commit
├── docker-compose.yml   # Định nghĩa & kết nối các container
└── README.md            # Tài liệu mô tả dự án

🧱 4. Hướng dẫn cài đặt và khởi chạy
📋 Yêu cầu trước khi bắt đầu

Cần cài đặt:

Docker Desktop

Docker Compose

Git

🔹 Bước 1: Clone dự án về máy
git clone <your-repository-url>
cd EProject-Phase-1

🔹 Bước 2: Tạo file .env cho từng service

Các file .env giúp lưu trữ thông tin môi trường (Port, JWT, MongoDB URI, RabbitMQ,…).

📁 File .env tại thư mục gốc
```bash
RABBITMQ_USER=myuser
RABBITMQ_PASS=mypassword

📁 File auth/.env
```bash
PORT=3000
MONGODB_URI=mongodb://mongo-auth:27017/authdb
JWT_SECRET=supersecretkey

📁 File product/.env
```bash
PORT=3001
MONGODB_URI=mongodb://mongo-product:27017/productdb
RABBITMQ_URI=amqp://myuser:mypassword@rabbitmq:5672
JWT_SECRET=supersecretkey

📁 File order/.env
```bash
PORT=3002
MONGODB_URI=mongodb://mongo-order:27017/orderdb
RABBITMQ_URI=amqp://myuser:mypassword@rabbitmq:5672
JWT_SECRET=supersecretkey

🔹 Bước 3: Khởi chạy toàn bộ hệ thống bằng Docker Compose
docker compose up --build -d


Giải thích:

--build: Tự động build lại image nếu có thay đổi.

-d: Chạy container ở chế độ nền (detached mode).

🔹 Bước 4: Kiểm tra trạng thái container
docker compose ps


Kết quả mong đợi:
Khoảng 8 container ở trạng thái Up (3 service chính, 3 MongoDB, 1 RabbitMQ, 1 API Gateway).

🌐 5. Cách sử dụng & kiểm thử API
📍 API Gateway

Tất cả request được gửi qua API Gateway tại:

http://localhost:3003

📊 Danh sách Endpoint
Nhóm chức năng	Phương thức	Endpoint	Xác thực
Authentication	POST	/auth/register	❌
	POST	/auth/login	❌
Product	GET	/products	✅ Bearer Token
	POST	/products	✅ Bearer Token
Order	POST	/orders	✅ Bearer Token
	GET	/orders	✅ Bearer Token
🔹 Bước 5: Test các API bằng Postman

Mở Postman

Gửi request đến http://localhost:3003/auth/register để đăng ký tài khoản

Đăng nhập qua http://localhost:3003/auth/login để nhận JWT Token

Dùng Token đó để truy cập các API có bảo mật (ví dụ: /products, /orders)

💡 Hãy đảm bảo RabbitMQ và MongoDB đang chạy trước khi test.

🧪 6. Chạy kiểm thử (Testing)
🔹 Bước 6: Chạy test cho từng service riêng lẻ

Ví dụ kiểm thử service auth:

cd auth
npm test


Giải thích:

cd auth: Di chuyển đến thư mục service cần test.

npm test: Chạy toàn bộ file test (dùng Mocha + Chai).

⚠️ Trước khi test, đảm bảo các container phụ thuộc (MongoDB, RabbitMQ) đã chạy.

🧾 7. Ghi chú quan trọng
🚫 Không commit các file sau vào GitHub
.env
node_modules
.DS_Store

🧱 Gợi ý thêm

Cập nhật README.md nếu có thay đổi cấu trúc hoặc cổng service.

Khi push lên GitHub:

git add .
git commit -m "Update project setup and README"
git push

✍️ Tác giả

Võ Ngọc Toàn
🎓 Sinh viên Trường Đại học Công Nghiệp TP. Hồ Chí Minh
📘 Dự án EProject - Phase 1: Hệ thống Microservices Quản lý Bán Hàng