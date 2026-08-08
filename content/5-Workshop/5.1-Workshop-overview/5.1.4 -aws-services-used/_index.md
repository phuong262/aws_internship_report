---
title : "AWS Services Used"
date : 2024-01-01
weight : 4
chapter : false
pre : " <b> 5.1.4 </b> "
---

This section details each AWS service utilized by SmartDocAI, including specific configurations and the rationale behind their selection, helping readers understand the role of each component within the overall architecture presented earlier.

### 1. Compute & API

| Service | Configuration | Role |
|---|---|---|
| **AWS Lambda** | Independent functions, Zip package type, Python 3.12 runtime, ap-southeast-1 region | Handles all business logic (authentication, uploads, RAG, vector operations) following a microservices model. Functions run independently, scale automatically, and optimize costs. |
| **Amazon API Gateway** | REST API, CORS integration, Cognito Authorizer | Secure communication gateway for the frontend; routes requests (GET, POST, DELETE) to the corresponding Lambda functions. |

### 2. Storage

| Service | Configuration | Role |
|---|---|---|
| **Amazon S3 (Application Storage)** | Separate buckets for Documents and Frontend | Securely stores physical files (PDFs, images) uploaded by users via pre-signed URLs. Also hosts static files for the React SPA application. |
| **Amazon S3 (Frontend Hosting)** | pgvector extension integration | Serves as the core vector database for the RAG system. Stores text chunks and 1024-dimensional vector embeddings; executes Cosine Similarity queries. |
| **Amazon DynamoDB** | Users-dev, ChatHistory-dev, and Documents-dev tables | High-speed NoSQL database used to store user profiles, document metadata, and complete chat session histories. |

### 3. Identity & Security

| | Service | Configuration | Role |
|---|---|---|
| **Amazon Cognito** | User Pool, JWT Token issuance | Manages identity; handles registration, login, and secure user authentication flows. Automatically triggers Lambda functions upon user account confirmation. |
| **AWS IAM** | Custom Roles & Policies for each Lambda function | Ensures the Principle of Least Privilege. Grants Lambda secure access to S3, RDS, and DynamoDB, and allows calls to Bedrock/Textract. |

### 4. AI & Machine Learning

| Service | Model / Function | Role |
|---|---|---|
| **Amazon Textract** | Text Extraction (OCR) | Automatically analyzes and extracts text from complex documents (scanned PDFs, images) to prepare for the chunking process. |
| **Amazon Bedrock (LLM)** | `amazon.nova-lite-v1:0` | Acts as the information synthesis engine, generating natural language responses based on context retrieved from RDS. |
| **Amazon Bedrock (Embeddings)** | `amazon.titan-embed-text-v2:0` (1024 dimensions) | Converts natural language from documents and questions into numerical vectors for indexing and semantic search. |

### 5. Event Orchestration & Delivery

| Service | Configuration | Role |
|---|---|---|
| **Amazon SNS** | Notification Topic linked to Textract | Acts as the "messenger" within the event-driven architecture. Receive a signal when Textract finishes processing the file and triggers the `textract-result-dev` Lambda function. |
| **AWS Amplify** | Web Hosting & CI/CD | Hosts and automatically deploys React frontend applications directly from source code. Features built-in global CDN integration and automatic HTTPS security certificate management. |

### 6. Automation & Monitoring

| Service | Configuration | Role |
|---|---|---|
| **Amazon CloudWatch** | CloudWatch Logs | Monitors and records execution logs for all 15 Lambda functions, enabling engineers to track data flow and troubleshoot issues quickly. |

### 7. Architectural Layer Summary

| Layer | AWS Services Used |
|---|---|
| **Presentation** | AWS Amplify |
| **Application & Orchestration** | API Gateway, AWS Lambda, SNS |
| **Data** | S3 (Storage), RDS PostgreSQL (pgvector), DynamoDB |
| **AI & NLP** | Amazon Bedrock, Amazon Textract |
| **Security & Identity** | Amazon Cognito, AWS IAM |
| **Monitoring** | CloudWatch, SNS |