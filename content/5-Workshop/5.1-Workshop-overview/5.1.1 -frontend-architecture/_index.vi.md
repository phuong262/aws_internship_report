---
title : "Kiến trúc Frontend"
date : 2024-01-01 
weight : 1
chapter : false
pre : " <b> 5.1.1 </b> "
---

### 1. Giải thích cấu trúc thư mục Frontend

#### 1.1. Cấu trúc tổng quan

```
smart-docs-ai/                         # Thư mục gốc của frontend (Vite + React + TypeScript)
├── .bolt/                              # Cấu hình cho công cụ AI builder (Bolt), không thuộc mã nguồn runtime
│   ├── config.json
│   └── prompt
│
├── src/
│   ├── components/                    # Các component giao diện dùng chung cho tính năng chat
│   │   ├── AuthCorner.tsx             # Góc phải header: nút đăng nhập/đăng ký hoặc info user + đăng xuất
│   │   ├── AuthModal.tsx              # Modal xác thực (đăng nhập / đăng ký / xác thực OTP)
│   │   ├── ChatPanel.tsx              # Khung hội thoại chính: danh sách tin nhắn + ô nhập câu hỏi
│   │   └── Sidebar.tsx                # Sidebar: tạo phiên, upload tài liệu, danh sách tài liệu & lịch sử phiên
│   │
│   ├── context/                       # React Context API
│   │   └── AuthContext.tsx            # Quản lý trạng thái đăng nhập (AWS Amplify/Cognito trực tiếp)
│   │
│   ├── lib/                           # Utility & cấu hình dùng chung
│   │   ├── qa.ts                      # Module hỏi-đáp trích xuất (extractive Q&A) chạy phía client
│   │   └── supabase.ts                # Khởi tạo Supabase client + định nghĩa type (Session, DocumentRow, Message)
│   │
│   ├── App.tsx                        # Root component: Routes, AuthProvider, layout Header/Sidebar/ChatPanel
│   ├── index.css                      # Global styles + Tailwind directives
│   └── main.tsx                       # Entry point: cấu hình AWS Amplify (Cognito), mount React + BrowserRouter
│
├── supabase/
│   └── migrations/
│       └── 20260806054213_create_qa_ap...   # Migration SQL (bảng liên quan tới qa/answers phía Supabase)
│
├── .env.example                       # Template biến môi trường (VITE_SUPABASE_URL, VITE_SUPABASE_ANON_KEY...)
├── .gitignore
├── README.md
├── favicon.svg
├── index.html                         # HTML entry point (Vite)
├── package-lock.json
├── package.json
├── tsconfig.app.json
├── tsconfig.json
├── tsconfig.node.json
├── vercel.json                        # Cấu hình deploy trên Vercel
└── vite.config.ts                     # Vite config (path alias @/, build options)
```

---

#### 1.2. Giải thích chi tiết từng thư mục

##### `src/components/`
Các component UI phục vụ trực tiếp cho tính năng RAG chat, không tách theo Feature-Sliced Design mà gộp phẳng trong một thư mục:

- **`AuthCorner.tsx`**: Hiển thị ở góc phải header. Nếu đã đăng nhập: avatar chữ cái + email + nút "Đăng xuất" (`handleLogout` xóa sạch `localStorage`/`sessionStorage` rồi gọi `signOut()` từ `AuthContext`, sau đó `window.location.replace('/')` để không lưu lại lịch sử điều hướng). Nếu chưa đăng nhập: 2 nút "Đăng nhập" / "Đăng ký" mở `AuthModal`.

- **`AuthModal.tsx`**: Modal dùng chung cho 3 chế độ `signin` / `signup` / `verify`, gọi trực tiếp các hàm của **AWS Amplify Auth** (`signIn`, `signUp`, `confirmSignUp`, `getCurrentUser`) — không qua Redux thunk. Sau khi đăng nhập thành công, lưu thông tin user vào `sessionStorage` (`smartdocs-auth-user`), bắn custom event `smartdocs:auth-changed` để đồng bộ các component khác, rồi điều hướng sang `/chat`.

- **`ChatPanel.tsx`**: Khu vực hội thoại chính. Hiển thị danh sách tin nhắn (`MessageBubble`, `Avatar`), trạng thái đang xử lý ("Đang tìm trong tài liệu..."), `EmptyState` thay đổi nội dung tùy theo đã có phiên/tài liệu hay chưa. Ô nhập liệu hỗ trợ gửi bằng `Enter` (giữ `Shift+Enter` để xuống dòng), khóa input khi chưa có phiên hoạt động (`hasSession`).

- **`Sidebar.tsx`**: Gồm 3 khối chính:
  - Nút "Phiên mới" tạo session.
  - Khu vực upload tài liệu (ảnh/PDF): gọi thẳng API Gateway (`POST /upload`) để lấy `uploadUrl` (presigned URL S3), sau đó `PUT` file trực tiếp lên S3; tài liệu vừa tải được ghim tạm vào state `recentDocs` để hiển thị ngay trước khi đồng bộ lại từ server.
  - Danh sách tài liệu có checkbox chọn (`selectedDocIds`) để giới hạn phạm vi tài liệu dùng làm ngữ cảnh khi hỏi đáp, kèm nút xóa từng tài liệu; và danh sách lịch sử phiên với menu xóa phiên (có modal xác nhận).

##### `src/context/AuthContext.tsx`
Thay vì Redux, project dùng **React Context + AWS Amplify Auth trực tiếp**:
- Lưu user hiện tại vào `sessionStorage` (mỗi tab có phiên độc lập, tránh xung đột multi-tab).
- Khi mount, nếu `sessionStorage` trống (mở tab mới/trình duyệt mới), chủ động gọi `amplifySignOut()` để xóa token ẩn còn sót trong `localStorage` của Amplify — ngăn tình trạng "tự động đăng nhập ngầm".
- Expose `user`, `loading`, `signIn`, `signUp`, `signOut` qua hook `useAuth()`.
- Đồng bộ giữa các tab/component qua custom event `smartdocs:auth-changed` và sự kiện `storage`.

##### `src/lib/`
- **`qa.ts`**: Bộ máy hỏi-đáp trích xuất (extractive Q&A) chạy hoàn toàn phía client — tokenize tiếng Việt có loại bỏ dấu câu/stopword, tách văn bản tài liệu thành câu, chấm điểm từng câu theo độ trùng khớp từ khóa với câu hỏi (có xử lý match từng phần cho tiếng Việt), rồi trả về 1–2 đoạn phù hợp nhất. Đây là lớp trả lời dự phòng/độc lập, tách biệt với pipeline RAG chính chạy trên AWS (Bedrock + pgvector).
- **`supabase.ts`**: Khởi tạo Supabase client (dùng `sessionStorage` làm nơi lưu session) và định nghĩa các type dùng chung trong app: `Session`, `DocumentRow`, `Message`. Phần lớn nghiệp vụ chat/upload thực tế lại gọi thẳng **AWS API Gateway**, nên Supabase ở đây chủ yếu phục vụ typing và làm phương án dự phòng khi có cấu hình.

##### `src/App.tsx`
Root component của toàn app:
- Bọc `AuthProvider`, định nghĩa `Routes`: `/` (landing), `/chat`, `/chat/:sessionId`, wildcard redirect về `/`.
- `AppInner` giữ toàn bộ state nghiệp vụ (`sessions`, `messages`, `documents`, `selectedDocIds`) và đồng bộ hai chiều với `sessionStorage` để cache dữ liệu cục bộ theo tab.
- Không có lớp service/axios riêng — các lệnh gọi API tới AWS API Gateway (`https://wzie3iseyk.execute-api.ap-southeast-1.amazonaws.com/devv1/...`) được viết trực tiếp bằng `fetch` ngay trong component, kèm header `Authorization` lấy từ `sessionStorage.getItem("user_token")`.
- Các hàm chính: `loadSessions`, `loadDocuments`, tải chi tiết tin nhắn theo `activeSessionId`, `handleNewSession`, `handleDeleteSession`, `handleDeleteDoc`, `handleAsk` (gửi câu hỏi kèm `documentIds` đã chọn).
- `Header` + `LoggedOutHero` hiển thị khi chưa đăng nhập; `AppInner` (Sidebar + ChatPanel) hiển thị khi đã đăng nhập.

##### `src/main.tsx`
Entry point của Vite: cấu hình **AWS Amplify** với `userPoolId` và `userPoolClientId` (Cognito, vùng `ap-southeast-1`), sau đó mount `<App />` bên trong `<BrowserRouter>` và `<StrictMode>`.

##### `supabase/migrations/`
Chứa migration SQL đặt tên theo timestamp (`create_qa_ap...`) — định nghĩa schema liên quan tới câu hỏi/câu trả lời phía Supabase, tồn tại song song với luồng dữ liệu chính chạy qua AWS.

##### `.bolt/`
Thư mục cấu hình của công cụ AI builder (Bolt) dùng khi khởi tạo/scaffold dự án — không ảnh hưởng đến runtime của ứng dụng.

---

### 2. Luồng dữ liệu

```
User Action (trong Sidebar / ChatPanel / AuthModal)
    │
    ▼
Component tự gọi fetch() trực tiếp
    │
    ├──► Với xác thực: AWS Amplify Auth SDK (Cognito)
    │         │
    │         ▼
    │    AuthContext lưu user vào sessionStorage
    │         │
    │         ▼
    │    Bắn event "smartdocs:auth-changed" → đồng bộ AuthCorner, App
    │
    └──► Với nghiệp vụ chat/tài liệu: fetch trực tiếp
              │
              ▼
         Header "Authorization: <user_token>" (lấy từ sessionStorage)
              │
              ▼
         AWS API Gateway (ap-southeast-1) → Lambda
              │
              ▼
         S3 (upload qua presigned URL) / DynamoDB (session, message,
         document metadata) / Bedrock (embedding + sinh câu trả lời) /
         RDS PostgreSQL + pgvector (vector search)
              │
              ▼
         Response JSON
    │
    ▼
setState trong App.tsx / Sidebar.tsx / ChatPanel.tsx
    │
    ▼
React re-render (đồng thời ghi cache vào sessionStorage
qua readLocalState/writeLocalState trong App.tsx)
```

> Project này gọi thẳng `fetch` trong từng component và giữ state bằng `useState`/`useCallback` kết hợp cache `sessionStorage`. Điều này giúp scaffold nhanh nhưng khiến logic gọi API (URL, header, xử lý lỗi) bị lặp lại ở nhiều nơi (`App.tsx`, `Sidebar.tsx`) — có thể cân nhắc tách thành một `api/` hoặc `lib/api.ts` dùng chung nếu dự án mở rộng thêm.