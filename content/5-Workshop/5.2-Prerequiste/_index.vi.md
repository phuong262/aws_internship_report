---
title: "Chuẩn bị"
weight: 2
chapter: false
pre: "<b>5.2. </b>"
---

# Tổng quan

## Tài khoản và Region

- Sử dụng IAM User thay vì tài khoản Root.
- Bật MFA cho tài khoản.
- Chọn Region `ap-southeast-1` để tạo Database.
- Chọn Region `us-east-1` để sử dụng Amazon Bedrock.
- Kiểm tra AWS Credits, Cost Explorer và AWS Budgets.

![AWS Region](/images/2-Proposal/region.png)

## IAM Role cho Lambda

IAM Role cần có các nhóm quyền:

- Ghi log vào Amazon CloudWatch.
- Đọc và ghi bảng DynamoDB.
- Gọi Amazon Bedrock Runtime.
- Gắn Lambda vào VPC.
- Đọc đúng secret chứa thông tin kết nối RDS từ AWS Secrets Manager.

![Lambda IAM Role](/images/2-Proposal/policy.png)

## Khởi tạo VPC và Security Group

### Bước 1: Tạo VPC

1. Mở **VPC Console** và chọn **Create VPC**.
2. Chọn **VPC and more** để tạo đồng thời VPC và các Subnet.
3. Nhập tên VPC `document-chatbot-vpc-dev`.
4. Chọn IPv4 CIDR phù hợp, ví dụ `10.20.0.0/16`.
![Create VPC](/images/2-Proposal/vpc1.png)
5. Chọn ít nhất hai Availability Zone để RDS có thể tạo DB Subnet Group đúng yêu cầu.
6. Tạo các Private Subnet dùng cho RDS và Lambda.
![Create VPC](/images/2-Proposal/vpc2.png)
![Create VPC](/images/2-Proposal/vpc3.png)
7. VPC endpoints-> S3 Gateway, Chọn Enable DNS hostnames và Enable DNS resolution.
![Create VPC](/images/2-Proposal/vpc4.png)
8. Kiểm tra cấu hình rồi chọn **Create VPC**.
9. Chọn View VPC
![Create VPC](/images/2-Proposal/vpcflow.png)
10. Hiển thị VPC vừa tạo
![Create VPC](/images/2-Proposal/vpc.png)


### Bước 2: Tạo DB Subnet Group

1. Mở **Amazon RDS Console**.
2. Chọn **Subnet groups** và **Create DB subnet group**.
![DB subnet group](/images/2-Proposal/subnetg1.png)
3. Chọn VPC `document-chatbot-vpc-dev` vừa tạo.
![DB subnet group](/images/2-Proposal/sng3.png)
4. Chọn Subnet thuộc ít nhất hai Availability Zone.
![DB subnet group](/images/2-Proposal/subnetg2.png)
5. Tạo DB Subnet Group và sử dụng nhóm này khi tạo RDS PostgreSQL.
![DB subnet group](/images/2-Proposal/subnetg4.png)

### Bước 3: Tạo Security Group cho Lambda và RDS
Vào VPC -> Security Group -> Create

- **Lambda Security Group:** `document-chatbot-lambda-rds-sg` gắn vào các Lambda cần kết nối RDS; thông thường không cần Inbound Rule. Cấu hình :

![Lambda security group](/images/2-Proposal/lambdasg.png)
![Lambda security group](/images/2-Proposal/lambdasg2.png)

- **RDS Security Group:** `document-chatbot-rds-sg` thêm Inbound Rule loại PostgreSQL, TCP `5432`, Source là **Lambda Security Group**. Không đặt Source của cổng `5432` thành `0.0.0.0/0`. Cách tham chiếu Security Group bảo đảm chỉ Lambda thuộc nhóm được phép mới có thể kết nối cơ sở dữ liệu.

![RDS inbound rule](/images/2-Proposal/rdssg1.png)
![RDS inbound rule](/images/2-Proposal/rdssg2.png)

### Bước 4: Gắn Lambda vào VPC

1. Mở Lambda cần kết nối RDS.
![Lambda VPC configuration](/images/2-Proposal/vpclambda1.png)
![Lambda VPC configuration](/images/2-Proposal/vpclambda2.png)

2. Chọn **Configuration → VPC → Edit**.
3. Chọn VPC vừa tạo.
4. Chọn các Private Subnet phù hợp.
5. Chọn Lambda Security Group và lưu cấu hình.
![Lambda VPC configuration](/images/2-Proposal/vpclambda3.png)

Lambda trong Private Subnet cần một đường truy cập phù hợp để gọi Secrets Manager, DynamoDB và Bedrock. Có thể dùng NAT Gateway hoặc các VPC Endpoint tương ứng. Workshop chỉ mô tả phương án thực tế đã được cấu hình; không ghi rằng đã tạo NAT Gateway hoặc VPC Endpoint nếu bạn chưa thực hiện.

## Sử dụng secret do RDS quản lý

Khi khởi tạo Amazon RDS PostgreSQL, tùy chọn Manage master credentials in AWS Secrets Manager được bật. Vì vậy, RDS tự động tạo và quản lý secret chứa thông tin xác thực của Master User. Người thực hiện không tạo secret thủ công.

1. Mở RDS instance.
2. Kiểm tra phần **Configuration**
![Secret k](/images/2-Proposal/sec1.png)
3. Mở liên kết **Master credentials** tới secret đang được RDS quản lý.
![Secret k](/images/2-Proposal/sec2.png)
4. Sao chép ARN của secret để cấu hình `DB_SECRET_ARN` cho Lambda.

## Amazon Bedrock

Mô hình embedding sử dụng:

```text
Model ID: amazon.titan-embed-text-v2:0
Dimensions: 1024
Normalize: true
Region: us-east-1
```

## Source Code và Dependency

Chuẩn bị các Lambda sau:

- Lambda khởi tạo RDS.
- Lambda CRUD lịch sử chat.
- Lambda thêm vector.
- Lambda tìm kiếm vector.
- Lambda tạo dữ liệu thử nghiệm.

## Biến môi trường

Ví dụ các biến cần cấu hình:

```text
DB_HOST = chatbot-postgres-dev.*****.ap-southeast-1.rds.amazonaws.com
DB_PORT = 5432
DB_NAME = chatbot_db
DB_SECRET_ARN = arn:aws:secretsmanager:ap-southeast-1:043272859712*****
CHAT_TABLE_NAME = ChatHistory-dev
BEDROCK_REGION = us-east-1
```

Luôn dùng `DB_SECRET_ARN`. Chỉ có thể bỏ `DB_HOST`, `DB_PORT` và `DB_NAME` khi các trường này thật sự tồn tại trong JSON của secret.


### Nội dung thực hiện

1. [Chuẩn bị mã nguồn](5.2.1-source-code-preparation/)
2. [Chuẩn bị tài khoản AWS](5.2.2-aws-account-preparation/)
3. [Tạo IAM User](5.2.3-creating-an-IAM-user/)