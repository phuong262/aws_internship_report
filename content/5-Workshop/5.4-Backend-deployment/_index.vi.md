---
title : "Triển khai Backend"
date : 2024-01-01 
weight : 4 
chapter : false
pre : " <b> 5.4. </b> "
---


Trong phần này, nhóm sẽ thực hiện triển khai toàn bộ hệ thống xử lý Backend và cơ sở hạ tầng dịch vụ phụ trợ (Microservices & Serverless architecture) cho ứng dụng SmartDocAI trên nền tảng AWS. Quá trình triển khai bao gồm khởi tạo các dịch vụ cơ sở dữ liệu, dịch vụ lưu trữ tài liệu, quản lý định danh người dùng, xây dựng REST API Gateway, đóng gói và vận hành AI RAG Engine trên AWS Lambda qua Container Image, định tuyến API từ Frontend qua Amazon CloudFront, và tự động hóa toàn bộ quy trình CI/CD với AWS CodePipeline.

### Các Dịch Vụ AWS Được Sử Dụng

- **Amazon Cognito**: Quản lý đăng ký/đăng nhập người dùng, cấp phát JWT Tokens bảo mật API và liên kết Domain cho Hosted UI.
- **Amazon DynamoDB**: Lưu trữ thông tin hồ sơ người dùng mở rộng theo mô hình NoSQL On-Demand.
- **Amazon S3**: Lưu trữ tập trung tệp tài liệu gốc, chỉ mục FAISS Vector DB, ảnh đại diện Avatar và hệ thống tệp JSON Metadata phân lập theo từng người dùng.
- **AWS Lambda & Amazon ECR**: Xây dựng Engine xử lý backend tập trung (FastAPI + Mangum handler) đóng gói dạng Docker Container Image lưu trên ECR, kết hợp Lambda Trigger cho Cognito và EventBridge Cron Job tự động dọn dẹp tài khoản chưa xác minh.
- **Amazon API Gateway**: Cung cấp REST API Proxy, tích hợp Cognito Authorizer để bảo vệ tài nguyên API và cấu hình CORS Preflight cho Frontend.
- **Amazon CloudFront Integration**: Định tuyến các yêu cầu API từ ứng dụng Frontend trên CloudFront CDN tới API Gateway backend thông qua Origin và Behavior tương ứng.
- **AWS CodePipeline & CodeBuild**: Tự động hóa quy trình CI/CD từ GitHub repository, thực thi kiểm thử Linting & Unit Tests cơ chế Hard Fail, đóng gói Docker Image và cập nhật mã nguồn hàm Lambda tự động.

---

### Nội dung thực hiện
