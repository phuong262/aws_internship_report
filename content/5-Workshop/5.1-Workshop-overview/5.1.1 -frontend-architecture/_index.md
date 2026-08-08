---
title : "Frontend Architecture"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 5.1.1 </b> "
---

### 1. Explanation of the Frontend Directory Structure

#### 1.1. Overall Structure

```
smart-docs-ai/                         # Frontend root directory (Vite + React + TypeScript)
├── .bolt/                              # Configuration for the AI ​​builder tool (Bolt); not part of runtime source code
│   ├── config.json
│   └── prompt
│
├── src/
│   ├── components/                    # Shared UI components for chat functionality
│   │   ├── AuthCorner.tsx             # Header right corner: login/signup buttons or user info + logout
│   │   ├── AuthModal.tsx              # Authentication modal (login / signup / OTP verification)
│   │   ├── ChatPanel.tsx              # Main conversation area: message list + input field
│   │   └── Sidebar.tsx                # Sidebar: session creation, document upload, document list & session history
│   │
│   ├── context/                       # React Context API
│   │   └── AuthContext.tsx            # Login state management (AWS Amplify/Cognito integration)
│   │
│   ├── lib/                           # Shared utilities & configuration
│   │   ├── qa.ts                      # Client-side extractive Q&A module
│   │   └── supabase.ts                # Supabase client initialization + type definitions (Session, DocumentRow, Message)
│   │
│   ├── App.tsx                        # Root component: Routes, AuthProvider, Header/Sidebar/ChatPanel layout
│   ├── index.css                      # Global styles + Tailwind directives
│   └── main.tsx                       # Entry point: AWS Amplify (Cognito) config, React + BrowserRouter mounting
│
├── supabase/
│   └── migrations/
│       └── 20260806054213_create_qa_ap...   # SQL migrations (Supabase tables related to Q&A/answers)
│
├── .env.example                       # Environment variable template (VITE_SUPABASE_URL, VITE_SUPABASE_ANON_KEY...)
├── .gitignore
├── README.md
├── favicon.svg
├── index.html                         # HTML entry point (Vite)
├── package-lock.json
├── package.json
├── tsconfig.app.json
├── tsconfig.json
├── tsconfig.node.json
├── vercel.json                        # Vercel deployment configuration
└── vite.config.ts                     # Vite configuration (path alias @/, build options)
```

---

#### 1.2. Detailed explanation of each directory

##### `src/components/`
UI components directly supporting the RAG chat feature; these are not organized via Feature-Sliced ​​Design but are instead grouped flatly within a single directory:

- **`AuthCorner.tsx`**: Displayed in the top-right corner of the header. If logged in: shows an initial-based avatar, email, and a "Log out" button (`handleLogout` clears `localStorage`/`sessionStorage`, calls `signOut()` from `AuthContext`, and then uses `window.location.replace('/')` to avoid preserving navigation history). If not logged in: shows "Log in" and "Sign up" buttons that open the `AuthModal`.

- **`AuthModal.tsx`**: A shared modal for `signin`, `signup`, and `verify` modes; it calls **AWS Amplify Auth** functions directly (`signIn`, `signUp`, `confirmSignUp`, `getCurrentUser`) without using Redux thunks. Upon successful login, it saves user info to `sessionStorage` (`smartdocs-auth-user`), dispatches a custom event `smartdocs:auth-changed` to synchronize other components, and navigates to `/chat`.

- **`ChatPanel.tsx`**: The main conversation area. Displays the message list (`MessageBubble`, `Avatar`) and processing status (e.g., "Searching documents..."). Includes an `EmptyState` component with content that changes based on whether a session or document exists. The input field supports sending via `Enter` (with `Shift+Enter` for line breaks) and disables input when there is no active session (`hasSession`).

- **`Sidebar.tsx`**: Comprises three main sections:
- "New Session" button to create a session. - Document upload area (images/PDF): Directly calls the API Gateway (`POST /upload`) to retrieve an `uploadUrl` (S3 presigned URL), then performs a `PUT` request to upload the file straight to S3; the newly uploaded document is temporarily pinned to the `recentDocs` state for immediate display before synchronization with the server occurs. 
- Document list includes checkboxes (`selectedDocIds`) to define the scope of documents used as context for Q&A, along with individual document deletion buttons; also includes a session history list with a session deletion menu (featuring a confirmation modal).

##### `src/context/AuthContext.tsx`
Instead of Redux, the project utilizes **React Context combined directly with AWS Amplify Auth**:
- Stores the current user in `sessionStorage` (ensuring independent sessions per tab to avoid multi-tab conflicts).
- Upon mounting, if `sessionStorage` is empty (e.g., opening a new tab or browser instance), it proactively calls `amplifySignOut()` to clear any residual hidden tokens from Amplify's `localStorage`—preventing "silent auto-login" issues.
- Exposes `user`, `loading`, `signIn`, `signUp`, and `signOut` via the `useAuth()` hook.
- Synchronizes state across tabs/components using the custom event `smartdocs:auth-changed` and the standard `storage` event.

##### `src/lib/`
- **`qa.ts`**: A client-side extractive Q&A engine—it performs Vietnamese tokenization (removing punctuation and stopwords), splits documents into sentences, scores each sentence based on keyword overlap with the query (including partial matching for Vietnamese), and returns the 1–2 most relevant segments. This serves as an independent fallback/alternative answering layer, separate from the main RAG pipeline running on AWS (Bedrock + pgvector).
- **`supabase.ts`**: Initializes the Supabase client (using `sessionStorage` for session persistence) and defines shared types: `Session`, `DocumentRow`, and `Message`. Since most actual chat and upload operations call the **AWS API Gateway** directly, Supabase here primarily supports TypeScript typing and serves as a fallback option based on configuration.

##### `src/App.tsx`
The root component of the application:
- Wraps the app in `AuthProvider` and defines routes: `/` (landing), `/chat`, `/chat/:sessionId`, and a wildcard redirect to `/`.
- `AppInner` manages core business state (`sessions`, `messages`, `documents`, `selectedDocIds`) and maintains two-way synchronization with `sessionStorage` to cache data locally per browser tab.
- No dedicated service or Axios layer exists; API calls to the AWS API Gateway (`https://wzie3iseyk.execute-api.ap-southeast-1.amazonaws.com/devv1/...`) are made directly using `fetch` within the component, including an `Authorization` header retrieved from `sessionStorage.getItem("user_token")`.
- Key functions include: `loadSessions`, `loadDocuments`, loading message details based on `activeSessionId`, `handleNewSession`, `handleDeleteSession`, `handleDeleteDoc`, and `handleAsk` (sending the query along with selected `documentIds`).
- Displays `Header` and `LoggedOutHero` when the user is not logged in; displays `AppInner` (Sidebar + ChatPanel) when the user is logged in. ##### `src/main.tsx`
Vite entry point: configures **AWS Amplify** with `userPoolId` and `userPoolClientId` (Cognito, `ap-southeast-1` region), then mounts `<App />` within `<BrowserRouter>` and `<StrictMode>`.

##### `supabase/migrations/`
Contains timestamp-named SQL migrations (`create_qa_ap...`) — defining the Supabase schema for questions/answers, running alongside the main data flow handled by AWS.

##### `.bolt/`
Configuration directory for the AI ​​builder tool (Bolt) used during project initialization/scaffolding — does not affect the application's runtime.

---

### 2. Data Flow

```
User Action (in Sidebar / ChatPanel / AuthModal)
    │
    ▼
Component directly calls fetch()
    │
    ├──► For authentication: AWS Amplify Auth SDK (Cognito)
    │         │
    │         ▼
    │    AuthContext stores user in sessionStorage
    │         │
    │         ▼
    │    Dispatches "smartdocs:auth-changed" event → synchronizes AuthCorner, App
    │
    └──► For chat/document operations: direct fetch
              │
              ▼
         Header "Authorization: <user_token>" (retrieved from sessionStorage)
              │
              ▼
         AWS API Gateway (ap-southeast-1) → Lambda
              │
              ▼
         S3 (upload via presigned URL) / DynamoDB (session, message,
         document metadata) / Bedrock (embedding + response generation) /
         RDS PostgreSQL + pgvector (vector search)
              │
              ▼
         JSON Response
    │
    ▼
setState in App.tsx / Sidebar.tsx / ChatPanel.tsx
    │
    ▼
React re-render (simultaneously caching to sessionStorage
via readLocalState/writeLocalState in App.tsx)
```

> This project calls `fetch` directly within individual components and manages state using `useState`/`useCallback` combined with `sessionStorage` caching. This allows for rapid scaffolding but leads to duplicated API-calling logic (URLs, headers, error handling) across multiple files (e.g., `App.tsx`, `Sidebar.tsx`); you might consider extracting this into a shared `api/` module or `lib/api.ts` file if the project expands.