---
title : "Tích hợp API Gateway và Frontend"
weight : 6
chapter : false
pre : " <b> 5.4.6 </b> "
---

### 1. Xây dựng giao diện Frontend (React App)
Giao diện ứng dụng SmartDocAI được xây dựng trên nền tảng **React**, cung cấp các tính năng cốt lõi cho người dùng:
*   **Trang xác thực (Authentication):** Tích hợp AWS Amplify để kết nối trực tiếp với Amazon Cognito, hỗ trợ người dùng đăng nhập, đăng ký và xác thực tài khoản qua email.
*   **Trang tải tài liệu (Upload Documents):** Cho phép người dùng chọn file PDF hoặc tài liệu văn bản để hệ thống đẩy lên Amazon S3, biến đổi chuỗi và lưu trong RDS và DynamoDB thông qua các hàm Lambda xử lý tệp.
*   **Giao diện trò chuyện (Chat Interface):** Khung chat trực tuyến để người dùng gửi câu hỏi và nhận câu trả lời dựa trên nội dung tài liệu đã được xử lý RAG.

### 2. Tích hợp Amazon API Gateway vào Frontend
Amazon API Gateway đóng vai trò là điểm vào duy nhất (Single Entry Point) cho tất cả các yêu cầu từ phía client gửi lên các dịch vụ phía sau (Lambdas).

*   **Bước 1 - Cấu hình Base URL:** 
    Tại mã nguồn Frontend, nhóm thiết lập biến môi trường trỏ đến đường dẫn Endpoint của API Gateway vừa được deploy trên AWS `(https://wzie3iseyk.execute-api.ap-southeast-1.amazonaws.com/devv1)`.
*   **Bước 2 - Đính kèm Token xác thực:** 
    Ứng dụng React tự động lấy mã **JWT (JSON Web Token)** từ bộ nhớ và đính kèm vào phần Header (Authorization) của mọi API Request (gửi tin nhắn, upload file). Mã này đóng vai trò như "chìa khóa" để hệ thống backend AWS xác thực và cho phép người dùng truy cập dữ liệu.
*   **Bước 3 - Định tuyến qua API Gateway:** 
    API Gateway tiếp nhận yêu cầu, thực hiện kiểm tra tính hợp lệ của token và điều phối (route) request trực tiếp đến đúng hàm Lambda xử lý tương ứng (như `UploadFiles` hay `ChatbotRAG`).

### 3. Kết quả đạt được
Việc tích hợp thành công API Gateway giúp ứng dụng Frontend giao tiếp đồng bộ, bảo mật và tách biệt hoàn toàn với kiến trúc xử lý bên trong của hệ thống serverless.