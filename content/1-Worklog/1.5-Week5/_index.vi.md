---
title: "Worklog Tuần 5"
date: 2026-08-08
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

**Thời gian:** Từ ngày **20/07/2026** đến ngày **25/07/2026**

### Mục tiêu Tuần 5:

* Tích hợp dữ liệu đã xử lý với các thành phần AI/RAG và Database.
* Hoàn thiện cơ chế trạng thái và metadata tài liệu.
* Nghiên cứu Amazon RDS và biên soạn bài viết kỹ thuật.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|---|---|---|---|---|
| 2 | - Trao đổi với thành viên AI/RAG để thống nhất định dạng văn bản đầu vào, độ dài nội dung và metadata cần thiết cho bước chia đoạn, tạo embedding | 20/07/2026 | 20/07/2026 | |
| 3 | - Trao đổi với thành viên Database để thống nhất document ID, user ID, trạng thái xử lý và đường dẫn S3<br>- Kiểm tra khả năng truy vết dữ liệu về tài liệu gốc | 21/07/2026 | 21/07/2026 | |
| 4 | - Bổ sung các trạng thái UPLOADED, PROCESSING, COMPLETED và FAILED<br>- Cập nhật trạng thái tại từng bước để giao diện có thể thông báo tiến độ cho người dùng | 22/07/2026 | 22/07/2026 | |
| 5 | - Kiểm thử tích hợp với ảnh và PDF nhiều trang<br>- Sửa lỗi sai thứ tự dòng, khoảng trắng, ký tự tiếng Việt và dữ liệu trùng lặp | 23/07/2026 | 23/07/2026 | |
| 6 | - Nghiên cứu Amazon RDS, Multi-AZ, Read Replica, backup và mã hóa<br>- So sánh vai trò của RDS với DynamoDB và lớp lưu trữ vector trong hệ thống | 24/07/2026 | 24/07/2026 | [Amazon RDS](https://docs.aws.amazon.com/rds/) |
| 7 | - Viết bài “Tối ưu vận hành cơ sở dữ liệu với Amazon RDS” | 25/07/2026 | | |

### Kết quả đạt được tuần 5:

* Tích hợp định dạng dữ liệu với các thành phần AI/RAG và Database.
* Bổ sung trạng thái để theo dõi vòng đời tài liệu.
* Cải thiện chất lượng văn bản sau OCR.
* Thêm document ID và page number để truy vết nội dung về tài liệu gốc.
* Hoàn thành bài blog kỹ thuật về Amazon RDS.
