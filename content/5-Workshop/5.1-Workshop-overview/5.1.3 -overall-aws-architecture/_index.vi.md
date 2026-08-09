---
title : "Kiến trúc tổng thể trên AWS"
date : 2024-01-01 
weight : 3
chapter : false
pre : " <b> 5.1.3 </b> "
---

Sau khi tìm hiểu riêng kiến trúc Frontend và Backend ở 2 phần trước, phần này tổng hợp lại thành **bức tranh toàn cảnh** cách các thành phần AWS phối hợp với nhau để tạo thành hệ thống SmartDocAI hoàn chỉnh, cùng với cấu trúc lưu trữ dữ liệu per-user.

#### Kiến trúc tổng thể trên AWS

Toàn bộ hệ thống được triển khai theo mô hình **serverless**, chia thành 4 lớp (khối) chức năng như trong sơ đồ kiến trúc:

![](/images/5-Workshop/5.1-Workshop-overview/Structure.jpeg)

##### a. Lớp Frontend & API

- **User**: Người dùng cuối truy cập ứng dụng qua trình duyệt.
- **AWS Amplify**: Hosting và tự động deploy giao diện React, tích hợp CDN + chứng chỉ HTTPS.
- **Amazon Cognito**: Quản lý đăng ký/đăng nhập/xác thực người dùng, phát hành JWT (idToken/accessToken).
- **API Gateway**: Cổng vào duy nhất (REST API `/api`), xác thực request bằng Cognito Authorizer trước khi chuyển tiếp xuống các Lambda tương ứng ở lớp kế tiếp.

##### b. Lớp Cổng giao tiếp & Điều phối (Lambdas)

Đây là lớp business logic, gồm các Lambda **không nằm trong VPC** (trừ các Lambda thao tác trực tiếp với RDS ở lớp VPC riêng) để tránh chi phí NAT Gateway. Mỗi Lambda đảm nhiệm một nghiệp vụ riêng biệt (single responsibility):

| Lambda | Trigger | Vai trò |
|---|---|---|
| `user-post-confirm` | Cognito Post Confirmation trigger | Chạy tự động ngay sau khi user xác thực đăng ký thành công (ví dụ: khởi tạo hồ sơ user trong DynamoDB). |
| `UploadFiles` | API Gateway (`POST /upload`) | Sinh presigned URL để client `PUT` file trực tiếp lên **Amazon S3**. |
| `textract-start` | API Gateway hoặc S3 Event | Khởi chạy job `StartDocumentTextDetection` (Textract) ở chế độ bất đồng bộ. |
| `textract-result` | **SNS Notification** (Textract báo hoàn tất) | Lấy toàn bộ văn bản đã OCR, cắt chunk, gọi tiếp `create-vector`. |
| `create-vector` | Invoke từ `textract-result` | Gửi từng chunk đến **Bedrock (embedding)**, sau đó invoke `vector-insert`. |
| `ChatbotRAG` | API Gateway (`POST /chat`) | Điều phối luồng hỏi-đáp: embed câu hỏi → gọi `vector-search` → gọi **Bedrock (LLM)** sinh câu trả lời → ghi lịch sử vào **DynamoDB**. |
| `chat-get-history` | API Gateway (`GET /chat-history`) | Truy vấn lịch sử hội thoại từ DynamoDB. |
| `delete-session` | API Gateway (`DELETE /session`) | Xóa phiên chat (và dữ liệu liên quan). |
| `delete-document` | API Gateway (`DELETE /document`) | Xóa tài liệu (S3, metadata, vector liên quan). |
| `api-get-handle` | API Gateway (`GET ...`) | Lambda dùng chung xử lý các thao tác đọc (get) khác, có ghi/đọc thêm DynamoDB. |

##### c. Lớp Lưu trữ & AI (AWS Services)

- **Amazon S3**: Lưu trữ file gốc người dùng tải lên (ảnh/PDF).
- **Amazon Textract**: Trích xuất văn bản từ tài liệu (OCR), báo kết quả qua SNS khi hoàn tất.
- **Amazon Bedrock (Embedding — Titan Embed)**: Chuyển văn bản (chunk hoặc câu hỏi) thành vector.
- **Amazon Bedrock (LLM)**: Sinh câu trả lời cuối cùng dựa trên câu hỏi + ngữ cảnh truy xuất được.
- **Amazon DynamoDB**: Lưu lịch sử hội thoại (chat history) và metadata liên quan.

##### d. Lớp AWS VPC – Private Subnet

Các Lambda cần truy cập trực tiếp **Amazon RDS PostgreSQL (pgvector)** — vốn được đặt trong subnet riêng tư không có địa chỉ public — bắt buộc phải chạy trong VPC:

- **`vector-insert`**: Nhận vector embedding từ `create-vector`, ghi (insert) vào RDS.
- **`vector-search`**: Nhận vector câu hỏi từ `ChatbotRAG`, thực hiện truy vấn tương đồng cosine trên RDS, trả về các đoạn (chunk) liên quan nhất.
- **`rds-int`**: Lambda khởi tạo/bảo trì schema cho RDS (ví dụ tạo bảng, bật extension `pgvector`).

Việc tách riêng các Lambda này vào VPC — thay vì đưa toàn bộ hệ thống vào VPC — giúp:
1. Chỉ những thành phần thực sự cần truy cập RDS mới tốn thời gian khởi tạo ENI/ra vào VPC.
2. Các Lambda còn lại (điều phối, gọi Bedrock, gọi DynamoDB, gọi S3/Textract) không cần NAT Gateway vì các dịch vụ này đều có endpoint public/API riêng, giúp **tiết kiệm chi phí NAT Gateway** đáng kể.

##### e. Tổng hợp luồng dữ liệu chính

- **Luồng xác thực**: `User → Cognito → API Gateway` (đính kèm JWT ở mọi request tiếp theo).
- **Luồng nạp tài liệu (ingest)**: `UploadFiles → S3 → textract-start → Textract → (SNS) → textract-result → create-vector → Bedrock (embed) → vector-insert → RDS (pgvector)`.
- **Luồng hỏi đáp (RAG)**: `ChatbotRAG → Bedrock (embed câu hỏi) → vector-search → RDS (tìm kiếm cosine) → ChatbotRAG → Bedrock (LLM sinh câu trả lời) → DynamoDB (lưu lịch sử) → trả kết quả về client`.
- **Luồng quản lý**: `delete-session / delete-document / chat-get-history / api-get-handle` thao tác trực tiếp với DynamoDB (và gián tiếp dọn dữ liệu liên quan ở S3/RDS khi xóa tài liệu).

> **Ghi chú thiết kế**: Kiến trúc tuân thủ nguyên tắc mỗi Lambda chỉ đảm nhiệm một nghiệp vụ (single-purpose function), giúp dễ scale độc lập, dễ debug qua CloudWatch theo từng hàm, và giảm blast-radius khi một thành phần gặp lỗi.