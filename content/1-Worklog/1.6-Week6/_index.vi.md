---
title: "Worklog Tuần 6"
date: 2026-08-08
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

**Thời gian:** Từ ngày **27/07/2026** đến ngày **01/08/2026**

### Mục tiêu Tuần 6:

* Tăng khả năng giám sát và xử lý lỗi của pipeline.
* Rà soát quyền IAM, bảo mật dữ liệu và chi phí vận hành.
* Nghiên cứu Amazon CloudWatch và viết bài chia sẻ.
* Kiểm thử hệ thống trước khi triển khai phiên bản hoàn chỉnh.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|---|---|---|---|---|
| 2 | - Chuẩn hóa CloudWatch Logs của Lambda theo document ID, trạng thái và bước xử lý<br>- Bổ sung thông tin log cần thiết để dễ tìm nguyên nhân khi xảy ra lỗi | 27/07/2026 | 27/07/2026 | [CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/) |
| 3 | - Thiết kế dashboard theo dõi số tài liệu đã xử lý, số lần lỗi và thời gian xử lý trung bình<br>- Tìm hiểu cách lựa chọn metric hữu ích cho pipeline Serverless | 28/07/2026 | 28/07/2026 | [CloudWatch Dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html) |
| 4 | - Thiết lập cảnh báo thử nghiệm cho Lambda error, timeout và thời gian xử lý bất thường<br>- Kiểm tra cách nhận thông báo khi metric vượt ngưỡng | 29/07/2026 | 29/07/2026 | [CloudWatch Alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html) |
| 5 | - Bổ sung cơ chế retry có kiểm soát<br>- Tìm hiểu dead-letter queue và phương án xử lý sự kiện thất bại mà không làm mất tài liệu | 30/07/2026 | 30/07/2026 | |
| 6 | - Rà soát IAM theo nguyên tắc least privilege, mã hóa dữ liệu trên S3 và thông tin được phép ghi vào log<br>- Kiểm thử lại toàn bộ luồng từ tải tệp đến dữ liệu đầu ra trước khi triển khai | 31/07/2026 | 31/07/2026 | |
| 7 | - Viết bài “Làm chủ giám sát hệ thống Serverless với Amazon CloudWatch” | 01/08/2026 | | |

### Kết quả đạt được tuần 6:

* Pipeline có log nhất quán và dễ truy vết hơn.
* Xác định các metrics và cảnh báo quan trọng.
* Bổ sung phương án retry và xử lý sự kiện thất bại.
* Rà soát quyền IAM và nguyên tắc bảo vệ tài liệu.
* Hoàn thành kiểm thử luồng xử lý để chuẩn bị triển khai hệ thống trong tuần 7.
* Hoàn thành bài blog kỹ thuật về Amazon CloudWatch.
