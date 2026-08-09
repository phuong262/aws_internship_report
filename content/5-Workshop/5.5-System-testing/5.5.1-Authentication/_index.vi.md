---
title : "Kiểm thử xác thực"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 5.5.1 </b> "
---

### Mục tiêu kiểm thử

Phần kiểm thử này nhằm đánh giá luồng đăng ký, xác nhận email, đăng nhập và đăng xuất của Smart Docs AI. Hệ thống sử dụng Amazon Cognito để quản lý tài khoản, kiểm tra chính sách mật khẩu và xác thực người dùng trước khi cho phép truy cập trang trò chuyện.

### Các bước thực hiện

Đầu tiên, người dùng mở trang đăng nhập và thử đăng nhập bằng thông tin không chính xác. Hệ thống từ chối yêu cầu và hiển thị thông báo lỗi phù hợp.

Tiếp theo, người dùng chuyển sang chức năng đăng ký. Khi nhập mật khẩu chưa có ký tự đặc biệt, hệ thống không tạo tài khoản mà yêu cầu mật khẩu đáp ứng đúng chính sách. Sau khi nhập mật khẩu hợp lệ, một mã xác nhận gồm 6 chữ số được gửi tới email đã đăng ký.

Người dùng nhập mã xác nhận để kích hoạt tài khoản, sau đó đăng nhập lại bằng thông tin vừa tạo. Khi xác thực thành công, hệ thống chuyển tới trang chat. Cuối cùng, người dùng đăng xuất và được đưa trở lại giao diện yêu cầu đăng nhập.

### Kết quả kiểm thử

| Mã | Trường hợp kiểm thử | Kết quả mong đợi | Kết quả thực tế | Trạng thái |
|---|---|---|---|---|
| AUTH-01 | Đăng nhập sai tên tài khoản hoặc mật khẩu | Từ chối đăng nhập và hiển thị lỗi | Hiển thị `Incorrect username or password.` | Pass |
| AUTH-02 | Đăng ký bằng mật khẩu thiếu ký tự đặc biệt | Không tạo tài khoản và thông báo yêu cầu bổ sung ký tự | Hiển thị `Password must have symbol characters` | Pass |
| AUTH-03 | Đăng ký bằng thông tin hợp lệ | Gửi mã xác nhận tới email đăng ký | Giao diện xác thực email yêu cầu mã gồm 6 chữ số | Pass |
| AUTH-04 | Nhập mã xác nhận hợp lệ | Kích hoạt tài khoản thành công | Tài khoản được xác nhận và có thể đăng nhập | Pass |
| AUTH-05 | Đăng nhập bằng tài khoản đã xác nhận | Chuyển tới giao diện chính | Trang `/chat` được hiển thị đầy đủ | Pass |
| AUTH-06 | Đăng xuất khỏi hệ thống | Kết thúc phiên và trở lại trạng thái chưa đăng nhập | Người dùng được đưa về giao diện đăng nhập | Pass |

### Nhận xét

Luồng xác thực hoạt động đúng với các trường hợp đã kiểm tra. Hệ thống không chỉ xử lý đăng nhập thành công mà còn phản hồi rõ ràng khi thông tin đăng nhập hoặc mật khẩu đăng ký không hợp lệ. Bước xác nhận email giúp bảo đảm tài khoản được tạo bởi người có quyền truy cập địa chỉ email tương ứng. Sau khi đăng xuất, phiên làm việc được kết thúc và người dùng phải đăng nhập lại để tiếp tục sử dụng hệ thống.

Trong quá trình lưu bằng chứng kiểm thử, mật khẩu, mã xác nhận và địa chỉ email cần được che để tránh làm lộ thông tin cá nhân.
