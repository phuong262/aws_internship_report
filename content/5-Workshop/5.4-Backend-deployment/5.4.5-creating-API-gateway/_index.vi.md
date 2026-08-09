---
title : "Thiết lập Amazon API Gateway"
weight : 5
chapter : false
pre : " <b> 5.4.5 </b> "
---

### 1. Khởi tạo Amazon API Gateway
Trong dự án này, Amazon API Gateway được thiết lập đóng vai trò là điểm tiếp nhận và điều phối trung tâm cho toàn bộ hệ thống.
*   **Thông tin khởi tạo:** Hệ thống sử dụng giao thức REST API với tên gọi là `AWS_test`.
*   **Loại Endpoint:** Cấu hình Endpoint type được đặt ở dạng Regional để tối ưu hóa truy cập trong cùng khu vực triển khai.

### 2. Cấu hình Tài nguyên (Resources) và Phương thức (Methods)
Hệ thống API được thiết kế phân chia rõ ràng theo từng nghiệp vụ, bao gồm các tài nguyên và phương thức thao tác dữ liệu cụ thể:
*   **Resource `/chat`:** Xử lý các tác vụ liên quan đến trò chuyện. Hỗ trợ phương thức `GET` để lấy dữ liệu và `POST` để gửi nội dung.
*   **Resource `/delete-chat`:** Cung cấp phương thức `DELETE` để xóa đoạn chat cụ thể.
*   **Resource `/delete-document`:** Cung cấp phương thức `DELETE` để xử lý việc gỡ bỏ tài liệu khỏi hệ thống.
*   **Resource `/delete-session`:** Hỗ trợ phương thức `DELETE` để dọn dẹp các phiên hoạt động.
*   **Resource `/upload`:** Cung cấp phương thức `POST` để tiếp nhận dữ liệu tải lên từ phía người dùng.

### 3. Cấu hình CORS (Cross-Origin Resource Sharing)
Để đảm bảo Frontend có thể gọi API một cách trơn tru mà không bị trình duyệt chặn, cấu hình CORS đã được tích hợp đồng bộ:
*   Đã thiết lập phương thức `OPTIONS` trên tất cả các endpoint quan trọng bao gồm `/chat`, `/delete-document`, `/delete-session`, và `/upload`. 
*   Phương thức `OPTIONS` ở thư mục gốc (`/`) được cấu hình với Integration type là Mock và Authorization là None, giúp phản hồi nhanh chóng các yêu cầu preflight từ client.

### 4. Triển khai (Deployment) và Giới hạn lưu lượng
Sau khi hoàn tất cấu hình, API đã được triển khai (deploy) thành công lên môi trường thực tế:
*   **Stage triển khai:** Đã tạo và deploy toàn bộ cấu hình lên môi trường có tên `devv1`.
*   **Invoke URL:** Cung cấp đường dẫn gốc (Base URL) cho Frontend tích hợp là `https://wzie3iseyk.execute-api.ap-southeast-1.amazonaws.com/devv1`.
*   **Bảo vệ hệ thống:** Đã cấu hình các thông số giới hạn lưu lượng để chống quá tải (Throttling) với Rate limit là 10,000 và Burst limit là 5,000.