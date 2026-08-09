---
title: "Khởi tạo Amazon DynamoDB"
weight: 2
chapter: false
pre: "<b> 5.4.2 </b>"
---

## Tạo các bảng `ChatHistory-dev`, `Documents-dev`, `Users-dev`.

| Table | Partition Key | Sort Key | Chức năng |
| --- | --- | --- | --- |
| `ChatHistory-dev` | `sessionId (S)` | `messageKey (S)` | Lưu lịch sử trò chuyện |
| `Documents-dev` | `documentId (S)` | Không có | Lưu metadata tài liệu |
| `Users-dev` | `userId (S)` | Không có | Lưu thông tin người dùng |

1. Mở Amazon DynamoDB.
2. Chọn Create Table.
3. Nhập tên bảng, cấu hình Partition Key và Sort Key. Thiết lập đúng các key của từng table.
![DynamoDB table active](/images/5-Workshop/5.4-Backend-deployment/DynamoDb/12.png)
4. Tạo bảng và chờ trạng thái Active.
![DynamoDB table active](/images/5-Workshop/5.4-Backend-deployment/DynamoDb/11.png)

## Thiết kế dữ liệu

# AMAZON DYNAMODB

Amazon DynamoDB được sử dụng để lưu trữ lịch sử trò chuyện, metadata tài liệu và thông tin người dùng.

Trong hệ thống, dữ liệu vector embedding không được lưu trong DynamoDB mà được lưu trong bảng `document_chunks` của Amazon RDS PostgreSQL sử dụng extension `pgvector`.

Các bảng DynamoDB được sử dụng gồm:

- `ChatHistory-dev`: lưu lịch sử và thông tin phiên trò chuyện.
- `Documents-dev`: lưu metadata của tài liệu.
- `Users-dev`: lưu thông tin hồ sơ người dùng.

---

## Tạo các bảng DynamoDB

Các bảng được thiết kế như sau:

| Table | Partition Key | Sort Key | Chức năng |
| --- | --- | --- | --- |
| `ChatHistory-dev` | `sessionId (S)` | `messageKey (S)` | Lưu lịch sử trò chuyện |
| `Documents-dev` | `documentId (S)` | Không có | Lưu metadata tài liệu |
| `Users-dev` | `userId (S)` | Không có | Lưu thông tin người dùng |

### Các bước thực hiện

1. Mở **AWS Management Console**.
2. Tìm và chọn dịch vụ **Amazon DynamoDB**.
3. Trong menu bên trái, chọn **Tables**.
![DynamoDB](/images/5-Workshop/5.4-Backend-deployment/DynamoDb/1.png)

4. Chọn **Create table**.
5. Nhập tên bảng tại mục **Table name**.
6. Nhập **Partition key** theo thiết kế của từng bảng.
7. Với bảng `ChatHistory-dev`, nhập thêm **Sort key** là:
![DynamoDB](/images/5-Workshop/5.4-Backend-deployment/DynamoDb/2.png)
![DynamoDB](/images/5-Workshop/5.4-Backend-deployment/DynamoDb/3.png)
8. Chọn **Create table**.


```text
messageKey
|---|---|
| `userId` | Mã người dùng |
| `sessionId` | Mã phiên chat |
| `messageId` | Mã tin nhắn |
| `messageKey` | Khóa theo `createdAt#messageId` |
| `role` | `user` hoặc `assistant` |
| `content` | Nội dung tin nhắn |
| `references` | Nguồn tài liệu |
| `createdAt` | Thời gian tạo |
| `updatedAt` | Thời gian cập nhật |
```
![DynamoDB sample item](/images/5-Workshop/5.4-Backend-deployment/DynamoDb/4.png)

## Kiểm tra bảng

- Mở **Explore table items**.
- Kiểm tra khóa và thuộc tính.
- Lọc theo người dùng hoặc phiên chat.
- Kiểm tra thứ tự tin nhắn.

> **Kết quả mong đợi:** Dữ liệu của mỗi phiên có thể được truy vấn và sắp xếp theo thời gian.

![DynamoDB explore items](/images/5-Workshop/5.4-Backend-deployment/DynamoDb/5.png)

## Kiểm tra dữ liệu
Kiểm tra dữ liệu DynamoDb
1. Mở AWS DynamoDb.
2. Chọn Table kiểm tra `ChatHistory-dev`.
3. Chọn setting ở ChatHistory-dev - Items returned.

![DynamoDB table active](/images/5-Workshop/5.4-Backend-deployment/DynamoDb/13.png)

Kiểm tra dữ liệu RDS
1. Mở AWS lambda.
2. Sau khi hoàn thành lambda rds-init-dev -> Deploy-> Test.
3. Data = `{}` -> Test.

![DynamoDB table active](/images/5-Workshop/5.4-Backend-deployment/DynamoDb/14.png)