---
title : "Tổng quan"
date : 2024-01-01 
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

## Bài toán

Hệ thống Document Chatbot cho phép người dùng đặt câu hỏi về nội dung tài liệu. Để thực hiện tìm kiếm theo ngữ nghĩa, hệ thống cần lưu nội dung tài liệu dưới dạng các đoạn nhỏ và vector embedding. Hệ thống cũng cần lưu lịch sử hỏi đáp để người dùng có thể xem lại cuộc trò chuyện.

## Phạm vi thực hiện

Phần thực hành trong Workshop gồm:

- Khởi tạo RDS PostgreSQL và cấu hình mạng.
- Tạo VPC, Subnet và Security Group để kiểm soát kết nối giữa Lambda và RDS.
- Lưu thông tin đăng nhập cơ sở dữ liệu trong AWS Secrets Manager.
- Kích hoạt `pgvector`.
- Tạo bảng lưu tài liệu và vector.
- Tạo DynamoDB lưu lịch sử chat.
- Xây dựng Lambda Create, Get, Update và Delete.
- Xây dựng Lambda thêm và tìm kiếm vector.
- Gọi Titan Embeddings V2 để tạo vector 1.024 chiều.
- Bơm dữ liệu thử nghiệm và đo hiệu năng.

## Kiến trúc lớp dữ liệu

```text
AWS Lambda (gắn vào VPC)
├── Security Group ── cổng 5432 ──> Amazon RDS PostgreSQL
│                                  ├── documents
│                                  └── document_chunks (vector(1024))
├── AWS Secrets Manager ──> thông tin kết nối RDS
├── Amazon DynamoDB ──────> ChatHistory-dev
└── Amazon Bedrock ───────> Titan Embeddings V2
```

VPC và Security Group tạo ranh giới mạng cho phần cơ sở dữ liệu. Lambda không lưu mật khẩu trực tiếp trong mã nguồn mà lấy thông tin kết nối từ Secrets Manager khi thực thi. Sau đó Lambda kết nối tới RDS để xử lý vector, DynamoDB để xử lý lịch sử chat và Bedrock để tạo embedding.

## Các dịch vụ AWS được sử dụng và lý do lựa chọn

### Amazon VPC và Security Group

**Vai trò trong dự án:** VPC tạo môi trường mạng riêng cho RDS và Lambda. Security Group hoạt động như tường lửa, chỉ cho phép Security Group của Lambda kết nối tới PostgreSQL qua cổng `5432`. Nhờ đó cơ sở dữ liệu không cần mở cổng cho toàn bộ Internet.

**Tại sao lựa chọn:** RDS là tài nguyên chứa dữ liệu quan trọng nên cần kiểm soát đường truyền và nguồn kết nối. Custom VPC cũng giúp nhóm chủ động cấu hình Subnet, DB Subnet Group và quy tắc bảo mật.

**Tại sao không chỉ dùng Default VPC hoặc Public Access:** Default VPC thuận tiện cho thử nghiệm nhưng khó thể hiện rõ ranh giới mạng của hệ thống. Public Access có thể được bật tạm thời khi kiểm tra bằng công cụ quản trị, nhưng không phù hợp làm cấu hình lâu dài vì làm tăng bề mặt tấn công. Trong kiến trúc chính, kết nối RDS được giới hạn bằng VPC và Security Group.

### AWS Secrets Manager

**Vai trò trong dự án:** Secrets Manager lưu thông tin nhạy cảm dùng để kết nối RDS, tối thiểu gồm `username` và `password`. Secret được tạo thủ công có thể chứa thêm `host`, `port`, `dbname` và `engine`. Lambda đọc secret tại thời điểm chạy thông qua IAM Role; mật khẩu không được ghi cứng trong mã nguồn hoặc đưa lên GitHub.

**Tại sao lựa chọn:** Secret được mã hóa, quản lý tập trung, có lịch sử phiên bản và hỗ trợ xoay vòng thông tin đăng nhập. Khi mật khẩu thay đổi, nhóm chỉ cập nhật secret thay vì sửa và triển khai lại mã nguồn.

**Tại sao không lưu trong Lambda Environment Variables:** Biến môi trường phù hợp với cấu hình không nhạy cảm như tên bảng hoặc Region. Dù Lambda có hỗ trợ mã hóa biến môi trường, việc tách mật khẩu sang Secrets Manager giúp quản lý secret, phân quyền và xoay vòng rõ ràng hơn.

**Tại sao không dùng Systems Manager Parameter Store:** Parameter Store phù hợp với cấu hình và secret đơn giản, đồng thời có thể tiết kiệm chi phí hơn. Tuy nhiên, dự án chọn Secrets Manager vì thông tin đăng nhập RDS cần cơ chế quản lý secret chuyên biệt và khả năng hỗ trợ rotation.

### Amazon RDS for PostgreSQL và pgvector

**Vai trò trong dự án:** RDS lưu metadata tài liệu trong bảng `documents` và các đoạn nội dung cùng embedding `vector(1024)` trong bảng `document_chunks`. Extension `pgvector` thực hiện tìm kiếm tương đồng phục vụ RAG.

**Tại sao lựa chọn:** PostgreSQL hỗ trợ dữ liệu quan hệ, câu lệnh SQL và vector trong cùng một cơ sở dữ liệu. Điều này phù hợp với quy mô thử nghiệm 2.000 vector và giúp việc kiểm tra dữ liệu thuận tiện.

**Tại sao không dùng DynamoDB cho vector:** DynamoDB tối ưu cho truy vấn theo Partition Key và Sort Key, không phải phép tìm kiếm vector gần nhất. **Tại sao chưa dùng Amazon OpenSearch Service:** OpenSearch phù hợp với khối lượng tìm kiếm lớn và nhiều yêu cầu tìm kiếm nâng cao, nhưng cần thêm cấu hình, vận hành và chi phí; với phạm vi thực tập, PostgreSQL cùng pgvector đơn giản hơn.

### Amazon DynamoDB

**Vai trò trong dự án:** Bảng `ChatHistory-dev` lưu tin nhắn theo khóa truy cập của người dùng và phiên trò chuyện. Lambda thực hiện Create, Get, Update và Delete lịch sử chat.

**Tại sao lựa chọn:** Lịch sử chat có cấu trúc linh hoạt, chủ yếu được truy vấn theo khóa và không cần phép JOIN phức tạp. DynamoDB cung cấp độ trễ thấp, tự động mở rộng và không cần quản trị máy chủ.

**Tại sao không lưu toàn bộ trong RDS:** RDS vẫn có thể lưu lịch sử chat, nhưng việc tách hai loại dữ liệu giúp mỗi cơ sở dữ liệu đảm nhiệm đúng thế mạnh: RDS xử lý quan hệ và vector, DynamoDB xử lý truy vấn lịch sử theo khóa. Cách này cũng giảm số kết nối vào RDS.

### AWS Lambda

**Vai trò trong dự án:** Lambda khởi tạo bảng RDS, thực hiện CRUD lịch sử chat, tạo và chèn vector, tìm kiếm semantic, sinh dữ liệu giả và chạy benchmark.

**Tại sao lựa chọn:** Các tác vụ có thời gian chạy ngắn và được kích hoạt theo yêu cầu. Lambda không cần quản lý máy chủ, tự động mở rộng và tính phí theo số lần gọi cùng thời gian thực thi.

**Tại sao không dùng Amazon EC2 hoặc ECS:** EC2 yêu cầu quản lý máy ảo, hệ điều hành, cập nhật và chi phí trong thời gian máy chạy. ECS phù hợp hơn với container hoặc tiến trình chạy lâu. Phạm vi thực hành gồm các hàm ngắn nên Lambda gọn và tiết kiệm hơn.

### Amazon Bedrock – Titan Text Embeddings V2

**Vai trò trong dự án:** Bedrock chuyển nội dung văn bản thành embedding 1.024 chiều bằng model `amazon.titan-embed-text-v2:0`; vector sau đó được lưu và tìm kiếm trong pgvector.

**Tại sao lựa chọn:** Đây là dịch vụ AI được quản lý, tích hợp IAM và không yêu cầu tự triển khai máy chủ mô hình. Titan Embeddings đáp ứng trực tiếp nhu cầu tạo vector cho RAG.

**Tại sao không dùng Amazon SageMaker hoặc API AI bên ngoài:** SageMaker phù hợp khi cần huấn luyện, tùy chỉnh hoặc tự triển khai mô hình, nhưng phức tạp hơn nhu cầu của dự án. API bên ngoài làm phát sinh thêm việc quản lý khóa và truyền dữ liệu ra ngoài AWS; Bedrock giúp giữ luồng xử lý trong hệ sinh thái AWS.

### Amazon Cognito

**Vai trò trong dự án:** Cognito được dùng để tạo User Pool, cấu hình đăng ký/đăng nhập và kiểm tra quản lý người dùng. Phần thực hành này chỉ trình bày cấu hình xác thực mà bạn đã thực hiện, không nhận phần tích hợp toàn bộ backend.

**Tại sao lựa chọn:** Cognito cung cấp sẵn quản lý người dùng, chính sách mật khẩu và token, tránh phải tự xây dựng bảng tài khoản và cơ chế xác thực.

**Tại sao không tự viết hệ thống đăng nhập:** Tự xây dựng xác thực làm tăng rủi ro lưu mật khẩu sai cách, quản lý token không an toàn và tốn thêm thời gian kiểm thử bảo mật.

### AWS IAM và Amazon CloudWatch

**Vai trò trong dự án:** IAM Role cấp cho Lambda đúng các quyền cần thiết để truy cập RDS qua VPC, đọc secret, thao tác DynamoDB, gọi Bedrock và ghi log. CloudWatch Logs ghi lại kết quả chạy, thời gian phản hồi và lỗi của Lambda.

**Tại sao lựa chọn:** Đây là các dịch vụ tích hợp sẵn với Lambda. IAM hỗ trợ nguyên tắc least privilege, còn CloudWatch giúp kiểm tra và đo hiệu năng mà không cần cài hệ thống giám sát riêng.

| Nhu cầu | Dịch vụ được chọn | Phương án không chọn trong phạm vi này | Lý do chính |
|---|---|---|---|
| Mạng riêng cho RDS | VPC + Security Group | Chỉ dùng Default VPC/Public Access | Kiểm soát Subnet và nguồn được phép vào cổng 5432 |
| Bảo vệ thông tin RDS | Secrets Manager | Hard-code/Environment Variables/Parameter Store | Quản lý secret tập trung và hỗ trợ rotation |
| Dữ liệu tài liệu và vector | RDS PostgreSQL + pgvector | DynamoDB/OpenSearch | SQL và vector trong cùng DB, phù hợp quy mô thử nghiệm |
| Lịch sử chat | DynamoDB | Lưu toàn bộ trong RDS | Truy vấn theo khóa nhanh, schema linh hoạt, serverless |
| Xử lý tác vụ | Lambda | EC2/ECS | Không quản trị máy chủ, phù hợp tác vụ ngắn |
| Tạo embedding | Bedrock Titan V2 | SageMaker/API bên ngoài | Không tự triển khai model, tích hợp IAM |
| Quản lý người dùng | Cognito | Tự viết authentication | Giảm rủi ro bảo mật và thời gian phát triển |
| Phân quyền và log | IAM + CloudWatch | Hard-code key/hệ giám sát riêng | Tích hợp trực tiếp, least privilege và dễ kiểm tra |


## Kết quả mong đợi

- RDS hoạt động với `pgvector`.
- DynamoDB lưu và truy vấn được lịch sử chat.
- Các Lambda CRUD chạy thành công.
- Vector search trả về đúng đoạn dữ liệu mong đợi.
- Có số liệu hiệu năng RDS và DynamoDB.

## Nội dung thực hiện

1. [Đặc tả Kiến trúc Frontend](5.1.1%20-frontend-architecture/)
2. [Đặc tả Kiến trúc Backend & RAG Pipeline](5.1.2%20-backend-architecture/)
3. [Sơ đồ Kiến trúc Tổng quan trên AWS](5.1.3%20-overall-aws-architecture/)
4. [Danh sách Dịch vụ AWS Sử dụng](5.1.4%20-aws-services-used/)
