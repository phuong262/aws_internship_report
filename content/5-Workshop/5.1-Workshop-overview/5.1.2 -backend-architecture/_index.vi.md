---
title : "Kiến trúc Backend"
date : 2024-01-01 
weight : 2
chapter : false
pre : " <b> 5.1.2 </b> "
mermaid: true
---

### 1. Giải thích cấu trúc thư mục Backend
Hệ thống backend không sử dụng kiến trúc nguyên khối (Monolithic) mà áp dụng chuẩn Serverless Microservices trên AWS. Toàn bộ logic nghiệp vụ được phân tách thành các hàm AWS Lambda độc lập, kết nối với nhau thông qua cơ chế Event-Driven (Hướng sự kiện).

#### 1.1. Cấu trúc tổng quan

![](/images/5-Workshop/5.1-Workshop-overview/ImageFunction.png)
---

#### 1.2. Giải thích chi tiết từng hàm

#### Phân hệ Xử lý Tài liệu (Document Ingestion Pipeline)
Phân hệ này hoạt động hoàn toàn bất đồng bộ (Asynchronous), giúp Frontend không bị treo khi xử lý các file PDF/Hình ảnh lớn.

* **`upload-files`**: Đứng sau API Gateway. Nhận danh sách tên file từ Frontend, sinh ra mã định danh (`document_id`) và trả về mảng các Pre-signed URLs. Frontend sẽ sử dụng các link này để đẩy file trực tiếp lên Amazon S3, giảm tải tối đa bằng thông cho API Gateway (vốn bị giới hạn payload 10MB).
* **`textract-start`**: Hàm Lambda được kích hoạt tự động (S3 Event Trigger) ngay khi file vừa hạ cánh thành công xuống S3. Khởi tạo bản ghi trạng thái tài liệu vào Amazon DynamoDB và gọi Amazon Textract để bắt đầu đọc chữ.
* **`textract-result`**: Nhận tín hiệu (thông qua SNS) khi Textract hoàn thành. Lấy kết quả văn bản thô, tiến hành thuật toán Chunking thông minh (cắt đoạn văn bản theo độ dài quy định mà không làm đứt gãy ngữ cảnh). Gửi các chunk này sang phân hệ Vector.

---

#### Phân hệ Quản lý Vector (Vector Operations & RDS pgvector)
Đảm nhiệm việc biến đổi ngôn ngữ tự nhiên thành mảng số (Vector) và lưu trữ chuyên dụng để tối ưu tốc độ tìm kiếm.

* **`create-vector`**: Giao tiếp với dịch vụ Amazon Bedrock (sử dụng mô hình `amazon.titan-embed-text-v2:0`) để chuyển đổi từng chunk văn bản thành vector embedding 1024 chiều.
* **`rds-vector-insert`**: Kết nối tới Amazon RDS PostgreSQL thông qua thư viện thuẩn Python `pg8000`. Lưu trữ nội dung chunk, metadata (tên file, số trang) và trường vector vào bảng `document_chunks`.
* **`rds-vector-search`**: Thực thi truy vấn SQL tính toán độ tương đồng giữa vector của Câu hỏi và kho vector trong bảng `document_chunks`. Hỗ trợ lọc theo `user_id` để đảm bảo tính riêng tư của dữ liệu đa người dùng (Multi-tenancy).

---

#### Phân hệ Hỏi đáp (Chat & RAG Domain)
Quản lý tương tác thời gian thực với người dùng và điều phối các hệ thống phụ trợ.

* **`chat-get-history` (API Get Handler)**: Kết hợp việc truy vấn hai nguồn dữ liệu: Lấy danh sách tài liệu từ bảng Documents (để Frontend hiển thị panel bên trái) và lấy lịch sử tin nhắn của `session_id` hiện tại từ bảng ChatHistory (để hiển thị khung hội thoại bên phải).
* **`chatbot-rag`**: Trái tim của hệ thống RAG. 
  * Bước 1: Nhận câu hỏi, gọi Bedrock tạo Vector. 
  * Bước 2: Gọi hàm `rds-vector-search` (thông qua Boto3 Invoke) để lấy Top-K chunks liên quan nhất. 
  * Bước 3: Đóng gói các chunks này vào Prompt (khu vực `<context>`). 
  * Bước 4: Gửi Prompt cho mô hình ngôn ngữ lớn trên Bedrock sinh câu trả lời tự nhiên. 
  * Bước 5: Lưu toàn bộ giao dịch vào DynamoDB kèm theo thông tin tham chiếu.

---

### 2. Luồng dữ liệu và Kiến trúc Backend

{{< mermaid >}}
graph TD
%% Khối Frontend
Client["Client (React Frontend)"]

%% Khối API & Auth
APIGW["AWS API Gateway (/api)"]
Cognito["Amazon Cognito"]
Client -->|Xác thực| Cognito
Client -->|REST API Calls| APIGW

%% Luồng 1: RAG Chat
subgraph Luong1 [Luồng Hỏi Đáp Synchronous]
  APIGW -->|POST /chat| ChatbotRAG["Lambda: chatbot-rag"]
  ChatbotRAG -->|1. Tạo Vector Câu hỏi| Bedrock1["Amazon Bedrock (Titan Embed)"]
  ChatbotRAG -->|2. Invoke Lambda VectorSearch| VectorSearch["Lambda: rds-vector-search"]
  VectorSearch -->|3. Tìm kiếm Cosine| RDS[("Amazon RDS PostgreSQL<br/>(pgvector)")]
  ChatbotRAG -->|4. Sinh câu trả lời| Bedrock2["Amazon Bedrock (LLM)"]
  ChatbotRAG -->|5. Lưu Lịch sử Chat| DynamoChat[("DynamoDB<br/>(ChatHistory)")]
end

%% Luồng 2: Upload File
subgraph Luong2 [Luồng Xử lý Dữ liệu thô Asynchronous]
  Client -. "PUT File trực tiếp" .-> S3Bucket[("Amazon S3 Bucket")]
  S3Bucket -->|S3 Event Trigger| TextractStart["Lambda: textract-start"]
  TextractStart -->|Khởi chạy Textract| Textract["Amazon Textract"]
  Textract -->|SNS Notification| TextractResult["Lambda: textract-result"]
  TextractResult -->|Cắt Chunk & Invoke| CreateVector["Lambda: create-vector"]
  CreateVector --> Bedrock1
  CreateVector -->|Invoke| VectorInsert["Lambda: rds-vector-insert"]
  VectorInsert --> RDS
end
{{< /mermaid >}}
<script src="https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js"></script>
<script>mermaid.initialize({startOnLoad:true});</script>
---

### 3. Tương tác với Hạ tầng AWS (AWS Services Summary)

| Dịch vụ AWS | Mục đích & Vai trò trong Backend |
| :--- | :--- |
| **AWS Lambda** | Môi trường thực thi Serverless phân tán. Các hàm độc lập giảm rủi ro gián đoạn toàn hệ thống. |
| **Amazon Bedrock** | Dịch vụ AI tổng hợp đóng vai trò sinh câu trả lời (LLM) và tạo vector nhúng (Amazon Titan Embeddings V2). |
| **AWS Cognito** | Quản lý đăng ký, xác thực người dùng. |
| **Amazon S3** | Lưu trữ file tài liệu gốc (.pdf, ảnh). |
| **Amazon DynamoDB** | Cơ sở dữ liệu NoSQL chuyên dụng lưu trữ Lịch sử Hội thoại và Metadata của người dùng với tốc độ đọc ghi cực nhanh. |
| **Amazon RDS PostgreSQL** | Cơ sở dữ liệu chứa extension pgvector cực kỳ mạnh mẽ để lập chỉ mục và tìm kiếm ngữ nghĩa siêu tốc. |
| **AWS API Gateway** | Cổng giao tiếp REST API, tích hợp cơ chế bảo vệ CORS và xác thực Token trước khi gọi Lambda. |
| **Amazon Textract** | Dịch vụ thị giác máy tính OCR. Đọc chữ từ các định dạng khó như Hình ảnh hoặc PDF Scan. |
