---
title: "Triển khai Frontend"
date : 2024-01-01
weight : 4
chapter: false
pre: " <b> 5.3. </b> "
---

Trong phần này, nhóm triển khai giao diện người dùng (Frontend) của ứng dụng **SmartDocs AI**. Quá trình này tập trung vào việc xây dựng một Single Page Application (SPA) mượt mà, quản lý xác thực bảo mật và thiết lập luồng giao tiếp tối ưu với hệ thống Backend Serverless trên AWS.

### 1. Kiến trúc Ứng dụng (SPA & State Management)
*   **Công nghệ cốt lõi:** Ứng dụng được phát triển bằng **React**, sử dụng `react-router-dom` để điều hướng trang (Landing Page, Chat Panel) không độ trễ, không cần tải lại trình duyệt. Giao diện được thiết kế bằng **Tailwind CSS**.
*   **Quản lý trạng thái:** Sử dụng Context API (`AuthContext`) để lưu trữ trạng thái đăng nhập toàn cục. Các dữ liệu phiên chat, tin nhắn và tài liệu được quản lý cục bộ bằng các React Hooks (`useState`, `useEffect`).

### 2. Luồng Xác thực & Bảo mật (Authentication Flow)
*   **Giao diện tùy chỉnh (Custom UI):** Hệ thống không dùng giao diện mặc định mà tự xây dựng form đăng nhập/đăng ký (`AuthModal`), tích hợp ngầm với AWS Cognito thông qua thư viện `aws-amplify/auth` (các hàm `signIn`, `signUp`).
*   **Quản lý JWT Token nghiêm ngặt:** Để bảo mật, Frontend được cấu hình từ chối lưu token trong `localStorage`. Mọi thông tin xác thực được ép lưu vào **`sessionStorage`**. Điều này đảm bảo phiên đăng nhập sẽ tự động bị hủy và xóa sạch dữ liệu ngay khi người dùng đóng trình duyệt hoặc tắt tab.

### 3. Giao tiếp API (API Integration & Authorization)
*   **Đính kèm Token:** Mọi yêu cầu HTTP (gửi tin nhắn, tạo phiên, tải/xóa tài liệu) gửi đến hệ thống API Gateway đều tự động trích xuất token từ bộ nhớ và đính kèm vào phần Header (`Authorization: <token>`).
*   **Xử lý hỏi đáp (Chat):** Khi người dùng đặt câu hỏi, Frontend truyền dữ liệu (câu hỏi, danh sách `documentIds` đang được tích chọn, `sessionId`) xuống API. Trong lúc chờ Backend phản hồi, giao diện hiển thị trạng thái chờ (thinking) để tăng trải nghiệm người dùng.

### 4. Luồng Tải lên Tài liệu (Direct S3 Upload)
Nhằm giảm tải cho API Gateway và tăng tốc độ tải file, Frontend áp dụng luồng tải lên 2 bước (Pre-signed URL):
1.  **Xin quyền:** Gọi API lên AWS để yêu cầu cấp một đường dẫn tải lên tạm thời (Upload URL) cho file cụ thể.
2.  **Tải trực tiếp:** Sử dụng đường dẫn vừa nhận, Frontend đẩy trực tiếp file vật lý (PDF, ảnh) lên thẳng Amazon S3 bucket. Ngay khi thành công, giao diện tự động cập nhật (gắn tài liệu vào danh sách hiển thị tạm) mà không cần tải lại trang.

### 5. Triển khai Hosting Tĩnh
*   Mã nguồn React được "build" thành các tệp tĩnh (HTML, JS, CSS) tối ưu hóa dung lượng.
*   Các tệp này được lưu trữ và phân phối thông qua nền tảng Hosting (như AWS S3 tĩnh kết hợp CloudFront CDN, hoặc Vercel), giúp ứng dụng có tốc độ truy cập nhanh chóng và ổn định cho người dùng cuối.