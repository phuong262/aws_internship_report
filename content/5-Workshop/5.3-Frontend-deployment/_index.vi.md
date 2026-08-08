---
title: "Triển khai Frontend"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

# Triển khai Frontend

Trong phần này, **nhóm** sẽ thực hiện triển khai giao diện **SmartDocs AI** lên nền tảng AWS nhằm đưa ứng dụng vào môi trường vận hành thực tế, giúp người dùng có thể truy cập website thông qua Internet một cách nhanh chóng và ổn định. Quá trình triển khai không chỉ đảm bảo khả năng lưu trữ và phân phối các tệp tĩnh của ứng dụng mà còn tối ưu hiệu năng truy cập, đồng thời xây dựng quy trình triển khai tự động để giảm thiểu các thao tác thủ công khi cập nhật phiên bản mới.

Để thực hiện, nhóm sử dụng các dịch vụ của AWS bao gồm:

- **Amazon S3** để lưu trữ các tệp tĩnh của ứng dụng Frontend sau khi build.
- **Static Website Hosting** để biến S3 Bucket thành một website tĩnh có thể truy cập qua Internet.
- **Bucket Policy** và **Block Public Access** để cấu hình quyền truy cập phù hợp, cho phép người dùng truy cập website đồng thời đảm bảo tính bảo mật.
- **Amazon CloudFront** để phân phối nội dung thông qua mạng CDN, giúp giảm độ trễ, tăng tốc độ tải trang và cải thiện trải nghiệm người dùng.
- **AWS CodePipeline** kết hợp với **GitHub** để xây dựng quy trình CI/CD, tự động triển khai phiên bản mới của ứng dụng mỗi khi mã nguồn được cập nhật lên repository.

Sau khi hoàn thành chương này, Frontend của hệ thống sẽ được triển khai thành công trên AWS, có thể truy cập thông qua CloudFront với hiệu năng cao, đồng thời hỗ trợ cập nhật ứng dụng một cách tự động, nhanh chóng và nhất quán.

### Nội dung thực hiện

