---
title: "Blog 3 - Tự động hóa trích xuất tài liệu bằng Amazon Textract và Serverless"
date: 2026-08-08
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

Số hóa tài liệu thường yêu cầu hệ thống nhận dạng văn bản có khả năng xử lý nhiều loại hình ảnh, biểu mẫu và tài liệu PDF. Thay vì tự xây dựng và duy trì mô hình OCR, có thể kết hợp Amazon Textract với Amazon S3 và AWS Lambda để tạo một quy trình trích xuất dữ liệu tự động theo kiến trúc Serverless.

### Luồng xử lý đề xuất

1. Người dùng tải hình ảnh hoặc tài liệu lên Amazon S3.
2. Sự kiện tạo đối tượng mới trong S3 kích hoạt một hàm AWS Lambda.
3. Lambda gọi Amazon Textract để phân tích nội dung tài liệu.
4. Kết quả được chuẩn hóa thành JSON với các trường như mã tài liệu, nội dung văn bản và số trang.
5. Dữ liệu sau xử lý có thể được lưu trong Amazon DynamoDB, Amazon RDS hoặc chuyển sang những bước phân tích tiếp theo.

### Điểm nổi bật

* **Xử lý theo sự kiện:** Không cần duy trì máy chủ chờ; quy trình chỉ chạy khi có tài liệu mới.
* **Hỗ trợ nhiều loại dữ liệu:** Amazon Textract có thể nhận dạng văn bản in, chữ viết tay, biểu mẫu và bảng biểu.
* **Xử lý linh hoạt:** API đồng bộ phù hợp với ảnh JPEG hoặc PNG; API bất đồng bộ phù hợp với tài liệu PDF nhiều trang.
* **Dễ tích hợp:** Kết quả có cấu trúc có thể đưa vào cơ sở dữ liệu, công cụ tìm kiếm hoặc hệ thống RAG.
* **Tối ưu chi phí:** Kiến trúc Serverless tự động mở rộng theo số lượng tài liệu và tính phí dựa trên mức sử dụng thực tế.

### Giá trị thực tế

Giải pháp tạo nền tảng cho các bài toán xử lý hóa đơn, chứng từ, biểu mẫu và kho tài liệu doanh nghiệp. Việc tự động hóa từ khâu tải lên đến chuẩn hóa dữ liệu giúp giảm thao tác thủ công và hỗ trợ xây dựng các hệ thống tìm kiếm, phân tích hoặc hỏi đáp tài liệu thông minh.

### Vì sao cần tự động hóa xử lý tài liệu?

Trong nhiều tổ chức, dữ liệu vẫn tồn tại dưới dạng ảnh chụp, biểu mẫu, hóa đơn hoặc PDF. Nếu nhập liệu thủ công, quá trình xử lý sẽ tốn thời gian, khó mở rộng và dễ xảy ra sai sót. OCR truyền thống có thể nhận dạng ký tự nhưng thường yêu cầu đội ngũ tự xây dựng mô hình, xử lý ảnh và viết logic để xác định cấu trúc tài liệu.

Amazon Textract cung cấp API được quản lý để nhận dạng không chỉ dòng chữ mà còn mối quan hệ giữa các trường, bảng và biểu mẫu. Khi kết hợp với các dịch vụ Serverless, quy trình có thể tự động mở rộng theo số lượng tài liệu mà không cần duy trì máy chủ xử lý cố định.

### Kiến trúc chi tiết

Người dùng tải tài liệu lên một S3 bucket đầu vào. Sự kiện từ Amazon S3 có thể được chuyển trực tiếp đến AWS Lambda hoặc thông qua Amazon EventBridge. Lambda kiểm tra định dạng, kích thước và metadata trước khi gọi Amazon Textract.

Với ảnh đơn giản, hệ thống có thể gọi API đồng bộ và nhận kết quả ngay trong một lần xử lý. Với tài liệu PDF nhiều trang, nên sử dụng API bất đồng bộ. Textract bắt đầu một job xử lý và thông báo kết quả hoàn thành thông qua Amazon SNS. Một Lambda khác tiếp nhận thông báo, lấy kết quả và chuẩn hóa dữ liệu.

Kết quả cuối cùng có thể được lưu vào S3 dưới dạng JSON, ghi metadata vào Amazon DynamoDB hoặc Amazon RDS, và lập chỉ mục trong công cụ tìm kiếm. Tài liệu gốc và dữ liệu đã xử lý nên được lưu ở các khu vực riêng để dễ quản lý vòng đời và phân quyền.

### Các bước xử lý dữ liệu

1. Kiểm tra loại tệp và từ chối định dạng không được hỗ trợ.
2. Gán mã định danh duy nhất cho từng tài liệu.
3. Lưu trạng thái `UPLOADED`, `PROCESSING`, `COMPLETED` hoặc `FAILED`.
4. Gọi Amazon Textract theo chế độ đồng bộ hoặc bất đồng bộ.
5. Đọc các block kết quả và xác định nội dung văn bản, bảng hoặc cặp khóa–giá trị.
6. Chuẩn hóa dữ liệu thành JSON theo cấu trúc của ứng dụng.
7. Lưu kết quả và cập nhật trạng thái xử lý.
8. Gửi thông báo cho người dùng hoặc chuyển dữ liệu sang bước phân tích tiếp theo.

### Bảo mật và độ tin cậy

IAM role của Lambda chỉ nên được cấp quyền đọc đúng S3 bucket đầu vào, ghi vào khu vực kết quả và gọi những API Textract cần thiết. Các bucket cần chặn truy cập công khai và bật mã hóa. Nếu tài liệu chứa dữ liệu nhạy cảm, log không nên ghi nguyên nội dung tài liệu.

Để tăng độ tin cậy, hệ thống cần xử lý trường hợp Lambda chạy lại cùng một sự kiện, tài liệu lỗi hoặc job Textract thất bại. Có thể dùng dead-letter queue, cơ chế retry và mã tài liệu duy nhất để bảo đảm một tệp không bị ghi trùng kết quả.

### Kiểm soát hiệu năng và chi phí

Chi phí phụ thuộc vào loại phân tích và số trang được xử lý. Vì vậy, hệ thống nên kiểm tra tệp trước khi gửi, giới hạn kích thước và loại bỏ tài liệu trùng lặp. Amazon CloudWatch có thể theo dõi số job, tỷ lệ thất bại, thời gian xử lý trung bình và số tài liệu theo từng trạng thái.

Kiến trúc bất đồng bộ phù hợp hơn khi khối lượng tài liệu lớn vì không giữ Lambda chờ trong suốt thời gian Textract xử lý. Việc tách thành nhiều bước cũng giúp từng thành phần dễ mở rộng và khắc phục độc lập.

### Liên hệ với đề tài Smart Docs AI

Amazon Textract có thể đóng vai trò là lớp trích xuất đầu vào của Smart Docs AI. Văn bản sau khi được chuẩn hóa sẽ được chia thành các đoạn nhỏ, tạo vector embedding và lưu vào kho tìm kiếm. Khi người dùng đặt câu hỏi, hệ thống truy xuất những đoạn liên quan để tạo câu trả lời theo mô hình Retrieval-Augmented Generation.

### Kết luận

Sự kết hợp giữa Amazon S3, AWS Lambda và Amazon Textract tạo ra một pipeline xử lý tài liệu tự động, có khả năng mở rộng và dễ tích hợp. Đây là nền tảng phù hợp cho các hệ thống số hóa chứng từ, tìm kiếm tài liệu và xây dựng ứng dụng AI dựa trên dữ liệu doanh nghiệp.

**Bài viết đã đăng:** [Xem bài viết trên AWS Study Group VN](https://www.facebook.com/share/p/19J2wvt7Tv/)
