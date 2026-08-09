---
title: "Tạo Amazon S3 lưu trữ tài liệu thô"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 5.4.3 </b> "
---

Trong phần này, nhóm sử dụng Amazon S3 để lưu trữ các tài liệu PDF và hình ảnh thô được tải lên hệ thống Smart Docs AI.

### Thông tin S3 Bucket

| Thuộc tính | Giá trị |
|---|---|
| Tên Bucket | `document-chatbot-files-dev-273` |
| AWS Region | Asia Pacific (Singapore) - `ap-southeast-1` |
| Bucket Versioning | Disabled |
| Object Ownership | Bucket owner enforced |
| ACL | Disabled |
| Block Public Access | Bật toàn bộ |
| Mã hóa mặc định | SSE-S3 - Amazon S3 managed keys |

### Cấu trúc lưu trữ

Bucket được chia thành hai thư mục chính:

```text
document-chatbot-files-dev-273/
├── uploads/
└── textract-output/
```

- `uploads/`: lưu các tệp PDF và hình ảnh thô do người dùng tải lên.
- `textract-output/`: lưu kết quả được tạo ra trong quá trình trích xuất nội dung tài liệu.

### Các bước tạo Bucket

1. Truy cập **Amazon S3** trên AWS Management Console.
2. Chọn **Create bucket**.
3. Nhập tên Bucket `document-chatbot-files-dev-273`.
4. Chọn Region **Asia Pacific (Singapore) - ap-southeast-1**.
5. Chọn **ACLs disabled** và **Bucket owner enforced**.
6. Giữ **Block all public access** ở trạng thái bật.
7. Chọn mã hóa mặc định **Server-side encryption with Amazon S3 managed keys (SSE-S3)**.
8. Giữ **Bucket Versioning** ở trạng thái tắt.
9. Chọn **Create bucket**.
10. Trong Bucket vừa tạo, tạo hai thư mục `uploads/` và `textract-output/`.

### Cấu hình đang sử dụng

Khi một Object mới được tạo trong thư mục `uploads/`, S3 Event Notification sẽ kích hoạt Lambda `textract-start-dev` để bắt đầu xử lý tài liệu. Bucket cũng cho phép các phương thức `PUT`, `POST`, `GET` và `HEAD` trong cấu hình CORS để ứng dụng có thể tải tài liệu lên và truy cập tệp theo quyền được cấp.

### Kết quả

Sau khi hoàn tất, hệ thống có một S3 Bucket riêng tư để lưu tài liệu thô và kết quả xử lý. Các Object được mã hóa tự động bằng SSE-S3, không thể truy cập công khai và sẵn sàng cho bước xử lý tài liệu tiếp theo.
