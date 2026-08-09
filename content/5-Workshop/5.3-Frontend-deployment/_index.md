---
title: "Frontend Implementation"
date : 2024-01-01
weight : 4
chapter: false
pre: " <b> 5.3. </b> "
---

In this section, the team implements the user interface (Frontend) for the **SmartDocs AI** application. The process focuses on building a seamless Single Page Application (SPA), managing secure authentication, and establishing an optimized communication flow with the serverless backend system on AWS.

### 1. Application Architecture (SPA & State Management)
*   **Core Technologies:** The application is developed using **React**, utilizing `react-router-dom` for seamless page navigation (Landing Page, Chat Panel) without browser reloads. The interface is styled using **Tailwind CSS**.
*   **State Management:** The Context API (`AuthContext`) is used to manage global authentication state. Chat session data, messages, and documents are managed locally via React Hooks (`useState`, `useEffect`).

### 2. Authentication Flow & Security
*   **Custom UI:** Instead of using default interfaces, the system implements custom login/registration forms (`AuthModal`) that integrate directly with AWS Cognito via the `aws-amplify/auth` library (using `signIn` and `signUp` functions).
*   **Strict JWT Token Management:** For security purposes, the frontend is configured to avoid storing tokens in `localStorage`. All authentication data is stored in **`sessionStorage`**. This ensures that the login session is automatically terminated and data is cleared immediately when the user closes the browser or the tab.

### 3. API Communication (Integration & Authorization)
*   **Token Attachment:** Every HTTP request sent to the API Gateway (e.g., sending messages, creating sessions, uploading/deleting documents) automatically retrieves the token from storage and attaches it to the request header (`Authorization: <token>`). *   **Q&A Processing (Chat):** When a user submits a question, the frontend transmits the data (the question, the list of selected `documentIds`, and the `sessionId`) to the API. While awaiting the backend response, the interface displays a "thinking" status to enhance the user experience.

### 4. Document Upload Flow (Direct S3 Upload)
To reduce the load on the API Gateway and accelerate file upload speeds, the frontend employs a two-step upload process (Pre-signed URL):
1.  **Requesting Authorization:** A call is made to the AWS API to request a temporary upload URL for the specific file.
2.  **Direct Upload:** Using the received URL, the frontend uploads the physical file (PDF, image) directly to the Amazon S3 bucket. Upon successful upload, the interface automatically updates (adding the document to the temporary display list) without requiring a page reload.

### 5. Static Hosting Deployment
*   The React source code is built into size-optimized static files (HTML, JS, CSS).
*   These files are stored and distributed via a hosting platform (such as AWS S3 static hosting combined with CloudFront CDN, or Vercel), ensuring fast and stable access speeds for end-users.