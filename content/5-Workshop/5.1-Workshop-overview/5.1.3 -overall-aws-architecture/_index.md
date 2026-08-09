---
title : "Overall Architecture on AWS"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 5.1.3 </b> "
---

Having explored the Frontend and Backend architectures separately in the previous two sections, this section consolidates them into a **comprehensive overview** of how AWS components work together to form the complete SmartDocAI system, alongside the per-user data storage structure.

#### Overall Architecture on AWS

The entire system is deployed using a **serverless** model, organized into four functional layers (blocks) as shown in the architecture diagram:

![](/images/5-Workshop/5.1-Workshop-overview/Structure.jpeg)

##### a. Frontend & API Layer

- **User**: End-users accessing the application via a web browser.
- **AWS Amplify**: Hosts and automatically deploys the React interface; integrates CDN and HTTPS certificates.
- **Amazon Cognito**: Manages user registration, login, and authentication; issues JWTs (idToken/accessToken).
- **API Gateway**: The single entry point (REST API `/api`); authenticates requests using a Cognito Authorizer before forwarding them to the corresponding Lambda functions in the next layer.

##### b. Communication & Orchestration Layer (Lambdas)

This is the business logic layer, consisting of Lambda functions **located outside the VPC** (except for those interacting directly with RDS in a dedicated VPC layer) to avoid NAT Gateway costs. Each Lambda handles a specific business task (adhering to the single responsibility principle):

| Lambda | Trigger | Role |
|---|---|---|
| `user-post-confirm` | Cognito Post Confirmation trigger | Runs automatically after successful user registration (e.g., initializing the user profile in DynamoDB). |
| `UploadFiles` | API Gateway (`POST /upload`) | Generates a presigned URL allowing the client to `PUT` files directly to **Amazon S3**. |
| `textract-start` | API Gateway or S3 Event | Launch the `StartDocumentTextDetection` job (Textract) in asynchronous mode. |
| `textract-result` | **SNS Notification** (Textract signals completion) | Retrieve the full OCR'd text, split it into chunks, and call `create-vector`. |
| `create-vector` | Invoked by `textract-result` | Send each chunk to **Bedrock (embedding)**, then invoke `vector-insert`. |
| `ChatbotRAG` | API Gateway (`POST /chat`) | Orchestrate the Q&A flow: embed the question → call `vector-search` → call **Bedrock (LLM)** to generate the answer → write history to **DynamoDB**. |
| `chat-get-history` | API Gateway (`GET /chat-history`) | Query conversation history from DynamoDB. |
| `delete-session` | API Gateway (`DELETE /session`) | Delete the chat session (and related data). |
| `delete-document` | API Gateway (`DELETE /document`) | Delete the document (S3 object, metadata, and associated vectors). |
| `api-get-handle` | API Gateway (`GET ...`) | Shared Lambda function handling other read (GET) operations, involving DynamoDB read/write. |

##### c. Storage & AI Layer (AWS Services)

- **Amazon S3**: Stores original files uploaded by users (images/PDFs).
- **Amazon Textract**: Extracts text from documents (OCR) and notifies via SNS upon completion.
- **Amazon Bedrock (Embedding — Titan Embed)**: Converts text (chunks or questions) into vectors.
- **Amazon Bedrock (LLM)**: Generates the final answer based on the question and retrieved context.
- **Amazon DynamoDB**: Stores conversation history and related metadata.

##### d. AWS VPC Layer – Private Subnet

Lambda functions requiring direct access to **Amazon RDS PostgreSQL (pgvector)**—which resides in a private subnet without a public address—must run within the VPC:

- **`vector-insert`**: Receives vector embeddings from `create-vector` and inserts them into RDS.
- **`vector-search`**: Receives the query vector from `ChatbotRAG`, performs a cosine similarity search on RDS, and returns the most relevant chunks.
- **`rds-int`**: Handles RDS schema initialization and maintenance (e.g., creating tables, enabling the `pgvector` extension).

Isolating these specific Lambda functions within the VPC—rather than placing the entire system inside it—offers the following benefits:
1. Only the components that genuinely need RDS access incur the overhead of ENI initialization and VPC entry/exit.
2. The remaining Lambda functions (handling orchestration, Bedrock calls, DynamoDB operations, and S3/Textract interactions) do not require a NAT Gateway, as these services utilize public endpoints or dedicated APIs, resulting in **significant NAT Gateway cost savings**.

##### e. Summary of Main Data Flows

- **Authentication Flow**: `User → Cognito → API Gateway` (JWT attached to all subsequent requests).
- **Document Ingestion Flow**: `UploadFiles → S3 → textract-start → Textract → (SNS) → textract-result → create-vector → Bedrock (embedding) → vector-insert → RDS (pgvector)`.
- **Q&A Flow (RAG)**: `ChatbotRAG → Bedrock (embed query) → vector-search → RDS (cosine search) → ChatbotRAG → Bedrock (LLM generates answer) → DynamoDB (save history) → return result to client`. - **Management flow**: `delete-session`, `delete-document`, `chat-get-history`, and `api-get-handle` interact directly with DynamoDB (while indirectly cleaning up associated data in S3/RDS when a document is deleted).

> **Design note**: The architecture adheres to the single-purpose function principle, facilitating independent scaling, simplifying function-level debugging via CloudWatch, and minimizing the blast radius in the event of a component failure.