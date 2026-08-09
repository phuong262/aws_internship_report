---
title : "Integrating API Gateway and Frontend"
weight : 6
chapter : false
pre : " <b> 5.4.6 </b> "
---

### 1. Building the Frontend Interface (React App)
The SmartDocAI application interface is built on the **React** platform, providing core features for users:
*   **Authentication Page:** Integrates AWS Amplify to connect directly with Amazon Cognito, supporting user login, registration, and account verification via email.
*   **Document Upload Page:** Allows users to select PDF or text documents; the system then uploads them to Amazon S3, processes the content, and stores data in RDS and DynamoDB via file-processing Lambda functions.
*   **Chat Interface:** An online chat window where users can submit questions and receive answers based on the content of documents processed using RAG.

### 2. Integrating Amazon API Gateway with the Frontend
Amazon API Gateway serves as the **Single Entry Point** for all client-side requests directed at backend services (Lambda functions).

*   **Step 1 - Configuring the Base URL:**
In the frontend source code, the team configures an environment variable pointing to the API Gateway endpoint URL deployed on AWS `(https://wzie3iseyk.execute-api.ap-southeast-1.amazonaws.com/devv1)`.
*   **Step 2 - Attaching the Authentication Token:**
The React application automatically retrieves the **JWT (JSON Web Token)** from storage and attaches it to the **Authorization header** of every API request (e.g., sending messages, uploading files). This token acts as a "key" allowing the AWS backend system to authenticate the user and grant access to data. *   **Step 3 - Routing via API Gateway:**
API Gateway receives the request, validates the token, and routes the request directly to the appropriate Lambda function (such as `UploadFiles` or `ChatbotRAG`).

### 3. Outcomes
The successful integration of API Gateway enables the frontend application to communicate in a synchronized and secure manner, while remaining completely decoupled from the serverless system's internal processing architecture.