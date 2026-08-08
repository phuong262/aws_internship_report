---

title: "Bản đề xuất"
weight: 2
chapter: false
pre: "<b>2. </b>"
-----------------

# SMART DOCUMENT CHATBOT

## HỆ THỐNG HỎI ĐÁP TÀI LIỆU THÔNG MINH TRÊN AWS

---

## 1. TỔNG QUAN DỰ ÁN

Smart Document Chatbot là hệ thống hỗ trợ người dùng tải lên tài liệu và đặt câu hỏi bằng ngôn ngữ tự nhiên. Hệ thống tự động trích xuất nội dung, chia nhỏ văn bản, tạo vector embedding, tìm kiếm các đoạn thông tin liên quan và sử dụng mô hình trí tuệ nhân tạo để tạo câu trả lời.

Dự án áp dụng kiến trúc Retrieval-Augmented Generation (RAG), kết hợp khả năng tìm kiếm dữ liệu trong tài liệu với mô hình ngôn ngữ lớn. Nhờ đó, câu trả lời được tạo dựa trên nội dung tài liệu của người dùng thay vì chỉ dựa vào kiến thức có sẵn của mô hình AI.

Hệ thống được xây dựng chủ yếu bằng các dịch vụ AWS như Amazon Cognito, Amazon S3, Amazon Textract, AWS Lambda, Amazon Bedrock, Amazon RDS PostgreSQL, Amazon DynamoDB và Amazon API Gateway.

**Tên dự án:** Smart Document Chatbot

**Loại dự án:** Website hỏi đáp và tra cứu nội dung tài liệu

**Kiến trúc chính:** AWS Serverless kết hợp RDS PostgreSQL

**Mô hình xử lý:** Retrieval-Augmented Generation

**Khu vực triển khai:** AWS Region `us-east-1`

**Thời gian thực hiện:** 22/06/2026 - 15/08/2026

---

## 2. VẤN ĐỀ CẦN GIẢI QUYẾT

### 2.1. Tìm kiếm tài liệu thủ công mất nhiều thời gian

Người dùng thường phải đọc toàn bộ tài liệu PDF hoặc tài liệu văn bản để tìm kiếm một thông tin cụ thể. Đối với tài liệu dài hoặc số lượng tài liệu lớn, quá trình này tốn nhiều thời gian và dễ bỏ sót thông tin quan trọng.

### 2.2. Công cụ tìm kiếm từ khóa còn hạn chế

Tìm kiếm truyền thống chỉ hoạt động tốt khi từ khóa người dùng nhập giống với nội dung trong tài liệu. Hệ thống khó tìm được kết quả khi người dùng diễn đạt câu hỏi bằng từ ngữ khác nhưng có cùng ý nghĩa.

### 2.3. Mô hình AI có thể tạo thông tin không chính xác

Các mô hình ngôn ngữ có thể tạo ra câu trả lời không có trong tài liệu, còn được gọi là hiện tượng hallucination. Vì vậy, hệ thống cần giới hạn mô hình AI trả lời dựa trên những nội dung đã được truy xuất từ tài liệu.

### 2.4. Khó quản lý lịch sử trò chuyện

Người dùng cần lưu lại các câu hỏi và câu trả lời trước đó để tiếp tục cuộc hội thoại hoặc xem lại thông tin. Dữ liệu này cần được tổ chức theo từng người dùng và từng phiên trò chuyện.

### 2.5. Yêu cầu bảo mật dữ liệu người dùng

Tài liệu tải lên có thể chứa thông tin cá nhân hoặc dữ liệu nội bộ. Vì vậy, hệ thống cần xác thực người dùng và hạn chế quyền truy cập vào dữ liệu của người khác.

---

## 3. MỤC TIÊU DỰ ÁN

### 3.1. Mục tiêu tổng quát

Xây dựng một hệ thống hỏi đáp tài liệu thông minh trên AWS, cho phép người dùng tải tài liệu, đặt câu hỏi và nhận câu trả lời dựa trên nội dung của tài liệu đó.

### 3.2. Mục tiêu cụ thể

* Xây dựng chức năng đăng ký và đăng nhập người dùng bằng Amazon Cognito.
* Lưu trữ tài liệu tải lên trên Amazon S3.
* Sử dụng Amazon Textract để trích xuất văn bản từ tài liệu.
* Chia văn bản thành các đoạn nhỏ để phục vụ tìm kiếm.
* Sử dụng Amazon Titan Embeddings V2 để tạo vector embedding 1.024 chiều.
* Lưu trữ vector trong Amazon RDS PostgreSQL bằng extension `pgvector`.
* Tìm kiếm các đoạn tài liệu liên quan theo ngữ nghĩa.
* Sử dụng Amazon Bedrock để tạo câu trả lời dựa trên nội dung tìm được.
* Lưu trữ lịch sử trò chuyện trong Amazon DynamoDB.
* Xây dựng các Lambda thực hiện Create, Read, Update và Delete lịch sử trò chuyện.
* Kiểm thử hiệu năng tìm kiếm vector và truy vấn lịch sử chat.
* Xây dựng hệ thống có khả năng mở rộng và kiểm soát chi phí.

---

## 4. ĐỐI TƯỢNG SỬ DỤNG

Hệ thống hướng tới các nhóm người dùng sau:

* Sinh viên cần tra cứu giáo trình và tài liệu học tập.
* Giảng viên cần tìm kiếm thông tin trong tài liệu giảng dạy.
* Nhân viên doanh nghiệp cần tra cứu tài liệu nội bộ.
* Nhóm nghiên cứu cần tổng hợp thông tin từ nhiều tài liệu.
* Người dùng cá nhân cần hỏi đáp nhanh về nội dung PDF.

---

## 5. KIẾN TRÚC GIẢI PHÁP

### 5.1. Luồng xử lý tài liệu

1. Người dùng đăng ký hoặc đăng nhập thông qua Amazon Cognito.
2. Người dùng tải tài liệu lên hệ thống.
3. Tài liệu được lưu trữ trên Amazon S3.
4. Amazon Textract được kích hoạt để trích xuất văn bản từ tài liệu (PDF/Hình ảnh).
5. Khi hoàn tất, Textract phát tín hiệu qua Amazon SNS để tự động kích hoạt (trigger) hàm AWS Lambda.
6. AWS Lambda nhận sự kiện, lấy toàn bộ văn bản từ Textract và tiến hành chia nhỏ nội dung thành các đoạn (chunk).
7. Lambda gửi từng đoạn văn bản này đến Amazon Bedrock (mô hình amazon.titan-embed-text-v2:0) để chuyển hóa thành vector embedding 1.024 chiều.
8. Nội dung từng đoạn (chunk) và vector embedding tương ứng được lưu vào cơ sở dữ liệu Amazon RDS PostgreSQL (thông qua extension pgvector).
9. Các thông tin cơ bản của tài liệu (metadata) được lưu trữ vào Amazon DynamoDB để quản lý.

### 5.2. Luồng hỏi đáp

1. Người dùng nhập câu hỏi trên giao diện.
2. Câu hỏi được gửi đến backend thông qua Amazon API Gateway để gọi hàm AWS Lambda.
3. Hàm Lambda gửi câu hỏi đến Amazon Bedrock để chuyển hóa thành dạng vector embedding.
4. Hàm Lambda sử dụng vector của câu hỏi để thực hiện truy vấn tìm kiếm sự tương đồng trong Amazon RDS PostgreSQL.
5. Hệ thống trích xuất ra top các đoạn tài liệu (chunks) có nội dung liên quan nhất với câu hỏi.
6. Câu hỏi của người dùng và các đoạn tài liệu ngữ cảnh vừa tìm được sẽ được đóng gói và gửi đến mô hình ngôn ngữ lớn (LLM) trên Amazon Bedrock (amazon.nova-lite-v1:0).
7. Mô hình AI tổng hợp thông tin và tự động tạo ra câu trả lời chính xác dựa trên ngữ cảnh đã cung cấp.
8. Câu hỏi và câu trả lời được lưu vào Amazon DynamoDB.
9. Kết quả được trả về giao diện người dùng.

---

## 6. CÁC DỊCH VỤ AWS ĐƯỢC SỬ DỤNG

| Dịch vụ AWS           | Vai trò trong hệ thống                                   |
| --------------------- | -------------------------------------------------------- |
| AWS Amplify | Lưu trữ (hosting) và tự động triển khai (deploy) ứng dụng giao diện Frontend (React). Tích hợp sẵn mạng phân phối CDN toàn cầu và tự động quản lý chứng chỉ bảo mật HTTPS. |
| Amazon Cognito        | Quản lý đăng ký, đăng nhập và xác thực người dùng        |
| Amazon S3             | Lưu trữ tài liệu được người dùng tải lên                 |
| Amazon Textract       | Trích xuất văn bản từ PDF và hình ảnh                    |
| Amazon SNS           | Nhận thông báo hoàn thành từ Textract và kích hoạt (trigger) hàm Lambda backend    |
| AWS Lambda            | Xử lý logic backend, tài liệu, embedding và lịch sử chat |
| Amazon Bedrock        | Tạo vector embedding và sinh câu trả lời                 |
| Amazon RDS PostgreSQL | Lưu trữ tài liệu, đoạn nội dung và vector embedding      |
| pgvector              | Hỗ trợ lưu trữ và tìm kiếm vector trong PostgreSQL       |
| Amazon DynamoDB       | Lưu trữ lịch sử trò chuyện                               |
| Amazon API Gateway    | Cung cấp API để frontend gọi các Lambda backend          |
| Amazon CloudWatch     | Theo dõi log và lỗi của Lambda                           |
| AWS IAM               | Quản lý quyền truy cập giữa các dịch vụ AWS              |
| Amazon VPC            | Cung cấp môi trường mạng cho Lambda và RDS               |

---

## 7. THIẾT KẾ CƠ SỞ DỮ LIỆU

### 7.1. Amazon RDS PostgreSQL

RDS PostgreSQL được sử dụng để lưu trữ thông tin tài liệu và vector embedding.

#### Bảng `documents`

Bảng `documents` lưu thông tin tổng quát của tài liệu:

| Thuộc tính    | Mô tả                         |
| ------------- | ----------------------------- |
| `document_id` | Mã định danh tài liệu         |
| `user_id`     | Mã người dùng sở hữu tài liệu |
| `file_name`   | Tên tài liệu                  |
| `file_type`   | Định dạng tài liệu            |
| `status`      | Trạng thái xử lý              |
| `created_at`  | Thời gian tạo                 |

#### Bảng `document_chunks`

Bảng `document_chunks` lưu các đoạn nội dung và vector:

| Thuộc tính        | Mô tả                        |
| ----------------- | ---------------------------- |
| `id`              | Mã định danh đoạn nội dung   |
| `document_id`     | Mã tài liệu                  |
| `user_id`         | Mã người dùng                |
| `file_name`       | Tên tài liệu                 |
| `page_number`     | Số trang                     |
| `chunk_index`     | Vị trí đoạn nội dung         |
| `content`         | Nội dung văn bản             |
| `embedding`       | Vector embedding 1.024 chiều |
| `embedding_model` | Tên mô hình embedding        |
| `metadata`        | Dữ liệu bổ sung              |

Việc tìm kiếm ngữ nghĩa được thực hiện bằng toán tử khoảng cách vector của `pgvector`.

### 7.2. Amazon DynamoDB

Bảng `ChatHistory-dev` được sử dụng để lưu lịch sử trò chuyện.

Các thuộc tính chính bao gồm:

| Thuộc tính   | Mô tả                                   |
| ------------ | --------------------------------------- |
| `userId`     | Mã người dùng                           |
| `sessionId`  | Mã phiên trò chuyện                     |
| `messageId`  | Mã tin nhắn                             |
| `messageKey` | Khóa sắp xếp theo `createdAt#messageId` |
| `role`       | Người gửi, gồm user hoặc assistant      |
| `content`    | Nội dung tin nhắn                       |
| `references` | Nguồn tài liệu tham khảo                |
| `createdAt`  | Thời gian tạo                           |
| `updatedAt`  | Thời gian cập nhật                      |

Cách tổ chức khóa giúp hệ thống truy vấn nhanh lịch sử tin nhắn theo người dùng, phiên trò chuyện và thời gian.

---

## 8. CÁC CHỨC NĂNG CHÍNH

### 8.1. Xác thực người dùng

* Đăng ký tài khoản.
* Xác nhận tài khoản qua email.
* Đăng nhập và đăng xuất.
* Nhận token xác thực từ Amazon Cognito.
* Giới hạn quyền truy cập đối với người dùng chưa đăng nhập.

### 8.2. Quản lý tài liệu

* Tải tài liệu lên Amazon S3.
* Trích xuất văn bản bằng Amazon Textract.
* Chia nội dung thành các đoạn nhỏ.
* Tạo và lưu vector embedding.
* Hiển thị danh sách tài liệu của người dùng.
* Xóa tài liệu không còn sử dụng.

### 8.3. Hỏi đáp tài liệu

* Nhập câu hỏi bằng ngôn ngữ tự nhiên.
* Tìm kiếm các đoạn tài liệu có nội dung tương đồng.
* Tạo câu trả lời dựa trên nội dung tìm được.
* Trả về tên tài liệu, trang và đoạn nội dung tham khảo.
* Hạn chế câu trả lời không có căn cứ trong tài liệu.

### 8.4. Quản lý lịch sử trò chuyện

* Tạo tin nhắn mới.
* Xem lại lịch sử trò chuyện.
* Cập nhật nội dung tin nhắn.
* Xóa tin nhắn.
* Phân chia lịch sử theo người dùng và phiên chat.

---

## 9. PHẠM VI CÔNG VIỆC PHỤ TRÁCH

Trong dự án nhóm, phần công việc được phụ trách tập trung vào cơ sở dữ liệu, Lambda và kiểm thử hiệu năng:

* Khởi tạo Amazon RDS PostgreSQL.
* Cấu hình VPC và Security Group cho RDS.
* Kích hoạt extension `pgvector`.
* Xây dựng bảng `documents` và `document_chunks`.
* Cấu hình trường embedding với kích thước 1.024 chiều.
* Tạo bảng DynamoDB `ChatHistory-dev`.
* Thiết kế khóa phục vụ truy vấn lịch sử trò chuyện.
* Phát triển Lambda Create tin nhắn.
* Phát triển Lambda Get History.
* Phát triển Lambda Update tin nhắn.
* Phát triển Lambda Delete tin nhắn.
* Xây dựng Lambda tạo dữ liệu vector thử nghiệm.
* Xây dựng Lambda tìm kiếm vector.
* Tích hợp Amazon Titan Embeddings V2.
* Tạo dữ liệu giả cho RDS và DynamoDB.
* Đo và phân tích hiệu năng truy vấn.
* Cấu hình Amazon Cognito User Pool và kiểm thử đăng nhập.
* Tổng hợp kết quả và xây dựng báo cáo.

Các thành viên khác trong nhóm phụ trách frontend, lưu trữ S3, trích xuất tài liệu bằng Textract và các thành phần tích hợp còn lại.

---

## 10. KẾ HOẠCH THỰC HIỆN

| Giai đoạn   | Thời gian          | Nội dung   chính                                              |
| ----------- | ------------------ | ----------------------------------------------------------- |
| Giai đoạn 1 | 22/06 - 28/06/2026 | Tìm hiểu AWS, IAM, Global Infrastructure và quản lý chi phí |
| Giai đoạn 2 | 29/06 - 05/07/2026 | Nghiên cứu các dịch vụ AWS cốt lõi                          |
| Giai đoạn 3 | 06/07 - 12/07/2026 | Phân tích yêu cầu và thiết kế kiến trúc                     |
| Giai đoạn 4 | 13/07 - 19/07/2026 | Khởi tạo RDS, pgvector và DynamoDB                          |
| Giai đoạn 5 | 20/07 - 26/07/2026 | Phát triển các Lambda CRUD                                  |
| Giai đoạn 6 | 27/07 - 02/08/2026 | Tích hợp Bedrock, tạo embedding và dữ liệu kiểm thử         |
| Giai đoạn 7 | 03/08 - 09/08/2026 | Đo hiệu năng và cấu hình Cognito                            |
| Hoàn thiện  | 10/08 - 15/08/2026 | Tích hợp, tổng hợp kết quả và viết báo cáo                  |

---

## 11. KẾT QUẢ KIỂM THỬ

### 11.1. Tìm kiếm vector trên RDS

* Tổng số vector thử nghiệm: **2.000**
* Số lượt warm-up: **5**
* Số lượt kiểm thử: **50**
* Số kết quả mỗi truy vấn: **5**
* Độ trễ nhỏ nhất: **49,561 ms**
* Độ trễ lớn nhất: **60,079 ms**
* Độ trễ trung bình: **50,398 ms**
* Trung vị: **49,998 ms**
* P95: **50,519 ms**
* Kết quả mong đợi được tìm thấy: **Đúng**

### 11.2. Truy vấn lịch sử chat trên DynamoDB

* Số phiên trò chuyện: **100**
* Số tin nhắn mỗi phiên: **20**
* Số lượt truy vấn: **100**
* Số lượt thành công: **100**
* Số lỗi: **0**
* Độ trễ nhỏ nhất: **16,797 ms**
* Độ trễ lớn nhất: **238,972 ms**
* Độ trễ trung bình: **35,172 ms**
* Trung vị: **38,423 ms**
* P95: **58,278 ms**
* Tỷ lệ lỗi: **0%**

Kết quả cho thấy hệ thống có thể tìm kiếm vector chính xác và truy vấn lịch sử trò chuyện ổn định trong phạm vi dữ liệu thử nghiệm.

---

## 12. CHI PHÍ VÀ PHƯƠNG ÁN KIỂM SOÁT

Hệ thống sử dụng kết hợp dịch vụ Serverless và Amazon RDS PostgreSQL.

Các dịch vụ như Lambda, DynamoDB và S3 được tính phí theo lượng sử dụng. Amazon Bedrock được tính phí theo số lượng token đầu vào và đầu ra. Amazon Textract được tính theo số trang tài liệu được xử lý.

Amazon RDS là tài nguyên phát sinh chi phí trong thời gian database hoạt động, ngay cả khi không có truy vấn. Vì vậy, dự án áp dụng các biện pháp:

* Sử dụng AWS Credits và Free Tier khi phù hợp.
* Dừng RDS khi không thực hiện kiểm thử.
* Xóa snapshot và tài nguyên không cần thiết.
* Theo dõi chi phí bằng AWS Cost Explorer.
* Thiết lập cảnh báo bằng AWS Budgets.
* Giới hạn số lượng tài liệu và lượt gọi Bedrock trong môi trường demo.
* Kiểm tra chi phí theo từng dịch vụ định kỳ.

---

## 13. RỦI RO VÀ GIẢI PHÁP

| Rủi ro                            | Mức độ     | Giải pháp                                                       |
| --------------------------------- | ---------- | --------------------------------------------------------------- |
| Lambda không kết nối được RDS     | Cao        | Kiểm tra VPC, Subnet, Security Group và quyền IAM               |
| Thiếu thư viện kết nối PostgreSQL | Trung bình | Đóng gói dependency trong deployment package hoặc Lambda Layer  |
| Sai AWS Region                    | Trung bình | Thống nhất sử dụng `us-east-1` cho các tài nguyên liên quan     |
| Vector không đúng 1.024 chiều     | Cao        | Kiểm tra kích thước embedding trước khi lưu                     |
| Câu trả lời không đúng tài liệu   | Cao        | Chỉ cung cấp ngữ cảnh đã truy xuất và yêu cầu trả lời kèm nguồn |
| Rò rỉ dữ liệu giữa người dùng     | Cao        | Xác thực bằng Cognito và lọc dữ liệu theo `user_id`             |
| Phát sinh chi phí RDS             | Trung bình | Dừng database khi không sử dụng và thiết lập AWS Budgets        |
| Lambda bị timeout                 | Trung bình | Tối ưu truy vấn, giới hạn dữ liệu và cấu hình timeout phù hợp   |
| Mất dữ liệu lịch sử chat          | Trung bình | Thiết kế khóa DynamoDB rõ ràng và kiểm thử CRUD                 |
| Thiếu quyền IAM                   | Trung bình | Áp dụng nguyên tắc least privilege và kiểm tra CloudWatch Logs  |

---

## 14. KẾT QUẢ MONG ĐỢI

Sau khi hoàn thành, hệ thống cần đáp ứng các yêu cầu:

* Người dùng có thể đăng ký và đăng nhập.
* Người dùng có thể tải tài liệu lên hệ thống.
* Hệ thống có thể trích xuất và xử lý nội dung tài liệu.
* Nội dung được chuyển thành vector embedding 1.024 chiều.
* Vector được lưu trong PostgreSQL bằng `pgvector`.
* Người dùng có thể đặt câu hỏi về tài liệu.
* Hệ thống trả về câu trả lời dựa trên nội dung được tìm thấy.
* Câu trả lời có thông tin nguồn tham khảo.
* Lịch sử trò chuyện được lưu và truy vấn theo người dùng.
* Các chức năng CRUD hoạt động chính xác.
* Hệ thống có số liệu kiểm thử về độ trễ và tính chính xác.
* Chi phí tài nguyên được theo dõi và kiểm soát.

---

## 15. SẢN PHẨM BÀN GIAO

* Kiến trúc tổng thể hệ thống trên AWS.
* Amazon RDS PostgreSQL có extension `pgvector`.
* Các bảng `documents` và `document_chunks`.
* Bảng DynamoDB `ChatHistory-dev`.
* Bộ Lambda CRUD lịch sử trò chuyện.
* Lambda tạo dữ liệu vector thử nghiệm.
* Lambda tìm kiếm vector.
* Cấu hình Amazon Cognito User Pool.
* Kết quả kiểm thử RDS và DynamoDB.
* Mã nguồn dự án.
* Tài liệu hướng dẫn triển khai.
* Website báo cáo song ngữ Việt - Anh.

---

## 16. KẾT LUẬN

Smart Document Chatbot giải quyết nhu cầu tìm kiếm và hỏi đáp thông tin trong tài liệu bằng cách kết hợp các dịch vụ AWS với kiến trúc RAG.

Dự án giúp giảm thời gian tìm kiếm thủ công, nâng cao khả năng truy xuất thông tin theo ngữ nghĩa và quản lý lịch sử trò chuyện một cách hiệu quả.

Thông qua dự án, nhóm có thể áp dụng kiến thức về Cloud Computing, Serverless, Security, Database và Generative AI vào một bài toán thực tế. Hệ thống cũng tạo nền tảng để tiếp tục phát triển thêm các chức năng như hỗ trợ nhiều định dạng tài liệu, quản lý tài liệu theo người dùng, cải thiện trích dẫn nguồn và tối ưu hiệu năng trong tương lai.
