---
title: "Proposal"
weight: 2
chapter: false
pre: "<b>2. </b>"
---

# SMART DOCUMENT CHATBOT

## AN INTELLIGENT DOCUMENT Q&A SYSTEM ON AWS

---

## 1. PROJECT OVERVIEW

Smart Document Chatbot is a system that allows users to upload documents and ask questions in natural language. The system automatically extracts content, splits text into chunks, generates vector embeddings, retrieves relevant information, and utilizes artificial intelligence models to generate responses.

The project implements a Retrieval-Augmented Generation (RAG) architecture, combining document retrieval capabilities with large language models. As a result, answers are generated strictly based on the user's uploaded document content rather than relying solely on the AI model's pre-trained knowledge.

The system is primarily built using AWS services, including Amazon Cognito, Amazon S3, Amazon Textract, AWS Lambda, Amazon Bedrock, Amazon RDS PostgreSQL, Amazon DynamoDB, and Amazon API Gateway.

**Project Name:** Smart Document Chatbot

**Project Type:** Document Q&A and Content Search Web Application

**Core Architecture:** AWS Serverless combined with Amazon RDS PostgreSQL

**Processing Model:** Retrieval-Augmented Generation (RAG)

**Deployment Region:** AWS Region `us-east-1`

**Timeline:** June 22, 2026 – August 15, 2026

---

## 2. PROBLEM STATEMENT

### 2.1. Time-Consuming Manual Document Search

Users often have to read through entire PDF or text documents to find specific information. For long documents or large volumes of files, this manual process is time-consuming and prone to missing critical details.

### 2.2. Limitations of Keyword-Based Search

Traditional search engines work well only when the user's search query exactly matches the keywords in the document. The system struggles to find relevant results when users phrase questions using different terminology with similar meanings.

### 2.3. AI Hallucination Risks

Large language models can generate responses containing information not present in the reference documents, a phenomenon known as hallucination. Therefore, the system must constrain the AI model to answer strictly based on retrieved document contexts.

### 2.4. Difficulty in Managing Chat History

Users need to retain previous questions and answers to maintain context across continuous multi-turn conversations or review past interactions. This data must be structured systematically per user and session.

### 2.5. User Data Security Requirements

Uploaded documents may contain sensitive personal information or internal proprietary data. Consequently, the system requires robust user authentication and strict access controls to prevent unauthorized access to other users' data.

---

## 3. PROJECT OBJECTIVES

### 3.1. General Objective

Build an intelligent document Q&A system on AWS that enables users to upload documents, ask natural language questions, and receive accurate answers derived directly from the document content.

### 3.2. Specific Objectives

* Implement user registration and authentication workflows using Amazon Cognito.
* Store uploaded documents securely in Amazon S3.
* Utilize Amazon Textract for automated text extraction from documents.
* Perform text chunking to optimize retrieval performance.
* Generate 1,024-dimensional vector embeddings using Amazon Titan Embeddings V2.
* Store vector embeddings in Amazon RDS PostgreSQL using the `pgvector` extension.
* Execute semantic similarity searches on document chunks.
* Generate grounded responses using Amazon Bedrock based on retrieved contexts.
* Persist conversation history in Amazon DynamoDB.
* Develop AWS Lambda functions for Create, Read, Update, and Delete (CRUD) operations on chat history.
* Conduct performance benchmarks for vector search and chat history queries.
* Design a scalable and cost-controlled architecture.

---

## 4. TARGET AUDIENCE

The system caters to the following user groups:

* **Students:** Accessing and querying textbooks, research papers, and study materials.
* **Instructors/Educators:** Extracting specific information from teaching materials and reference books.
* **Enterprise Employees:** Querying internal documentation, policies, and operational manuals.
* **Researchers:** Synthesizing information across multiple reference documents.
* **Individual Users:** Quick Q&A and information retrieval from PDF files.

---

## 5. SOLUTION ARCHITECTURE

### 5.1. Document Processing Pipeline

1. The user registers or logs in via Amazon Cognito.
2. The user uploads a document through the web interface.
3. The document is stored securely in Amazon S3.
4. Amazon Textract is triggered to extract text from the document (PDF/Image).
5. Upon completion, Textract sends a notification via Amazon SNS to automatically trigger an AWS Lambda function.
6. AWS Lambda receives the event, retrieves the extracted text from Textract, and splits it into smaller chunks.
7. Lambda sends each text chunk to Amazon Bedrock (`amazon.titan-embed-text-v2:0`) to generate 1,024-dimensional vector embeddings.
8. Each text chunk and its corresponding vector embedding are saved into Amazon RDS PostgreSQL (via the `pgvector` extension).
9. Document metadata is recorded in Amazon DynamoDB for management.

### 5.2. Q&A and Retrieval Pipeline

1. The user inputs a question on the frontend interface.
2. The question is routed to the backend via Amazon API Gateway to invoke an AWS Lambda function.
3. The Lambda function passes the query to Amazon Bedrock to generate a vector embedding.
4. The Lambda function uses the query vector to execute a similarity search in Amazon RDS PostgreSQL.
5. The system retrieves the top most relevant document chunks based on semantic similarity.
6. The user question and the retrieved contextual chunks are packaged into a prompt and sent to the Large Language Model (LLM) on Amazon Bedrock (`amazon.nova-lite-v1:0`).
7. The AI model synthesizes the information and generates an accurate answer grounded in the provided context.
8. The question and answer pair are stored in Amazon DynamoDB.
9. The final response along with source references is returned to the user interface.

---

## 6. AWS SERVICES UTILIZED

| AWS Service          | Role in the System                                                    |
| -------------------- | --------------------------------------------------------------------- |
| AWS Amplify | Hosting and automated deployment for frontend applications (React). Includes built-in global CDN integration and automated HTTPS security certificate management. |
| Amazon Cognito       | User authentication, authorization, and user pool management          |
| Amazon S3            | Object storage for user-uploaded documents                            |
| Amazon Textract      | Text extraction from PDF documents and images                         |
| Amazon SNS           | Notifications for Textract job completion to trigger Lambda functions |
| AWS Lambda           | Serverless backend execution for document processing, embeddings, and chat history |
| Amazon Bedrock       | Generating vector embeddings and LLM responses                        |
| Amazon RDS PostgreSQL| Relational storage for metadata and vector embeddings                 |
| pgvector             | PostgreSQL extension for vector storage and similarity searches       |
| Amazon DynamoDB      | NoSQL database for fast, key-value chat history storage              |
| Amazon API Gateway   | RESTful API management exposing backend Lambda endpoints to frontend |
| Amazon CloudWatch    | Logging, monitoring, and error tracking for Lambda functions          |
| AWS IAM              | Identity and Access Management securing inter-service communications   |
| Amazon VPC           | Isolated network environment for AWS Lambda and Amazon RDS            |

---

## 7. DATABASE DESIGN

### 7.1. Amazon RDS PostgreSQL

Amazon RDS PostgreSQL is used to store document details and vector embeddings.

#### Table `documents`

Stores high-level metadata for uploaded files:

| Attribute     | Description                             |
| ------------- | --------------------------------------- |
| `document_id` | Unique identifier for the document      |
| `user_id`     | Identifier of the owning user           |
| `file_name`   | Original name of the uploaded file      |
| `file_type`   | File format / extension                 |
| `status`      | Current processing status               |
| `created_at`  | Timestamp of creation                   |

#### Table `document_chunks`

Stores text chunks and their corresponding vector embeddings:

| Attribute         | Description                             |
| ----------------- | --------------------------------------- |
| `id`              | Unique identifier for the text chunk    |
| `document_id`     | Reference ID to `documents` table       |
| `user_id`         | User identifier                         |
| `file_name`       | Document file name                      |
| `page_number`     | Page number where chunk resides         |
| `chunk_index`     | Sequential position index of the chunk  |
| `content`         | Extracted raw text content              |
| `embedding`       | 1,024-dimensional vector embedding      |
| `embedding_model` | Model used for embedding generation     |
| `metadata`        | Additional metadata                     |

Semantic search is performed using `pgvector` distance operators.

### 7.2. Amazon DynamoDB

The `ChatHistory-dev` table persists chat logs and conversation threads.

Key attributes include:

| Attribute    | Description                                            |
| ------------ | ------------------------------------------------------ |
| `userId`     | Partition key - User identifier                        |
| `sessionId`  | Session identifier                                     |
| `messageId`  | Unique message identifier                              |
| `messageKey` | Sort key formatted as `createdAt#messageId`           |
| `role`       | Message sender (`user` or `assistant`)                 |
| `content`    | Message payload text                                   |
| `references` | Array of source document citations                     |
| `createdAt`  | Timestamp of creation                                  |
| `updatedAt`  | Timestamp of last update                               |

The primary key design enables efficient querying of chat messages filtered by user, session, and chronological order.

---

## 8. CORE FEATURES

### 8.1. User Authentication

* User registration and signup.
* Email verification workflows.
* Login and logout functionality.
* JWT token management via Amazon Cognito.
* Route protection for unauthorized guests.

### 8.2. Document Management

* File uploads to Amazon S3.
* Automated text extraction using Amazon Textract.
* Intelligent text chunking.
* Vector embedding generation and indexing.
* Document list display per user.
* Document deletion and cleanup.

### 8.3. Intelligent Document Q&A

* Natural language question processing.
* Vector similarity retrieval against document chunks.
* Context-aware response generation.
* Citation referencing (document name, page number, chunk text).
* Strict prompt constraints to mitigate AI hallucinations.

### 8.4. Chat History Management

* Saving incoming and outgoing messages.
* Fetching session chat history.
* Updating message contents.
* Deleting specific messages or chat sessions.
* Data partitioning by `userId` and `sessionId`.

---

## 9. RESPONSIBILITIES AND SCOPE OF WORK

In this team project, the assigned scope focuses on backend database engineering, serverless Lambda functions, and performance benchmarking:

* Provisioning and configuring Amazon RDS PostgreSQL.
* Setting up VPC, private subnets, and Security Groups for RDS.
* Enabling and configuring the `pgvector` extension.
* Designing and creating `documents` and `document_chunks` schemas.
* Configuring 1,024-dimension vector columns.
* Creating and tuning the `ChatHistory-dev` DynamoDB table.
* Designing composite key structures for optimal query patterns.
* Developing Lambda function for Creating chat messages.
* Developing Lambda function for Fetching chat history.
* Developing Lambda function for Updating chat messages.
* Developing Lambda function for Deleting chat messages.
* Developing Lambda for generating benchmark synthetic vector data.
* Developing Lambda for vector similarity search.
* Integrating Amazon Titan Embeddings V2 APIs.
* Generating test data for RDS PostgreSQL and DynamoDB.
* Conducting query latency benchmarking and analysis.
* Configuring Amazon Cognito User Pool and testing login workflows.
* Aggregating benchmark results and contributing to technical documentation.

Other team members were responsible for frontend development, S3 storage integration, Textract pipeline configuration, and overall system integration.

---

## 10. PROJECT SCHEDULE

| Phase       | Timeline           | Core Deliverables & Activities                               |
| ----------- | ------------------ | ------------------------------------------------------------ |
| Phase 1     | Jun 22 - Jun 28, 2026 | AWS fundamentals, IAM policies, global infrastructure & cost management |
| Phase 2     | Jun 29 - Jul 05, 2026 | Researching core AWS serverless and database services        |
| Phase 3     | Jul 06 - Jul 12, 2026 | Requirements analysis and system architecture design          |
| Phase 4     | Jul 13 - Jul 19, 2026 | Provisioning RDS, pgvector setup, and DynamoDB schema creation|
| Phase 5     | Jul 20 - Jul 26, 2026 | Developing CRUD AWS Lambda functions                         |
| Phase 6     | Jul 27 - Aug 02, 2026 | Bedrock integration, vector generation, and test data seed   |
| Phase 7     | Aug 03 - Aug 09, 2026 | Performance testing, latency analysis, and Cognito config    |
| Final Phase | Aug 10 - Aug 15, 2026 | End-to-end integration, result synthesis, and report writing |

---

## 11. BENCHMARK AND TEST RESULTS

### 11.1. Vector Search Performance (Amazon RDS PostgreSQL)

* **Total Test Vectors:** 2,000
* **Warm-up Requests:** 5
* **Test Iterations:** 50
* **Top-K Results:** 5
* **Min Latency:** 49.561 ms
* **Max Latency:** 60.079 ms
* **Average Latency:** 50.398 ms
* **Median (P50):** 49.998 ms
* **95th Percentile (P95):** 50.519 ms
* **Expected Ground Truth Match:** Passed (100% accuracy)

### 11.2. Chat History Query Performance (Amazon DynamoDB)

* **Total Sessions:** 100
* **Messages per Session:** 20
* **Query Requests:** 100
* **Successful Requests:** 100
* **Failed Requests:** 0
* **Min Latency:** 16.797 ms
* **Max Latency:** 238.972 ms
* **Average Latency:** 35.172 ms
* **Median (P50):** 38.423 ms
* **95th Percentile (P95):** 58.278 ms
* **Error Rate:** 0.00%

The test results demonstrate that the system achieves high precision vector retrieval and maintains low-latency, stable performance for chat history queries within the tested dataset scale.

---

## 12. COST ESTIMATION AND OPTIMIZATION PLAN

The system employs a hybrid Serverless and Managed Database architecture.

Serverless components including AWS Lambda, DynamoDB, and Amazon S3 follow pay-as-you-go pricing models. Amazon Bedrock is billed based on processed input and output token counts, while Amazon Textract is charged per processed document page.

Amazon RDS incurs continuous charges while the database instance is running regardless of traffic. To optimize costs during development, the following measures were enforced:

* Utilizing AWS Free Tier and AWS Educational Credits where applicable.
* Stopping Amazon RDS instances during non-testing hours.
* Cleaning up unnecessary DB snapshots and orphaned S3 objects.
* Monitoring real-time expenditures via AWS Cost Explorer.
* Setting up automated budget alerts via AWS Budgets.
* Capping document upload sizes and Bedrock invocation limits during testing.
* Performing weekly service-by-service cost reviews.

---

## 13. RISK MANAGEMENT AND MITIGATION

| Risk Factor                       | Severity | Mitigation Strategy                                             |
| --------------------------------- | -------- | --------------------------------------------------------------- |
| Lambda fails to connect to RDS    | High     | Verify VPC configuration, Subnets, Security Groups, and IAM roles|
| Missing PostgreSQL client modules | Medium   | Package dependencies using Lambda Layers or deployment packages |
| AWS Region mismatch               | Medium   | Enforce unified deployment in the `us-east-1` region            |
| Vector dimension mismatch         | High     | Validate 1,024-dimension output prior to DB insertion          |
| Un-grounded AI responses          | High     | Inject strict prompt instructions requiring source citations    |
| Cross-user data leakage           | High     | Authenticate via Cognito and enforce strict `user_id` filtering |
| Unexpected RDS cost overruns      | Medium   | Stop DB instances when idle; set up AWS Budgets alerts          |
| Lambda execution timeout          | Medium   | Optimize SQL queries, reduce chunk payload, adjust timeouts     |
| Chat history data loss            | Medium   | Design durable key structures and conduct thorough CRUD testing |
| Insufficient IAM permissions      | Medium   | Follow least-privilege principles and audit CloudWatch Logs     |

---

## 14. EXPECTED OUTCOMES

Upon completion, the system satisfies all key deliverables:

* Functional user registration and authentication flow.
* Secure document upload capabilities.
* Automated text extraction and document preprocessing.
* Accurate generation of 1,024-dimension vector embeddings.
* Vector storage and indexing in PostgreSQL using `pgvector`.
* Intelligent natural language Q&A interface over uploaded files.
* Grounded AI responses complete with page and document citations.
* Session-based chat history persistence filtered by user.
* Verified, error-free CRUD operations on backend services.
* Empirical performance benchmarks for system latency.
* Effective cost control and resource management.

---

## 15. DELIVERABLES

* Overall System Architecture Diagram on AWS.
* Configured Amazon RDS PostgreSQL instance with `pgvector`.
* SQL Schema definitions for `documents` and `document_chunks`.
* Amazon DynamoDB `ChatHistory-dev` table configuration.
* Suite of AWS Lambda functions for Chat History CRUD operations.
* Synthetic vector data generation Lambda function.
* Vector similarity search Lambda function.
* Amazon Cognito User Pool configuration settings.
* Comprehensive performance test reports (RDS & DynamoDB).
* Source code repository.
* System deployment and setup guide.
* Bilingual (Vietnamese - English) project website documentation.

---

## 16. CONCLUSION

Smart Document Chatbot successfully addresses the challenges of searching and retrieving information from complex documents by combining AWS Cloud services with a RAG architecture.

The project significantly reduces manual search time, improves semantic information retrieval accuracy, and provides reliable chat history management.

Through this project, the team effectively applied practical knowledge of Cloud Computing, Serverless, Security, Database Systems, and Generative AI to a real-world software engineering problem. The resulting architecture serves as a solid foundation for future enhancements, such as multi-format file support, multi-tenant workspace management, advanced citation rendering, and query optimization.