---
title: "Worklog Tuần 4"
date: 2026-08-08
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

**Thời gian:** Từ ngày **13/07/2026** đến ngày **18/07/2026**

### Mục tiêu Tuần 4:

* Phát triển luồng xử lý tài liệu bằng Amazon Textract cho cả ảnh và PDF.
* Chuẩn hóa các block và ghép văn bản thành nội dung hoàn chỉnh.
* Tổng hợp kiến thức đã thực hành thành bài viết kỹ thuật.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|---|---|---|---|---|
| 2 | - Phân tích các block PAGE, LINE, WORD, Geometry và mối quan hệ trong kết quả Textract<br>- Xác định những trường dữ liệu cần giữ lại để truy vết nội dung | 13/07/2026 | 13/07/2026 | [Textract Response Objects](https://docs.aws.amazon.com/textract/latest/dg/how-it-works-document-layout.html) |
| 3 | - Viết logic lọc block LINE, nhóm kết quả theo từng trang và loại bỏ dữ liệu không cần thiết<br>- Thử nghiệm với tài liệu có nhiều đoạn văn bản | 14/07/2026 | 14/07/2026 | |
| 4 | - Sắp xếp các dòng dựa trên vị trí Geometry trước khi ghép<br>- Chuẩn hóa khoảng trắng, ký tự xuống dòng và nội dung tiếng Việt sau OCR | 15/07/2026 | 15/07/2026 | |
| 5 | - Nghiên cứu quy trình Textract bất đồng bộ cho PDF nhiều trang<br>- Tìm hiểu StartDocumentTextDetection, JobId và GetDocumentTextDetection | 16/07/2026 | 16/07/2026 | [Asynchronous Operations](https://docs.aws.amazon.com/textract/latest/dg/async.html) |
| 6 | - Chuẩn hóa kết quả thành JSON gồm document ID, page number và nội dung văn bản<br>- Lưu dữ liệu đã xử lý vào S3 output để các thành phần tiếp theo sử dụng | 17/07/2026 | 17/07/2026 | |
| 7 | - Viết bài “Tự động hóa trích xuất tài liệu bằng Amazon Textract và Serverless” | 18/07/2026 | | |

### Kết quả đạt được tuần 4:

* Trích xuất và ghép nội dung văn bản theo đúng thứ tự tương đối.
* Giữ lại page number để truy vết nguồn tài liệu.
* Chuẩn hóa dữ liệu JSON thống nhất với nhóm.
* Hoàn thiện thiết kế xử lý ảnh và PDF nhiều trang.
* Hoàn thành bài blog kỹ thuật về Amazon Textract.
