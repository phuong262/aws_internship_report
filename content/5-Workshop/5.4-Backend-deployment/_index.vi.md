---
title : "Triển khai Backend"
date : 2024-01-01
weight : 4
chapter : false
pre : " <b> 5.4. </b> "
---

Trong phần này, nhóm sẽ thực hiện triển khai toàn bộ hệ thống xử lý Backend và cơ sở hạ tầng dịch vụ phụ trợ (Microservices & Serverless architecture) cho ứng dụng Smart Document Chatbot trên nền tảng AWS. Quá trình triển khai bao gồm khởi tạo các dịch vụ cơ sở dữ liệu (NoSQL và Vector Database), dịch vụ lưu trữ tài liệu gốc, quản lý định danh người dùng, xây dựng REST API Gateway, triển khai logic RAG Engine qua 15 hàm AWS Lambda độc lập, và tích hợp các dịch vụ AI như Amazon Bedrock và Textract.

### Các Dịch Vụ AWS Được Sử Dụng

- **Amazon Cognito**: Quản lý đăng ký/đăng nhập người dùng, cấp phát JWT Tokens bảo mật API và tự động kích hoạt hàm Lambda để đồng bộ dữ liệu người dùng mới.
- **Amazon DynamoDB**: Cơ sở dữ liệu NoSQL tốc độ cao dùng để lưu trữ hồ sơ người dùng, siêu dữ liệu (metadata) của tài liệu và toàn bộ lịch sử các phiên trò chuyện.
- **Amazon S3**: Lưu trữ an toàn các tệp tài liệu gốc (PDF, hình ảnh) của người dùng thông qua cơ chế upload trực tiếp bằng Pre-signed URL.
- **Amazon RDS PostgreSQL**: Đóng vai trò là Vector Database cốt lõi (thông qua extension `pgvector`), lưu trữ các đoạn văn bản (chunks) và vector embedding 1024 chiều, hỗ trợ thực thi truy vấn tìm kiếm Cosine Similarity siêu tốc.
- **AWS Lambda & Amazon SNS**: Xây dựng hệ thống Backend phân tán với 15 hàm Microservices độc lập (đóng gói dạng Zip). Kết hợp kiến trúc Hướng sự kiện (Event-Driven) với SNS để tự động hóa luồng xử lý tài liệu bất đồng bộ.
- **Amazon API Gateway**: Cung cấp cổng giao tiếp REST API an toàn, tích hợp Cognito Authorizer để kiểm soát quyền truy cập và cấu hình CORS chặt chẽ cho Frontend.
- **Amazon Bedrock & Amazon Textract**: Trái tim AI của hệ thống. Textract đảm nhiệm trích xuất văn bản phức tạp (OCR), trong khi Bedrock tạo vector nhúng (Titan Embeddings V2) và sinh câu trả lời tự nhiên (Nova Lite).

---

### Nội dung thực hiện

1. [Khởi tạo Amazon Cognito User Pool](5.4.1-creating-amazon-cognito/)
2. [Khởi tạo Amazon DynamoDB](5.4.2-creating-amazon-dynamoDB/)
3. [Tạo Amazon S3 lưu trữ tài liệu thô](5.4.3-creating-amazon-S3-for-document-storage/)
4. [Khởi tạo Amazon RDS PostgreSQL & pgvector](5.4.4-creating-amazon-rds-pgvector/)
5. [Thiết lập Amazon API Gateway](5.4.5-creating-API-gateway/)
6. [Tích hợp API Gateway vào Frontend (AWS Amplify)](5.4.6-integrating-api-gateway-frontend/)