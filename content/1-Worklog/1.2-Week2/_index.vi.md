---
title: "Worklog Tuần 2"
date: 2026-08-08
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu Tuần 2:

* Tiếp tục học các dịch vụ AWS liên quan đến lưu trữ, phân phối nội dung, cơ sở dữ liệu, mở rộng hệ thống, mã hóa và xác thực người dùng.
* Tìm hiểu ưu điểm, hạn chế và trường hợp sử dụng của từng dịch vụ trước khi lựa chọn kiến trúc cho Smart Docs AI.
* Thực hành một số cấu hình cơ bản trên AWS để củng cố kiến thức đã học.
* Làm quen với giao diện Smart Docs AI và tìm hiểu cách Frontend kết nối với các dịch vụ Backend trên AWS.
* Chuẩn bị kiến thức nền tảng cho nhiệm vụ xử lý dữ liệu tài liệu trong các tuần tiếp theo.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|---|---|---|---|---|
| 2 | - Làm quen với mã nguồn Frontend của Smart Docs AI và tìm hiểu cách tổ chức các thành phần giao diện bằng ReactJS<br>- Học cách sử dụng Tailwind CSS để thống nhất màu sắc, khoảng cách và cách trình bày giữa các trang<br>- Trao đổi với nhóm về luồng hoạt động dự kiến của hệ thống: tải tài liệu lên, xử lý nội dung, lưu trữ dữ liệu và đặt câu hỏi<br>- Chỉnh sửa thử một số thành phần giao diện đơn giản để hiểu cách dự án được tổ chức | 29/06/2026 | 29/06/2026 | [React Documentation](https://react.dev/learn), [Tailwind CSS](https://tailwindcss.com/docs) |
| 3 | - Nghiên cứu Amazon S3: bucket, object, storage class, versioning và quyền truy cập<br>- Tìm hiểu tính năng Static Website Hosting và các rủi ro khi cho phép bucket truy cập công khai<br>- So sánh website lưu trực tiếp trên S3 với mô hình S3 kết hợp Amazon CloudFront về HTTPS, bộ nhớ đệm và khả năng phân phối nội dung<br>- Thực hành tạo S3 bucket, tải tệp mẫu và tìm hiểu cách CloudFront Distribution truy cập S3 thông qua Origin Access Control | 30/06/2026 | 30/06/2026 | [Amazon S3](https://docs.aws.amazon.com/s3/), [Amazon CloudFront](https://docs.aws.amazon.com/cloudfront/) |
| 4 | - Tìm hiểu Amazon RDS và Amazon Aurora; so sánh cơ sở dữ liệu được quản lý truyền thống với cơ sở dữ liệu tương thích Cloud có khả năng mở rộng cao<br>- Học khái niệm automated backup, snapshot thủ công, restore và Multi-AZ trên Amazon RDS<br>- Tìm hiểu Amazon DynamoDB và mô hình dữ liệu key-value/document<br>- So sánh RDS, Aurora và DynamoDB theo cấu trúc dữ liệu, khả năng mở rộng, cách quản trị và chi phí để làm cơ sở lựa chọn cơ sở dữ liệu cho dự án | 01/07/2026 | 01/07/2026 | [Amazon RDS](https://docs.aws.amazon.com/rds/), [Amazon Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html), [Amazon DynamoDB](https://docs.aws.amazon.com/dynamodb/) |
| 5 | - Tìm hiểu EC2 Auto Scaling gồm Launch Template, Auto Scaling Group và scaling policy<br>- Học cách Elastic Load Balancing phân phối lưu lượng đến các instance đang hoạt động tốt<br>- Tìm hiểu ví dụ mở rộng theo chỉ số CloudWatch, chẳng hạn tăng số lượng instance khi CPU vượt ngưỡng<br>- Tìm hiểu tổng quan Amazon EFS, Amazon FSx và Amazon Lightsail; so sánh với Amazon S3 để hiểu loại lưu trữ phù hợp với từng nhu cầu | 02/07/2026 | 02/07/2026 | [EC2 Auto Scaling](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html), [Elastic Load Balancing](https://docs.aws.amazon.com/elasticloadbalancing/), [Amazon EFS](https://docs.aws.amazon.com/efs/) |
| 6 | - Tìm hiểu AWS KMS và vai trò của khóa mã hóa trong việc bảo vệ dữ liệu trên AWS<br>- Phân biệt AWS Managed Key và Customer Managed Key; tìm hiểu cách các dịch vụ như S3 và DynamoDB sử dụng KMS để mã hóa dữ liệu<br>- Tìm hiểu Amazon Cognito User Pool và quy trình đăng ký, xác nhận email, đăng nhập, nhận token và đăng xuất<br>- Ghi chú yêu cầu sử dụng HTTPS cho OAuth callback và lý do có thể kết hợp CloudFront với S3 khi triển khai Frontend | 03/07/2026 | 03/07/2026 | [AWS KMS](https://docs.aws.amazon.com/kms/), [Amazon Cognito](https://docs.aws.amazon.com/cognito/) |
| 7 | - Ôn lại kiến thức về S3, CloudFront, RDS, Aurora, DynamoDB, Auto Scaling, KMS và Cognito<br>- Vẽ sơ đồ thử nghiệm để hình dung cách Frontend, xác thực người dùng, lưu trữ tài liệu và cơ sở dữ liệu có thể kết nối với nhau<br>- Trao đổi với nhóm về lựa chọn kiến trúc Serverless nhằm giảm công việc vận hành và tối ưu chi phí trong giai đoạn thực tập<br>- Xác định phần việc xử lý tài liệu: nhận tệp từ S3, xử lý bằng Lambda và Textract, sau đó chuẩn hóa nội dung cho hệ thống RAG<br>- Hoàn thiện Worklog và tổng hợp các vấn đề cần tiếp tục nghiên cứu trong tuần sau | 04/07/2026 | 04/07/2026 | [AWS Architecture Center](https://aws.amazon.com/architecture/), [AWS Serverless](https://aws.amazon.com/serverless/) |

### Kết quả đạt được tuần 2:

* Hiểu cách tổ chức cơ bản của Frontend Smart Docs AI và vai trò của giao diện trong luồng tải tài liệu, đăng nhập và đặt câu hỏi.
* Nắm được chức năng chính của Amazon S3 và lý do nên sử dụng CloudFront để bổ sung HTTPS, bộ nhớ đệm và giới hạn truy cập trực tiếp vào bucket.
* Phân biệt được Amazon RDS, Amazon Aurora và Amazon DynamoDB ở mức cơ bản; hiểu rằng việc lựa chọn phụ thuộc vào cấu trúc dữ liệu và nhu cầu mở rộng của hệ thống.
* Hiểu nguyên lý hoạt động của EC2 Auto Scaling, Launch Template, Auto Scaling Group và Elastic Load Balancing.
* Nhận biết được điểm khác nhau giữa Amazon S3, Amazon EFS, Amazon FSx và mục đích sử dụng của từng loại lưu trữ.
* Hiểu vai trò của AWS KMS trong mã hóa dữ liệu và sự khác nhau giữa AWS Managed Key với Customer Managed Key.
* Nắm được quy trình xác thực cơ bản của Amazon Cognito User Pool và yêu cầu HTTPS đối với OAuth callback.
* Hình dung được kiến trúc tổng quan của Smart Docs AI và xác định các dịch vụ cần tiếp tục học sâu cho phần xử lý dữ liệu tài liệu.
* Xác định rõ hơn nhiệm vụ xử lý tài liệu trong dự án: tiếp nhận tệp từ S3, gọi Lambda và Textract, chuẩn hóa dữ liệu trước khi chuyển sang quy trình tạo vector embedding và tìm kiếm ngữ nghĩa.
