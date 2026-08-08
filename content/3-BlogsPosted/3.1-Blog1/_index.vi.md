---
title: "Blog 1 - Làm chủ giám sát hệ thống Serverless với Amazon CloudWatch"
date: 2026-08-08
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

Trong kiến trúc Serverless và Microservices, một yêu cầu có thể đi qua nhiều thành phần như Amazon API Gateway, AWS Lambda, cơ sở dữ liệu và hàng đợi thông điệp. Vì vậy, việc xác định nguyên nhân khi hệ thống gặp lỗi thường khó khăn hơn so với ứng dụng nguyên khối. Amazon CloudWatch cung cấp một nền tảng quan sát tập trung, giúp theo dõi hoạt động của toàn bộ hệ thống AWS.

### Nội dung chính

* **CloudWatch Logs và Logs Insights:** Thu thập log từ nhiều dịch vụ về một nơi và hỗ trợ truy vấn để nhanh chóng tìm thông báo lỗi, thống kê sự kiện hoặc phân tích hành vi hệ thống.
* **Metrics và Dashboard:** Theo dõi các chỉ số có sẵn như thời gian thực thi Lambda, số request qua API Gateway hoặc mức sử dụng tài nguyên. Custom Metrics cho phép bổ sung những chỉ số nghiệp vụ riêng của ứng dụng.
* **CloudWatch Alarms:** Thiết lập ngưỡng cảnh báo cho các tình huống bất thường, chẳng hạn thời gian xử lý vượt giới hạn hoặc số lỗi HTTP 500 tăng cao. Cảnh báo có thể được gửi qua Amazon SNS đến email hoặc các kênh liên lạc của đội ngũ vận hành.
* **Tự động phản ứng với sự kiện:** Cảnh báo và sự kiện giám sát có thể kích hoạt các quy trình tự động như mở rộng tài nguyên hoặc chạy tác vụ khắc phục sự cố.

### Giá trị thực tế

CloudWatch giúp chuyển hoạt động giám sát từ bị động sang chủ động. Việc xây dựng log, metrics, dashboard và cảnh báo ngay từ đầu giúp rút ngắn thời gian debug, cải thiện khả năng vận hành và tăng độ tin cậy của hệ thống Serverless.

### Cách CloudWatch hỗ trợ một hệ thống Serverless

Trong một ứng dụng Serverless, người dùng có thể gửi yêu cầu qua Amazon API Gateway, yêu cầu được xử lý bởi nhiều hàm AWS Lambda và dữ liệu được đọc hoặc ghi vào Amazon DynamoDB, Amazon RDS hay Amazon S3. Khi một bước trong chuỗi này xảy ra lỗi, chỉ xem trạng thái của từng dịch vụ riêng lẻ thường không đủ để xác định nguyên nhân. CloudWatch giúp liên kết các tín hiệu vận hành thông qua log, metrics và cảnh báo tập trung.

Ví dụ, khi API phản hồi chậm, dashboard có thể cho thấy đồng thời thời gian phản hồi của API Gateway, thời lượng thực thi Lambda và số lần Lambda bị giới hạn đồng thời. Kỹ sư vận hành có thể dùng Logs Insights để lọc log theo mã yêu cầu, khoảng thời gian hoặc loại lỗi. Nhờ vậy, quá trình xác định lỗi được thu hẹp từ toàn bộ hệ thống xuống đúng thành phần gặp vấn đề.

### Quy trình giám sát đề xuất

1. Các dịch vụ AWS gửi log và metrics về Amazon CloudWatch.
2. Log được tổ chức theo log group và log stream để thuận tiện cho việc truy vấn.
3. CloudWatch Logs Insights được sử dụng để tìm lỗi, thống kê tần suất và phân tích xu hướng.
4. Các metrics quan trọng được đưa lên dashboard chung của hệ thống.
5. CloudWatch Alarms theo dõi ngưỡng cảnh báo và gửi thông báo thông qua Amazon SNS.
6. Với sự cố có kịch bản xử lý rõ ràng, cảnh báo có thể kích hoạt AWS Lambda hoặc một quy trình tự động để khắc phục.

### Các chỉ số nên theo dõi

* Số lượng request thành công và thất bại tại API Gateway.
* Tỷ lệ lỗi HTTP 4xx và 5xx.
* Thời gian thực thi, số lỗi và số lần throttle của AWS Lambda.
* Số lượng tài liệu được xử lý thành công hoặc thất bại.
* Độ trễ của cơ sở dữ liệu và mức sử dụng tài nguyên.
* Số lượng thông điệp đang chờ hoặc bị chuyển vào dead-letter queue.

### Kinh nghiệm triển khai

Log nên được viết theo cấu trúc nhất quán, ưu tiên định dạng JSON và có các trường như timestamp, request ID, user ID ẩn danh, tên chức năng và trạng thái xử lý. Không nên ghi mật khẩu, access key, token hoặc dữ liệu nhạy cảm vào log. Đồng thời, cần thiết lập thời gian lưu log phù hợp để tránh phát sinh chi phí không cần thiết.

Dashboard cũng nên tập trung vào những chỉ số phản ánh trực tiếp sức khỏe hệ thống thay vì hiển thị quá nhiều dữ liệu. Cảnh báo cần có ngưỡng hợp lý để tránh tình trạng gửi quá nhiều thông báo, khiến đội ngũ bỏ qua những cảnh báo thật sự quan trọng.

### Liên hệ với đề tài Smart Docs AI

Đối với Smart Docs AI, CloudWatch có thể theo dõi số tài liệu được tải lên, thời gian trích xuất nội dung, số yêu cầu hỏi đáp, thời gian phản hồi của mô hình và tỷ lệ xử lý thất bại. Những dữ liệu này hỗ trợ đánh giá hiệu năng, phát hiện điểm nghẽn và kiểm soát chi phí vận hành của nền tảng.

### Kết luận

Amazon CloudWatch không chỉ là nơi lưu log mà là nền tảng quan sát toàn diện cho hệ thống AWS. Khi được thiết kế đúng từ đầu, CloudWatch giúp đội ngũ nhìn thấy trạng thái hệ thống theo thời gian thực, phát hiện bất thường trước người dùng và đưa ra quyết định vận hành dựa trên dữ liệu.

**Bài viết đã đăng:** [Xem bài viết trên AWS Study Group VN](https://www.facebook.com/share/p/19PcYE9kvR/)
