---
title: "Worklog Tuần 3"
date: 2026-08-08
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

**Thời gian:** Từ ngày **06/07/2026** đến ngày **11/07/2026**

### Mục tiêu Tuần 3:

* Tìm hiểu sâu hơn về Amazon S3, AWS Lambda và Amazon Textract trong quy trình xử lý tài liệu.
* Thiết kế và thử nghiệm luồng tiếp nhận tài liệu trên Amazon S3.
* Xây dựng Lambda nhận sự kiện tải tệp, kiểm tra dữ liệu đầu vào và gọi Amazon Textract.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|---|---|---|---|---|
| 2 | - Tìm hiểu lại cấu trúc bucket, object key, prefix và metadata trên Amazon S3<br>- Thiết kế thư mục logic để phân biệt tài liệu gốc, dữ liệu đang xử lý và kết quả đầu ra | 06/07/2026 | 06/07/2026 | [Amazon S3](https://docs.aws.amazon.com/s3/) |
| 3 | - Tìm hiểu S3 Event Notification và cách kích hoạt Lambda khi có tài liệu mới<br>- Cấu hình trigger theo prefix và loại sự kiện để tránh Lambda bị gọi ngoài ý muốn | 07/07/2026 | 07/07/2026 | [S3 Event Notifications](https://docs.aws.amazon.com/AmazonS3/latest/userguide/EventNotifications.html) |
| 4 | - Học cấu trúc dữ liệu của một S3 event<br>- Viết Lambda đọc bucket, object key, kích thước và metadata của tệp vừa tải lên<br>- Kiểm tra quyền IAM để Lambda chỉ được truy cập các tài nguyên cần thiết | 08/07/2026 | 08/07/2026 | [Using Lambda with S3](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html) |
| 5 | - Kiểm tra phần mở rộng và giới hạn định dạng tệp được hỗ trợ<br>- Xử lý object key được URL encode và tên tệp có khoảng trắng hoặc ký tự tiếng Việt<br>- Bổ sung kiểm tra dữ liệu đầu vào trước khi chuyển sang bước OCR | 09/07/2026 | 09/07/2026 | |
| 6 | - Tìm hiểu API đồng bộ của Amazon Textract<br>- Thử gọi Textract từ Lambda với ảnh PNG/JPEG mẫu và đọc các block PAGE, LINE, WORD trong phản hồi | 10/07/2026 | 10/07/2026 | [Amazon Textract API](https://docs.aws.amazon.com/textract/latest/dg/API_Reference.html) |
| 7 | - Lưu kết quả Textract thô vào khu vực output trên S3<br>- Kiểm tra CloudWatch Logs để theo dõi từng bước xử lý<br>- Tổng kết thử nghiệm và ghi nhận các vấn đề cần giải quyết với PDF nhiều trang | 11/07/2026 | 11/07/2026 | [CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/) |

### Kết quả đạt được tuần 3:

* Hiểu cách S3 Event Notification kích hoạt Lambda trong kiến trúc hướng sự kiện.
* Hoàn thành luồng thử nghiệm từ thao tác tải tệp đến Lambda và Textract.
* Trích xuất thành công văn bản từ ảnh thử nghiệm.
* Lưu được kết quả thô trên Amazon S3.
* Bổ sung bước giải mã object key để xử lý tên tệp có khoảng trắng.
* Giới hạn trigger bằng prefix để tránh vòng lặp khi Lambda ghi kết quả.
