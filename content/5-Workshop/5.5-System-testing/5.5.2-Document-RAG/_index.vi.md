---
title : "Kiểm thử tải tài liệu và RAG"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.5.2 </b> "
---

### Mục tiêu kiểm thử

Phần này kiểm tra luồng xử lý chính của Smart Docs AI, từ tạo và xóa phiên trò chuyện, tải nhiều tài liệu PDF lên hệ thống đến lựa chọn tài liệu và nhận câu trả lời theo đúng nội dung đang được chọn. Kết quả trả về được so sánh trực tiếp với dữ liệu trong từng PDF để đánh giá độ chính xác và khả năng tách biệt ngữ cảnh của cơ chế RAG.

### Dữ liệu sử dụng

Tài liệu kiểm thử là tệp `ke_hoach_gia_dinh_demo.pdf` gồm 3 trang. Nội dung tài liệu mô tả kế hoạch chi tiêu và sinh hoạt của một gia đình, bao gồm ngân sách, các khoản chi, lịch hẹn và kế hoạch đi Vũng Tàu. Toàn bộ tên và số liệu trong tệp đều là dữ liệu giả lập.

Một số thông tin dùng để đối chiếu gồm:

- Tiền dự phòng còn lại: `4.400.000 đồng`.
- Khoản chi lớn nhất: tiền thuê nhà, chiếm khoảng `34%` tổng chi dự kiến.
- Nếu trời mưa lớn ngày `25/07/2026`, chuyến đi Vũng Tàu được chuyển sang ngày `01/08/2026`.

### Các bước thực hiện

1. Đăng nhập vào Smart Docs AI.
2. Chọn **Phiên mới** để tạo một cuộc trò chuyện.
3. Chọn khu vực **Tải tài liệu lên** và tải tệp `ke_hoach_gia_dinh_demo.pdf`.
4. Tải thêm một tài liệu thứ hai có nội dung khác với tài liệu gia đình.
5. Kiểm tra cả hai tài liệu xuất hiện trong danh sách bên trái.
6. Lần lượt chọn từng tài liệu và đặt câu hỏi liên quan đến nội dung của tài liệu đó.
7. Kiểm tra câu trả lời thay đổi theo tài liệu đang được chọn và không lấy nhầm dữ liệu từ tài liệu còn lại.
8. Đặt các câu hỏi liên quan đến số liệu, nội dung tổng hợp và tình huống có điều kiện trong tài liệu gia đình.
9. Đặt thêm câu hỏi có phạm vi không tồn tại trong tài liệu để kiểm tra khả năng hạn chế câu trả lời không có căn cứ.
10. Tạo một phiên trò chuyện thử, sau đó sử dụng chức năng xóa phiên và kiểm tra danh sách lịch sử được cập nhật.
11. Đối chiếu từng phản hồi với nội dung PDF tương ứng.

### Kết quả kiểm thử

| Mã | Câu hỏi hoặc thao tác | Kết quả mong đợi | Kết quả thực tế | Trạng thái |
|---|---|---|---|---|
| DOC-01 | Tạo phiên trò chuyện mới | Phiên mới được tạo và lưu trong lịch sử | Phiên xuất hiện trong mục **Lịch sử phiên** | Pass |
| DOC-02 | Tải tệp PDF lên hệ thống | Tài liệu được tiếp nhận và gắn với phiên | Tệp xuất hiện trong mục **Tài liệu (1)** và có thể được chọn | Pass |
| DOC-03 | Tải hai tài liệu có nội dung khác nhau | Cả hai tệp được lưu và hiển thị trong danh sách tài liệu | Hai tài liệu xuất hiện trong danh sách và có thể được lựa chọn độc lập | Pass |
| DOC-04 | Chọn tài liệu thứ nhất rồi đặt câu hỏi | Câu trả lời chỉ sử dụng nội dung tài liệu thứ nhất | Hệ thống trả lời đúng theo tài liệu thứ nhất đang được chọn | Pass |
| DOC-05 | Chuyển sang tài liệu thứ hai và đặt câu hỏi | Ngữ cảnh được chuyển sang tài liệu thứ hai | Câu trả lời thay đổi theo nội dung tài liệu thứ hai, không lấy nhầm dữ liệu tài liệu trước | Pass |
| DOC-06 | Xóa một phiên trò chuyện | Phiên bị xóa và không còn xuất hiện trong lịch sử | Danh sách **Lịch sử phiên** được cập nhật sau khi xóa | Pass |
| RAG-01 | `Gia đình còn bao nhiêu tiền dự phòng?` | Trả về `4.400.000 đồng` | Trả lời đúng số tiền dự phòng `4.400.000 đồng` | Pass |
| RAG-02 | `Khoản chi nào lớn nhất?` | Xác định tiền thuê nhà và tỷ lệ khoảng 34% | Trả lời tiền thuê nhà chiếm khoảng 34% tổng chi dự kiến | Pass |
| RAG-03 | `Nếu ngày 25/07 trời mưa thì kế hoạch Vũng Tàu thay đổi thế nào?` | Chuyển chuyến đi sang ngày `01/08/2026` | Trả lời đúng ngày thay thế `01/08/2026` | Pass |
| RAG-04 | `Khoản chi nào lớn nhất trong năm qua?` | Không đưa ra thông tin khi tài liệu không có dữ liệu “năm qua” | Trả lời không tìm thấy thông tin trong tài liệu | Pass |

### Nhận xét

Các tài liệu được tải lên và liên kết đúng với phiên trò chuyện. Khi có hai tài liệu, người dùng có thể chuyển đổi tài liệu đang chọn và hệ thống sử dụng đúng nội dung tương ứng để trả lời. Kết quả này cho thấy dữ liệu truy xuất được giới hạn theo tài liệu, tránh trộn lẫn ngữ cảnh giữa các tệp.

Hệ thống trả lời chính xác các câu hỏi yêu cầu truy xuất số liệu, xác định khoản chi lớn nhất và xử lý tình huống có điều kiện. Khi câu hỏi đề cập đến phạm vi “năm qua” không có trong tài liệu, hệ thống không tự suy đoán mà thông báo không tìm thấy thông tin. Chức năng xóa phiên cũng hoạt động đúng và cập nhật ngay danh sách lịch sử trò chuyện.

Kết quả cho thấy cơ chế RAG đã sử dụng đúng nội dung của tài liệu được chọn và hạn chế được việc tạo câu trả lời không có căn cứ. Giao diện duy trì được danh sách tài liệu, hỗ trợ chuyển đổi ngữ cảnh và cho phép quản lý lịch sử phiên bằng chức năng xóa.
