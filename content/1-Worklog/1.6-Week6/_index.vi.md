---
title: "Worklog Tuần 6"
date: 2026-08-08
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

**Thời gian:** Từ ngày **27/07/2026** đến ngày **01/08/2026**

### Mục tiêu Tuần 6:

* Hoàn thiện khả năng xử lý lỗi và giám sát cho luồng S3, Lambda và Textract.
* Rà soát quyền IAM và kiểm thử nhiều loại tài liệu.
* Hoàn thiện phần việc xử lý tài liệu để tích hợp vào hệ thống.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|---|---|---|---|---|
| 2 | - Chuẩn hóa CloudWatch Logs theo document ID, trạng thái và bước xử lý<br>- Bổ sung log cần thiết để truy vết lỗi | 27/07/2026 | 27/07/2026 | [CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/) |
| 3 | - Bổ sung xử lý lỗi khi tệp không hợp lệ, thiếu quyền hoặc Textract trả về lỗi<br>- Hoàn thiện cơ chế retry có kiểm soát | 28/07/2026 | 28/07/2026 | [AWS Lambda Error Handling](https://docs.aws.amazon.com/lambda/latest/dg/invocation-retries.html) |
| 4 | - Kiểm thử với ảnh PNG, JPEG và PDF nhiều trang<br>- Kiểm tra tên tệp có khoảng trắng, ký tự tiếng Việt và object key được URL encode | 29/07/2026 | 29/07/2026 | |
| 5 | - Đối chiếu văn bản sau xử lý với tài liệu gốc<br>- Sửa lỗi thứ tự dòng, khoảng trắng, xuống dòng và nội dung trùng lặp | 30/07/2026 | 30/07/2026 | |
| 6 | - Rà soát IAM theo nguyên tắc least privilege cho S3, Lambda, Textract, SNS và CloudWatch<br>- Kiểm tra dữ liệu nhạy cảm không bị ghi vào log | 31/07/2026 | 31/07/2026 | [AWS IAM](https://docs.aws.amazon.com/iam/) |
| 7 | - Kiểm thử toàn bộ luồng xử lý tài liệu cùng nhóm<br>- Hoàn thiện tài liệu mô tả đầu vào, đầu ra và bàn giao phần xử lý cho bước tích hợp | 01/08/2026 | 01/08/2026 | |

### Kết quả đạt được tuần 6:

* Luồng xử lý tài liệu có log nhất quán và dễ truy vết.
* Bổ sung xử lý lỗi và retry cho các trường hợp thường gặp.
* Xử lý thành công ảnh và PDF nhiều trang, gồm tên tệp có ký tự tiếng Việt.
* Rà soát quyền IAM đúng phạm vi cần thiết.
* Hoàn thành kiểm thử và bàn giao đầu ra cho bước tích hợp hệ thống.
