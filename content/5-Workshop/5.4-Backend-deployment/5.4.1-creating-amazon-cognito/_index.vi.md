---
title : "Khởi tạo Amazon Cognito User Pool"
weight : 1
chapter : false
pre : " <b> 5.4.1 </b> "
---

**Amazon Cognito User Pool** giúp xây dựng hệ thống quản lý danh tính, xác thực người dùng và bảo mật các cổng giao tiếp API cho ứng dụng SmartDocAI.

### 1. Mục đích khởi tạo
Amazon Cognito User Pool đóng vai trò như một thư mục người dùng được quản lý hoàn toàn trên nền tảng AWS. Việc tích hợp Cognito giúp hệ thống:
*   Lưu trữ an toàn thông tin tài khoản (Email, Mật khẩu) của người dùng.
*   Phát hành mã thông báo xác thực (**JSON Web Token - JWT**) sau khi đăng nhập thành công để Frontend đính kèm vào các request gọi API.
*   Giảm tải gánh nặng tự xây dựng cơ chế mã hóa mật khẩu và xử lý phiên đăng nhập ở tầng Backend.

### 2. Các bước thực hiện cấu hình trên AWS Console

*   **Bước 1 - Khởi tạo User Pool:** 
    Tiến hành tạo mới một User Pool trên giao diện AWS Cognito. Tại đây, cấu hình phương thức đăng nhập chính được chọn là **Email**, đồng thời thiết lập các tiêu chuẩn về mật khẩu (bắt buộc có ký tự đặc biệt, chữ hoa, số và độ dài tối thiểu) nhằm đảm bảo tính bảo mật cơ bản cho tài khoản người dùng.
![Cognito User Pool](/images/5-Workshop/5.4-Backend-deployment/cognito.jpeg)
*   **Bước 2 - Cấu hình tính năng tự xác thực (Sign-up experience):** 
    Kích hoạt tính năng gửi mã xác thực qua email (Verification email) để đảm bảo tính hợp lệ của địa chỉ email khi người dùng đăng ký tài khoản mới. (như trên)
    
*   **Bước 3 - Tích hợp Lambda Trigger (User Post Confirmation):** 
    Để đồng bộ dữ liệu người dùng vừa đăng ký sang cơ sở dữ liệu hệ thống, cấu hình liên kết sự kiện `Post Confirmation` với hàm Lambda `user-post-confirmation-dev`. Ngay khi người dùng xác thực tài khoản thành công, hàm Lambda này sẽ tự động chạy để ghi nhận thông tin metadata vào bảng quản lý.

### 3. Kết quả đạt được
Sau khi hoàn tất cấu hình và tiến hành Deploy, hệ thống cung cấp các thông số định danh quan trọng gồm `User Pool ID` và `Client ID`. Các thông số này được đưa vào file cấu hình môi trường của Frontend và API Gateway, giúp hệ thống phân loại chính xác các request hợp lệ và chặn đứng hoàn toàn các truy cập trái phép chưa được xác thực.