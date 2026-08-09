---
title: "Worklog Tuần 5"
date: 2026-08-08
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

**Thời gian:** Từ ngày **20/07/2026** đến ngày **25/07/2026**

### Mục tiêu Tuần 5:

* Hoàn thiện luồng Textract bất đồng bộ cho tài liệu PDF nhiều trang.
* Xây dựng Lambda nhận kết quả, ghép dòng và chuẩn hóa nội dung văn bản.
* Tích hợp đầu ra xử lý tài liệu với các thành phần tiếp theo của hệ thống.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|---|---|---|---|---|
| 2 | - Cấu hình Lambda gọi `StartDocumentTextDetection` cho PDF nhiều trang<br>- Lưu `JobId` và thông tin tài liệu để theo dõi quá trình xử lý | 20/07/2026 | 20/07/2026 | [Textract Asynchronous Operations](https://docs.aws.amazon.com/textract/latest/dg/async.html) |
| 3 | - Cấu hình Amazon SNS nhận thông báo khi Textract hoàn tất<br>- Thiết lập Lambda nhận sự kiện thông báo và lấy kết quả theo `JobId` | 21/07/2026 | 21/07/2026 | [Amazon SNS](https://docs.aws.amazon.com/sns/) |
| 4 | - Xử lý phân trang khi gọi `GetDocumentTextDetection`<br>- Thu thập đầy đủ các block từ tài liệu nhiều trang | 22/07/2026 | 22/07/2026 | [GetDocumentTextDetection](https://docs.aws.amazon.com/textract/latest/dg/API_GetDocumentTextDetection.html) |
| 5 | - Viết logic sắp xếp và ghép các dòng, đoạn văn rời rạc thành khối nội dung hoàn chỉnh<br>- Chuẩn hóa khoảng trắng, xuống dòng và ký tự tiếng Việt | 23/07/2026 | 23/07/2026 | |
| 6 | - Chuẩn hóa đầu ra gồm document ID, page number và nội dung văn bản<br>- Chuyển kết quả cho thành phần lưu trữ vector và cơ sở dữ liệu do thành viên khác phụ trách | 24/07/2026 | 24/07/2026 | |
| 7 | - Kiểm thử luồng S3 → Lambda → Textract → SNS → Lambda xử lý kết quả<br>- Kiểm tra CloudWatch Logs và sửa các lỗi tích hợp | 25/07/2026 | 25/07/2026 | [CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/) |

### Kết quả đạt được tuần 5:

* Hoàn thành luồng Textract bất đồng bộ cho PDF nhiều trang.
* Nhận đầy đủ kết quả Textract thông qua SNS và xử lý phân trang thành công.
* Ghép các dòng, đoạn văn thành nội dung hoàn chỉnh và chuẩn hóa đầu ra.
* Tích hợp thành công đầu ra với thành phần tiếp theo của hệ thống.
