---
title : "Các dịch vụ AWS được sử dụng"
date : 2024-01-01 
weight : 4
chapter : false
pre : " <b> 5.1.4 </b> "
---

Phần này liệt kê chi tiết từng dịch vụ AWS mà SmartDocAI sử dụng, cấu hình cụ thể và lý do lựa chọn — giúp người đọc hiểu rõ vai trò của mỗi thành phần trong kiến trúc tổng thể đã trình bày ở phần trước.

### 1. Compute & API

| Dịch vụ | Cấu hình | Vai trò |
|---|---|---|
| **AWS Lambda** | functions độc lập, Package type Zip, Runtime Python 3.12, region ap-southeast-1 | Xử lý toàn bộ logic nghiệp vụ (auth, upload, RAG, vector operations) theo mô hình Microservices. Các hàm chạy độc lập, tự động scale và tối ưu chi phí. |
| **Amazon API Gateway** | REST API, tích hợp CORS và Cognito Authorizer | Cổng giao tiếp an toàn cho Frontend, định tuyến request (GET, POST, DELETE) tới các hàm Lambda tương ứng |

### 2. Storage

| Dịch vụ | Cấu hình | Vai trò |
|---|---|---|
| **Amazon S3 (Application Storage)** | Các Buckets độc lập cho Tài liệu và Frontend | Lưu trữ an toàn các file vật lý (PDF, Hình ảnh) do người dùng tải lên thông qua Pre-signed URL. Đồng thời hosting các file tĩnh của ứng dụng React SPA. |
| **Amazon S3 (Frontend Hosting)** | Tích hợp extension pgvector | Đóng vai trò là Vector Database cốt lõi của hệ thống RAG. Lưu trữ các đoạn văn bản (chunks) và vector embedding 1024 chiều, thực thi truy vấn Cosine Similarity. |
| **Amazon DynamoDB** | Các bảng Users-dev, ChatHistory-dev, Documents-dev | Cơ sở dữ liệu NoSQL tốc độ cao dùng để lưu trữ hồ sơ người dùng, siêu dữ liệu (metadata) của tài liệu và toàn bộ lịch sử các phiên trò chuyện. |

### 3. Identity & Security

| Dịch vụ | Cấu hình | Vai trò |
|---|---|---|
| **Amazon Cognito** | User Pool, cấp phát JWT Token | Quản lý danh tính, xử lý luồng đăng ký, đăng nhập và xác thực bảo mật cho người dùng. Tự động kích hoạt Lambda trigger khi người dùng xác nhận tài khoản. |
| **AWS IAM** | Custom Roles & Policies cho từng hàm Lambda | Đảm bảo nguyên tắc quyền hạn tối thiểu (Least Privilege). Cấp phép cho Lambda kết nối an toàn với S3, RDS, DynamoDB và gọi Bedrock/Textract. |

### 4. AI & Machine Learning

| Dịch vụ | Model / Chức năng | Vai trò |
|---|---|---|
| **Amazon Textract** | Trích xuất văn bản (OCR) | Tự động phân tích và bóc tách chữ từ các tài liệu phức tạp (PDF scan, hình ảnh) để chuẩn bị cho quá trình chunking. |
| **Amazon Bedrock (LLM)** | `amazon.nova-lite-v1:0` | Đóng vai trò là bộ não tổng hợp thông tin, sinh câu trả lời tự nhiên dựa trên ngữ cảnh (context) truy xuất từ RDS. |
| **Amazon Bedrock (Embeddings)** | `amazon.titan-embed-text-v2:0` (1024 chiều) | Chuyển đổi ngôn ngữ tự nhiên của tài liệu và câu hỏi thành vector số học để lập chỉ mục và tìm kiếm ngữ nghĩa. |

### 5. Event Orchestration & Delivery

| Dịch vụ | Cấu hình | Vai trò |
|---|---|---|
| **Amazon SNS** | Notification Topic liên kết với Textract | Đóng vai trò "người đưa tin" trong kiến trúc Hướng sự kiện. Nhận tín hiệu khi Textract xử lý xong file và kích hoạt hàm Lambda textract-result-dev. |
| **AWS Amplify** | Web Hosting & CI/CD | Lưu trữ và tự động triển khai (deploy) ứng dụng React Frontend trực tiếp từ mã nguồn. Tích hợp sẵn mạng phân phối CDN toàn cầu và tự động quản lý chứng chỉ bảo mật HTTPS. |

### 6. Automation & Monitoring

| Dịch vụ | Cấu hình | Vai trò |
|---|---|---|
| **Amazon CloudWatch** | CloudWatch Logs | Giám sát và ghi nhận toàn bộ log thực thi của 15 hàm Lambda, giúp kỹ sư theo dõi luồng dữ liệu và khắc phục sự cố nhanh chóng. |

### 7. Bảng tổng hợp theo tầng kiến trúc

| Tầng | Dịch vụ AWS sử dụng |
|---|---|
| **Presentation** | AWS Amplify |
| **Application & Orchestration** | API Gateway, AWS Lambda, SNS |
| **Data** | S3 (Storage), RDS PostgreSQL (pgvector), DynamoDB |
| **AI & NLP** | Amazon Bedrock, Amazon Textract |
| **Security & Identity** | Amazon Cognito, AWS IAM |
| **Monitoring** | CloudWatch SNS |
