---
title: "Proposal"
date: 2026-08-08
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# SMART DOCUMENT CHATBOT

## INTELLIGENT DOCUMENT QUESTION-ANSWERING SYSTEM ON AWS

---

## 1. PROJECT OVERVIEW

Smart Document Chatbot allows users to upload documents and ask questions in natural language. The system automatically extracts content, divides text into chunks, creates vector embeddings, searches for relevant passages, and uses an artificial intelligence model to generate an answer.

The project uses Retrieval-Augmented Generation (RAG), combining document retrieval with a large language model. Answers are therefore grounded in the user's documents instead of relying only on the model's existing knowledge.

The system is primarily built with Amazon Cognito, Amazon S3, Amazon Textract, AWS Lambda, Amazon Bedrock, Amazon RDS for PostgreSQL, Amazon DynamoDB, and Amazon API Gateway.

**Project name:** Smart Document Chatbot

**Project type:** Web application for document search and question answering

**Primary architecture:** AWS Serverless combined with RDS PostgreSQL

**Processing model:** Retrieval-Augmented Generation

**Deployment region:** AWS Region `us-east-1`

**Implementation period:** June 22, 2026 – August 15, 2026

---

## 2. PROBLEMS TO SOLVE

### 2.1. Manual document search is time-consuming

Users often need to read an entire PDF or text document to find specific information. With long documents or large collections, this process takes considerable time and can overlook important details.

### 2.2. Keyword search is limited

Traditional search works best when a user's keywords exactly match the document. It may miss relevant information when the question uses different words with the same meaning.

### 2.3. AI models may generate inaccurate information

Language models can produce answers that do not exist in the source document, a behavior known as hallucination. The system must therefore constrain the model to answer from retrieved document content.

### 2.4. Conversation history is difficult to manage

Users need to retain previous questions and answers to continue a conversation or review information. This data must be organized by user and chat session.

### 2.5. User data requires protection

Uploaded documents may contain personal or internal information. The system must authenticate users and prevent access to another user's data.

---

## 3. PROJECT OBJECTIVES

### 3.1. General objective

Build an intelligent document question-answering system on AWS that lets users upload documents, ask questions, and receive answers grounded in those documents.

### 3.2. Specific objectives

* Implement user registration and sign-in with Amazon Cognito.
* Store uploaded documents in Amazon S3.
* Extract document text with Amazon Textract.
* Divide text into smaller chunks for retrieval.
* Generate 1,024-dimensional vector embeddings with Amazon Titan Embeddings V2.
* Store vectors in Amazon RDS for PostgreSQL with the `pgvector` extension.
* Retrieve semantically relevant document chunks.
* Use Amazon Bedrock to generate answers from retrieved content.
* Store conversation history in Amazon DynamoDB.
* Develop Lambda functions to create, read, update, and delete chat history.
* Test vector-search and chat-history query performance.
* Build a scalable system with controlled operating costs.

---

## 4. TARGET USERS

The system is intended for:

* Students searching textbooks and study materials.
* Lecturers searching teaching documents.
* Employees searching internal corporate documents.
* Research teams synthesizing information from multiple documents.
* Individuals who need quick question answering over PDF content.

---

## 5. SOLUTION ARCHITECTURE

### 5.1. Document-processing flow

1. The user registers or signs in through Amazon Cognito.
2. The user uploads a document.
3. The document is stored in Amazon S3.
4. Amazon Textract extracts its text.
5. AWS Lambda divides the content into smaller chunks.
6. Lambda sends each chunk to Amazon Bedrock.
7. `amazon.titan-embed-text-v2:0` creates a 1,024-dimensional embedding.
8. Content and vectors are stored in Amazon RDS for PostgreSQL.
9. Basic document information is stored in the `documents` table.

### 5.2. Question-answering flow

1. The user enters a question in the interface.
2. The question is sent to the backend through an API.
3. Amazon Bedrock converts the question into a vector embedding.
4. AWS Lambda searches PostgreSQL for the nearest vectors.
5. The system retrieves the most relevant document chunks.
6. Retrieved content is sent to a language model on Amazon Bedrock.
7. The model generates an answer grounded in the document context.
8. The question and answer are stored in Amazon DynamoDB.
9. The result is returned to the user interface.

---

## 6. AWS SERVICES USED

| AWS service | Role in the system |
|---|---|
| Amazon Cognito | User registration, sign-in, and authentication |
| Amazon S3 | Storage for uploaded documents |
| Amazon Textract | Text extraction from PDFs and images |
| AWS Lambda | Backend, document, embedding, and chat-history processing |
| Amazon Bedrock | Vector embedding generation and answer generation |
| Amazon RDS for PostgreSQL | Storage for documents, chunks, and vector embeddings |
| pgvector | Vector storage and similarity search in PostgreSQL |
| Amazon DynamoDB | Conversation-history storage |
| Amazon API Gateway | APIs connecting the frontend to backend Lambda functions |
| Amazon CloudWatch | Lambda logs and error monitoring |
| AWS IAM | Access control between AWS services |
| Amazon VPC | Network environment for Lambda and RDS |

---

## 7. DATABASE DESIGN

### 7.1. Amazon RDS for PostgreSQL

RDS PostgreSQL stores document information and vector embeddings.

#### `documents` table

| Attribute | Description |
|---|---|
| `document_id` | Document identifier |
| `user_id` | Identifier of the document owner |
| `file_name` | Document name |
| `file_type` | Document format |
| `status` | Processing status |
| `created_at` | Creation time |

#### `document_chunks` table

| Attribute | Description |
|---|---|
| `id` | Chunk identifier |
| `document_id` | Document identifier |
| `user_id` | User identifier |
| `file_name` | Document name |
| `page_number` | Page number |
| `chunk_index` | Chunk position |
| `content` | Text content |
| `embedding` | 1,024-dimensional embedding vector |
| `embedding_model` | Embedding model name |
| `metadata` | Additional data |

Semantic search uses `pgvector` distance operators.

### 7.2. Amazon DynamoDB

The `ChatHistory-dev` table stores conversation history.

| Attribute | Description |
|---|---|
| `userId` | User identifier |
| `sessionId` | Chat-session identifier |
| `messageId` | Message identifier |
| `messageKey` | Sort key in `createdAt#messageId` format |
| `role` | Message sender: user or assistant |
| `content` | Message content |
| `references` | Document references |
| `createdAt` | Creation time |
| `updatedAt` | Last update time |

This key design supports efficient retrieval by user, chat session, and time.

---

## 8. MAIN FEATURES

### 8.1. User authentication

* Register an account.
* Confirm an account through email.
* Sign in and sign out.
* Receive an authentication token from Amazon Cognito.
* Restrict access for unauthenticated users.

### 8.2. Document management

* Upload documents to Amazon S3.
* Extract text with Amazon Textract.
* Divide content into chunks.
* Generate and store vector embeddings.
* Display each user's documents.
* Delete documents that are no longer required.

### 8.3. Document question answering

* Enter questions in natural language.
* Find semantically similar document chunks.
* Generate answers from retrieved content.
* Return the source document, page, and reference passage.
* Limit unsupported answers that are not grounded in documents.

### 8.4. Conversation-history management

* Create new messages.
* Review conversation history.
* Update message content.
* Delete messages.
* Organize history by user and chat session.

---

## 9. ASSIGNED WORK SCOPE

The assigned work in the group project focused on databases, Lambda functions, and performance testing:

* Initialize Amazon RDS for PostgreSQL.
* Configure the VPC and Security Group for RDS.
* Enable the `pgvector` extension.
* Create the `documents` and `document_chunks` tables.
* Configure the embedding field for 1,024 dimensions.
* Create the `ChatHistory-dev` DynamoDB table.
* Design keys for conversation-history queries.
* Develop a Lambda function to create messages.
* Develop a Lambda function to retrieve history.
* Develop a Lambda function to update messages.
* Develop a Lambda function to delete messages.
* Develop a Lambda function that creates test vector data.
* Develop a Lambda function for vector search.
* Integrate Amazon Titan Embeddings V2.
* Generate test data for RDS and DynamoDB.
* Measure and analyze query performance.
* Configure an Amazon Cognito User Pool and test sign-in.
* Summarize results and prepare the report.

Other team members were responsible for the frontend, S3 storage, document extraction with Textract, and the remaining integration components.

---

## 10. IMPLEMENTATION PLAN

| Phase | Period | Main activities |
|---|---|---|
| Phase 1 | 22/06–28/06/2026 | Study AWS, IAM, Global Infrastructure, and cost management |
| Phase 2 | 29/06–05/07/2026 | Research core AWS services |
| Phase 3 | 06/07–12/07/2026 | Analyze requirements and design the architecture |
| Phase 4 | 13/07–19/07/2026 | Initialize RDS, pgvector, and DynamoDB |
| Phase 5 | 20/07–26/07/2026 | Develop CRUD Lambda functions |
| Phase 6 | 27/07–02/08/2026 | Integrate Bedrock and create embeddings and test data |
| Phase 7 | 03/08–09/08/2026 | Measure performance and configure Cognito |
| Completion | 10/08–15/08/2026 | Integrate components, summarize results, and write the report |

---

## 11. TEST RESULTS

### 11.1. Vector search on RDS

* Total test vectors: **2,000**
* Warm-up runs: **5**
* Test runs: **50**
* Results per query: **5**
* Minimum latency: **49.561 ms**
* Maximum latency: **60.079 ms**
* Average latency: **50.398 ms**
* Median: **49.998 ms**
* P95: **50.519 ms**
* Expected result found: **Yes**

### 11.2. Chat-history queries on DynamoDB

* Chat sessions: **100**
* Messages per session: **20**
* Query runs: **100**
* Successful runs: **100**
* Errors: **0**
* Minimum latency: **16.797 ms**
* Maximum latency: **238.972 ms**
* Average latency: **35.172 ms**
* Median: **38.423 ms**
* P95: **58.278 ms**
* Error rate: **0%**

The results show that the system can accurately retrieve vectors and reliably query conversation history within the test-data scope.

---

## 12. COSTS AND CONTROL MEASURES

The system combines Serverless services with Amazon RDS for PostgreSQL. Lambda, DynamoDB, and S3 are charged by usage. Amazon Bedrock is charged by input and output tokens, while Amazon Textract is charged by processed page.

Amazon RDS incurs costs while the database is running, even without queries. The project therefore applies these controls:

* Use AWS Credits and Free Tier where appropriate.
* Stop RDS when performance tests are not running.
* Delete unnecessary snapshots and resources.
* Monitor costs with AWS Cost Explorer.
* Configure alerts with AWS Budgets.
* Limit documents and Bedrock calls in the demonstration environment.
* Review cost by service regularly.

---

## 13. RISKS AND MITIGATIONS

| Risk | Severity | Mitigation |
|---|---|---|
| Lambda cannot connect to RDS | High | Check VPC, subnet, Security Group, and IAM permissions |
| Missing PostgreSQL client library | Medium | Package the dependency or provide it through a Lambda Layer |
| Incorrect AWS Region | Medium | Use `us-east-1` consistently for related resources |
| Vector does not have 1,024 dimensions | High | Validate embedding dimensions before storage |
| Answer is not grounded in documents | High | Supply only retrieved context and require source references |
| Data leaks between users | High | Authenticate with Cognito and filter by `user_id` |
| Unexpected RDS costs | Medium | Stop the database when unused and configure AWS Budgets |
| Lambda timeout | Medium | Optimize queries, limit data, and configure an appropriate timeout |
| Loss of chat history | Medium | Design DynamoDB keys clearly and test CRUD operations |
| Missing IAM permissions | Medium | Apply least privilege and inspect CloudWatch Logs |

---

## 14. EXPECTED RESULTS

After completion, the system should meet these requirements:

* Users can register and sign in.
* Users can upload documents.
* The system can extract and process document content.
* Content is converted to 1,024-dimensional vector embeddings.
* Vectors are stored in PostgreSQL with `pgvector`.
* Users can ask questions about their documents.
* Answers are generated from retrieved document content.
* Answers include source references.
* Conversation history is stored and queried by user.
* CRUD functions operate correctly.
* Performance and accuracy test results are available.
* Resource costs are monitored and controlled.

---

## 15. DELIVERABLES

* Overall AWS system architecture.
* Amazon RDS for PostgreSQL with the `pgvector` extension.
* `documents` and `document_chunks` tables.
* `ChatHistory-dev` DynamoDB table.
* Lambda functions for chat-history CRUD operations.
* Lambda function for generating test vector data.
* Lambda function for vector search.
* Amazon Cognito User Pool configuration.
* RDS and DynamoDB test results.
* Project source code.
* Deployment instructions.
* Bilingual Vietnamese-English report website.

---

## 16. CONCLUSION

Smart Document Chatbot addresses the need to search and ask questions over document content by combining AWS services with a RAG architecture.

The project reduces manual search time, improves semantic information retrieval, and manages conversation history effectively.

Through the project, the team applies knowledge of Cloud Computing, Serverless, Security, Databases, and Generative AI to a practical problem. The system also provides a foundation for supporting additional document formats, managing documents by user, improving source citations, and optimizing performance in the future.
