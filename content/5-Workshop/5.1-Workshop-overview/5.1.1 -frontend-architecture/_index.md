---

title : "Frontend Architecture"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 5.1.1 </b> "
------------------------

### 1. Frontend Folder Structure Explanation

#### 1.1. Overall Structure

```
smart-docs-ai/                         # Frontend root directory
├── public/                            # Static assets (favicon, logo...)
│
├── src/
│   ├── api/                           # HTTP configuration & AWS integration
│   │   ├── axiosConfig.js             # Axios instance + interceptor (automatically attaches Bearer token, retries 503/504)
│   │   ├── cognito.js                 # AWS Cognito UserPool connection, JWT token retrieval helper
│   │   └── services/
│   │       ├── smartdocService.js     # API calls: upload, process, chat, files, history...
│   │       └── profileService.js      # API calls: getProfile, updatePersonalInfo, updateAvatar, changePassword
│   │
│   ├── assets/                        # Static assets (images, icons...)
│   │
│   ├── components/                    # Shared/reusable components
│   │   ├── common/                    # Common utility components
│   │   │   ├── Loading.jsx            # Loading spinner
│   │   │   ├── LoadMore.jsx           # Load more button
│   │   │   └── alert/
│   │   │       └── FailedAlert.jsx    # Error alert
│   │   │
│   │   └── ui/                        # Shadcn/ui components (Radix-based)
│   │       ├── alert.jsx
│   │       ├── badge.jsx
│   │       ├── button.jsx
│   │       ├── card.jsx
│   │       ├── dialog.jsx
│   │       ├── dropdown-menu.jsx
│   │       ├── input.jsx
│   │       ├── label.jsx
│   │       ├── progress.jsx
│   │       ├── radio-group.jsx
│   │       ├── select.jsx
│   │       ├── sonner.jsx             # Toaster provider (Sonner)
│   │       ├── spinner.jsx
│   │       ├── switch.jsx
│   │       ├── tabs.jsx
│   │       └── toggle.jsx
│   │
│   ├── contexts/                      # React Context API
│   │   └── ThemeContext.jsx           # Dark/Light mode management (stored in localStorage)
│   │
│   ├── features/                      # Modules organized by feature (Feature-Sliced Design)
│   │   ├── auth/                      # User authentication
│   │   │   ├── components/
│   │   │   │   ├── LoginForm.jsx      # Login form (calls Cognito through Redux thunk)
│   │   │   │   ├── RegisterForm.jsx   # Registration form (calls backend API)
│   │   │   │   └── ProtectedRoute.jsx # Route guard (checks isAuthenticated)
│   │   │   └── pages/
│   │   │       ├── LoginPage.jsx      # Login page
│   │   │       └── RegisterPage.jsx   # Registration page
│   │   │
│   │   ├── profile/                   # User profile
│   │   │   ├── components/
│   │   │   │   ├── ProfileAvatar.jsx  # Upload & display avatar
│   │   │   │   ├── PersonalInfoTab.jsx# Personal information editing tab
│   │   │   │   └── SecurityTab.jsx    # Password change tab
│   │   │   └── pages/
│   │   │       └── ProfilePage.jsx    # Profile page (tabs: Information / Security)
│   │   │
│   │   └── smartdocs/                 # Main feature: RAG Chat with documents
│   │       ├── components/
│   │       │   ├── chat/              # Conversation area
│   │       │   │   ├── ChatArea.jsx           # Main chat container (left/right layout)
│   │       │   │   ├── ChatInput.jsx          # Question input + send button
│   │       │   │   ├── ChatMessage.jsx        # Displays a single message (user / AI)
│   │       │   │   ├── WelcomeHero.jsx        # Welcome screen when there is no chat
│   │       │   │   ├── SearchSettings.jsx     # RAG settings (SelfRAG, CoRAG, top_k...)
│   │       │   │   ├── CoRagMetadata.jsx      # Displays CoRAG metadata
│   │       │   │   ├── SelfRagMetadata.jsx    # Displays SelfRAG metadata
│   │       │   │   └── SourceCitationPanel.jsx# Answer source citation panel
│   │       │   │
│   │       │   ├── layout/            # Overall SmartDoc page layout
│   │       │   │   └── SmartSidebar.jsx       # Main sidebar (collapsible, responsive mobile)
│   │       │   │
│   │       │   └── sidebar/           # Sub-components inside the Sidebar
│   │       │       ├── StatusBadge.jsx        # LLM connection status badge
│   │       │       ├── KpiRow.jsx             # Statistics row (files, pages, chunks)
│   │       │       ├── ChunkSettings.jsx      # chunk_size / chunk_overlap settings
│   │       │       ├── FileUploader.jsx        # Drag-and-drop document upload area
│   │       │       ├── FileCard.jsx           # Card displaying a processed file
│   │       │       ├── FileList.jsx           # List of processed files
│   │       │       ├── ActionButtons.jsx      # Delete document / clear chat history buttons
│   │       │       └── ChatHistoryList.jsx    # Conversation history list
│   │       │
│   │       └── pages/
│   │           └── SmartdocPage.jsx   # Entry page: combines SmartSidebar + ChatArea
│   │
│   ├── lib/                           # Utility functions & helpers
│   │   ├── utils.js                   # classNames utility (cn — combines clsx + tailwind-merge)
│   │   ├── helpers.js                 # Utility functions: formatDate, buildCategoryById, calculatePercent...
│   │   ├── alertUtils.js              # Notification utilities (toast / alert)
│   │   └── goalUtils.js              # Goal calculation utilities
│   │
│   ├── store/                         # Redux Toolkit store
│   │   ├── index.js                   # Store configuration (combineReducers: auth + smartdoc)
│   │   ├── README.md                  # Technical explanation of createSelector / memoization
│   │   └── slices/
│   │       ├── authSlice.js           # Authentication state: login, register, logout, getProfile, user information
│   │       └── smartdocSlice.js       # SmartDoc state: files, chat history, Ollama status, messages
│   │
│   ├── App.jsx                        # Root component: defines Routes, ThemeProvider, Toaster
│   ├── main.jsx                       # Entry point: mounts React to the DOM, wraps BrowserRouter + Redux Provider
│   └── index.css                      # Global styles + Tailwind directives
│
├── .env-example                       # Environment variable template
├── buildspec.yml                      # AWS CodeBuild config (CI/CD)
├── components.json                    # Shadcn/ui configuration
├── eslint.config.js                   # ESLint config
├── index.html                         # HTML entry point (Vite)
├── jsconfig.json                      # Path alias config for IDE
├── package.json
├── tailwind.config.js                 # Tailwind CSS config
├── vite.config.js                     # Vite config (proxy API → AWS API Gateway, path alias @/)
└── README.md
```

---

#### 1.2. Detailed Explanation of Each Directory

##### `src/api/`

The communication layer with the AWS backend.

* **`axiosConfig.js`**: Creates a shared `axiosClient` with `baseURL` obtained from the `VITE_API_BASE_URL` environment variable. It includes 2 important interceptors:

  * **Request interceptor**: Automatically retrieves the JWT `idToken` from the Cognito session and attaches it to the `Authorization: Bearer <token>` header for every request.
  * **Response interceptor**: Automatically retries requests (up to 10 times, with a 5-second delay between each attempt) when encountering `503`, `504`, or timeout errors — this is a mechanism for handling Lambda cold starts.

* **`cognito.js`**: Initializes `CognitoUserPool` with `Storage: sessionStorage` (each browser tab has an independent session, preventing multi-tab conflicts). Exports the `getSessionToken()` function to retrieve the current JWT token.

* **`services/smartdocService.js`**: Contains all API calls for the RAG feature:

  * `checkStatus()` — GET `/api/status` (checks whether the LLM is online)
  * `getUploadUrl()` — POST `/api/upload-url` (gets an S3 presigned URL)
  * `uploadFileToS3()` — PUT directly to S3 through the presigned URL
  * `processDocument()` — POST `/api/process` (chunking + embedding)
  * `getDocuments()` — GET `/api/files`
  * `deleteDocument()` / `clearDocuments()`
  * `sendMessage()` — POST `/api/chat`
  * `getChatHistory()` / `clearChatHistory()`

* **`services/profileService.js`**: API calls for user profile management:

  * `getProfile()` — GET `/api/profile`
  * `updatePersonalInfo()` — PUT `/api/profile/personal-info`
  * `updateAvatar()` — PUT `/api/profile/avatar` (base64, automatically selects DynamoDB or S3 on the backend)
  * `changePassword()` — POST `/api/profile/change-password`

---

##### `src/store/`

Manages global state using **Redux Toolkit**.

The store has 2 main reducers:

* **`authSlice`**: Manages the authentication lifecycle:

  * `login` (async thunk) — Authenticates directly through the Cognito SDK and stores the user in `sessionStorage`
  * `register` (async thunk) — Calls the backend API to create an account and synchronize data with DynamoDB
  * `logout` (async thunk) — Signs out from Cognito + clears the session
  * `getProfile` (async thunk) — Retrieves complete user information from DynamoDB (including avatar)
  * State: `user` (user information), `isAuthenticated`, `loading`, `error`

* **`smartdocSlice`**: Manages the entire state of the RAG feature:

  * `checkOllamaStatus` — Checks the LLM backend status
  * `fetchProcessedFiles` — Retrieves the list of processed documents
  * `fetchChatHistory` — Retrieves conversation history
  * `sendChatMessage` — Sends a question and receives an AI response
  * `uploadAndProcessDocument` — File upload pipeline: gets a presigned URL → uploads to S3 → triggers processing
  * State: `ollamaStatus`, `processedFiles`, `chatHistory`, `messages`, `chunkSettings`...

> See `src/store/README.md` for a detailed explanation of `createSelector` and memoization techniques.

---

##### `src/features/`

Organized according to the **Feature-Sliced Design** pattern — each feature is an independent module with its own `components/` and `pages/`.

**`auth/`** — Authentication:

* `LoginPage` / `RegisterPage`: Public pages that do not require authentication.
* `ProtectedRoute`: HOC that checks `isAuthenticated` from the Redux store; if the user is not authenticated, they are redirected to `/login`.

**`profile/`** — User profile:

* `ProfilePage`: A page with 2 tabs — *Personal Information* and *Security*.
* `ProfileAvatar`: Uploads an avatar (crop, resize, send as base64).
* `PersonalInfoTab`: Edits name, email, phone number, and date of birth.
* `SecurityTab`: Changes the password.

**`smartdocs/`** — Main RAG feature:

* `SmartdocPage`: Main page that combines `SmartSidebar` and `ChatArea` in a 2-column layout.
* `SmartSidebar` (layout): A collapsible sidebar on desktop and an overlay drawer on mobile. Contains all controls: LLM status, file upload, file list, chunking settings, chat history, user information + logout button.
* `ChatArea` (chat): Conversation display area. When there is no chat, it displays `WelcomeHero`. Each AI message may include a `SourceCitationPanel` (source citations) and RAG metadata (`SelfRagMetadata` / `CoRagMetadata`).

---

##### `src/contexts/`

* **`ThemeContext`**: Manages Dark/Light mode. Reads preferences from `localStorage` and the system preference during startup. Toggling the theme applies the `dark` class to `<html>` (compatible with Tailwind dark mode).

---

##### `src/components/`

* **`ui/`**: All components from **Shadcn/ui** (button, card, dialog, select, tabs, toast...). These are primitive components based on Radix UI + Tailwind.
* **`common/`**: Custom utility components used across multiple parts of the application: `Loading`, `LoadMore`, `FailedAlert`.

---

##### `src/lib/`

* **`utils.js`**: Exports the `cn()` function — combines `clsx` and `tailwind-merge` to safely merge Tailwind classes.
* **`helpers.js`**: Utility functions: `formatDateToVNDate`, `buildCategoryById` (O(1) Map), `withCategory`, `calculatePercent`.
* **`alertUtils.js`**: Utility wrapper for displaying toast notifications.
* **`goalUtils.js`**: Utilities for goal-related calculations.

---

### 2. Data Flow

```
User Action
    │
    ▼
Component (dispatch action)
    │
    ▼
Redux Thunk (async thunk in slice)
    │
    ├──► API Service (smartdocService / profileService)
    │         │
    │         ▼
    │    axiosClient (automatically attaches Cognito JWT token)
    │         │
    │         ▼
    │    AWS API Gateway → Lambda → (S3 / DynamoDB / LLM)
    │         │
    │         ▼
    │    Response data
    │
    ▼
Redux Store (state updated)
    │
    ▼
React Component re-render (useSelector)
```
