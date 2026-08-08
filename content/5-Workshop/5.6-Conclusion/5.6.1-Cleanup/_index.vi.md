---
title : "Dọn dẹp tài nguyên"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.6.1 </b> "
---

### Lưu ý trước khi xóa

Việc xóa tài nguyên trong hướng dẫn này là không thể hoàn tác: toàn bộ dữ liệu (hồ sơ người dùng, tài liệu đã tải lên, cơ sở dữ liệu Vector, log hệ thống) sẽ mất vĩnh viễn, vì hệ thống không thiết lập cơ chế backup hay snapshot nào. Nếu cần giữ lại dữ liệu, hãy làm trước khi xóa:

- Export các bảng DynamoDB (Profile, Chat History)
- Tải về các file PDF/Hình ảnh gốc trên Amazon S3.
- Dump dữ liệu Vector từ Amazon RDS PostgreSQL.
- Export CloudWatch logs nếu sau này cần phân tích lại.

---

### Quy trình dọn dẹp - Thứ tự xóa tài nguyên

**Quy tắc:** Phải xóa các kết nối (Traffic/Triggers) trước, sau đó xóa Compute (Lambda), và cuối cùng mới xóa Storage (S3, RDS, DynamoDB).

**Tóm tắt các giai đoạn:**

| Giai đoạn | Tài nguyên | Hành động chính | Thời gian |
|-------|-----------|-------------|------|
| **Giai đoạn 1: Dừng traffic** | CloudFront, API Gateway, S3 Triggers, SNS | Tắt CloudFront (⏱️ chờ 30 phút) → Xóa API Gateway → Gỡ bỏ các event trigger | ~35 phút |
| **Giai đoạn 2: Gỡ compute** | AWS Lambda | Xóa 15 hàm Lambda (Upload, Vector, ChatbotRAG...) | ~5 phút |
| **Giai đoạn 3: Xóa dữ liệu** | Amazon RDS, S3 Buckets, DynamoDB, Cognito | Làm rỗng S3 → Xóa Database RDS → Xóa các bảng DynamoDB → Xóa Cognito User Pool | ~5 phút |
| **Giai đoạn 4: Giám sát** | CloudWatch Logs, IAM Roles | Xóa log groups của Lambda → Xóa các Role đã cấp quyền | ~2 phút |

**Tổng thời gian:** ~47 phút (chủ yếu chờ CloudFront disable)

---

## Kiểm tra xác nhận đã dọn dẹp xong

Hãy lướt qua một vòng các dịch vụ chính trên AWS Console (đặc biệt là mục Billing Dashboard) để đảm bảo không còn tài nguyên nào "mắc kẹt" sinh ra chi phí.

**Trạng thái cuối cùng mong đợi:** Tất cả các lệnh trên trả về kết quả rỗng/không tìm thấy

---

## Xử lý sự cố thường gặp

### Sự cố 1: "Cannot delete S3 bucket - not empty"

**Nguyên nhân:** Bucket vẫn còn chứa file, hoặc chứa các file ẩn (Versioned objects).

**Cách xử lý:**
```powershell
# Force empty bucket (including versioned objects)
aws s3api delete-objects `
  --bucket document-chatbot-files-dev-273 `
  --delete "$(aws s3api list-object-versions --bucket document-chatbot-files-dev-273 --query '{Objects: Versions[].{Key:Key,VersionId:VersionId}}' --output json)"

# Then delete bucket
aws s3api delete-bucket --bucket document-chatbot-files-dev-273 --region us-east-1
```

### Sự cố 2: "Không tìm thấy nút xóa ứng dụng AWS Amplify hoặc xóa bị lỗi"

**Nguyên nhân:** Khác với các dịch vụ khác có nút xóa ngay ngoài danh sách, nút xóa của Amplify được đặt sâu trong phần cài đặt để tránh xóa nhầm. Ngoài ra, nếu ứng dụng đang trong quá trình tự động Deploy/Build, AWS sẽ chặn thao tác xóa.

**Cách xử lý:**
1. Đảm bảo ứng dụng không ở trạng thái đang Build (nếu đang Build, hãy vào trong tiến trình và bấm Cancel).
2. Từ giao diện AWS Amplify Console, bấm chọn vào ứng dụng của bạn.
3. Nhìn sang menu bên trái, cuộn xuống dưới cùng và chọn **App settings** > **General** (Cài đặt chung).
4. Cuộn xuống dưới cùng của trang General, bạn sẽ thấy nút **Delete app** màu đỏ. Bấm vào đó và xác nhận để xóa hoàn toàn.
---