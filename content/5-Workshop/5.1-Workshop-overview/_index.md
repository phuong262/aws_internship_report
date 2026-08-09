---
title : "Overview"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

## Problem Statement

The Document Chatbot system allows users to ask questions about document content. To perform semantic search, the system needs to store document content as small chunks and vector embeddings. It also needs to store Q&A history so users can review their conversations.

## Scope of Implementation

The workshop's practical exercises include:

- Provisioning RDS PostgreSQL and configuring networking.
- Creating a VPC, Subnets, and Security Groups to control connectivity between Lambda and RDS.
- Storing database credentials in AWS Secrets Manager.
- Enabling `pgvector`.
- Creating tables to store documents and vectors.
- Creating a DynamoDB table to store chat history.
- Building Lambda functions for Create, Get, Update, and Delete operations.
- Building Lambda functions for adding and searching vectors.
- Invoking Titan Embeddings V2 to generate 1,024-dimensional vectors.
- Ingesting test data and measuring performance.

## Data Layer Architecture

```text
AWS Lambda (attached to VPC)
├── Security Group ── port 5432 ──> Amazon RDS PostgreSQL
│                                  ├── documents
│                                  └── document_chunks (vector(1024))
├── AWS Secrets Manager ──> RDS connection details
├── Amazon DynamoDB ──────> ChatHistory-dev
└── Amazon Bedrock ───────> Titan Embeddings V2
```

The VPC and Security Group establish a network boundary for the database component. Lambda does not hardcode passwords in the source code; instead, it retrieves connection details from Secrets Manager at runtime. It then connects to RDS for vector processing, DynamoDB for chat history management, and Bedrock for embedding generation. ## AWS Services Used and Rationale

### Amazon VPC and Security Groups

**Project Role:** The VPC establishes a private network environment for RDS and Lambda. Security Groups act as firewalls, permitting only the Lambda function's Security Group to connect to the PostgreSQL instance via port `5432`. This eliminates the need to expose the database to the public Internet.

**Rationale:** As RDS houses critical data, controlling network traffic and connection sources is essential. A custom VPC allows the team to proactively configure Subnets, DB Subnet Groups, and security rules.

**Why not use the Default VPC or Public Access?** While the Default VPC is convenient for testing, it fails to clearly define the system's network boundaries. Public Access might be enabled temporarily for administrative tasks but is unsuitable for long-term configuration due to the increased attack surface. In the production architecture, RDS connectivity is restricted via VPC and Security Groups.

### AWS Secrets Manager

**Project Role:** Secrets Manager stores sensitive information required for RDS connectivity—at a minimum, the `username` and `password`. Manually created secrets may also include the `host`, `port`, `dbname`, and `engine`. Lambda retrieves the secret at runtime using an IAM Role; passwords are neither hard-coded in the source code nor committed to GitHub.

**Rationale:** Secrets are encrypted and centrally managed, with support for version history and credential rotation. When a password changes, the team simply updates the secret rather than modifying and redeploying the source code.

**Why not use Lambda Environment Variables?** Environment variables are suitable for non-sensitive configurations, such as table names or AWS Regions. Although Lambda supports environment variable encryption, moving passwords to Secrets Manager offers superior management, access control, and rotation capabilities.

**Why not use Systems Manager Parameter Store?** Parameter Store is suitable for configurations and simple secrets, and can be more cost-effective. However, the project selected Secrets Manager because RDS credentials require a specialized secret management mechanism and support for secret rotation.

### Amazon RDS for PostgreSQL and pgvector

**Role in the project:** RDS stores document metadata in the `documents` table, while content chunks and `vector(1024)` embeddings are stored in the `document_chunks` table. The `pgvector` extension performs similarity searches to support RAG.

**Rationale:** PostgreSQL supports relational data, SQL queries, and vector data within a single database. This suits the experimental scale of 2,000 vectors and facilitates convenient data inspection.

**Why not use DynamoDB for vectors:** DynamoDB is optimized for queries based on Partition Keys and Sort Keys, not for nearest-neighbor vector searches. **Why not use Amazon OpenSearch Service:** While OpenSearch is suitable for high search volumes and complex search requirements, it entails additional configuration, operational overhead, and costs; for the scope of this internship, PostgreSQL with pgvector offers a simpler solution.

### Amazon DynamoDB

**Role in the project:** The `ChatHistory-dev` table stores messages indexed by user access keys and chat sessions. Lambda functions handle the Create, Get, Update, and Delete operations for chat history.

**Rationale:** Chat history data has a flexible structure, is primarily queried by key, and does not require complex JOIN operations. DynamoDB offers low latency, automatic scaling, and requires no server administration.

**Why not store everything in RDS:** While RDS *can* store chat history, separating the data types allows each database to play to its strengths: RDS handles relational data and vectors, while DynamoDB handles key-based history queries. This approach also reduces the number of connections to RDS.

### AWS Lambda

**Role in the project:** Lambda initializes the RDS table, performs CRUD operations on chat history, generates and inserts vectors, conducts semantic searches, generates mock data, and runs benchmarks.

**Why choose it:** The tasks are short-lived and triggered on demand. Lambda requires no server management, scales automatically, and charges based on invocation count and execution duration.

**Why not use Amazon EC2 or ECS:** EC2 requires managing virtual machines, operating systems, and updates, and incurs costs while the instance is running. ECS is better suited for containers or long-running processes. Since the project scope involves short functions, Lambda is a more streamlined and cost-effective choice.

### Amazon Bedrock – Titan Text Embeddings V2

**Role in the project:** Bedrock converts text content into 1,024-dimensional embeddings using the `amazon.titan-embed-text-v2:0` model; these vectors are then stored and searched within pgvector.

**Why choose it:** It is a managed AI service with IAM integration that eliminates the need to self-host model servers. Titan Embeddings directly meets the requirement for generating vectors for RAG.

**Why not use Amazon SageMaker or external AI APIs:** SageMaker is suitable for training, customizing, or self-hosting models, but it is more complex than this project requires. External APIs introduce the overhead of key management and data transfer outside of AWS; Bedrock keeps the processing workflow within the AWS ecosystem.

### Amazon Cognito

**Role in the project:** Cognito is used to create a User Pool, configure sign-up/sign-in processes, and handle user management. This practical section covers only the authentication configuration you have implemented; it does not include full backend integration.

**Why choose this:** Cognito provides built-in user management, password policies, and token handling, eliminating the need to build custom account tables and authentication mechanisms.

**Why not write a custom login system:** Building your own authentication increases risks associated with improper password storage and insecure token management, and requires additional time for security testing.

### AWS IAM and Amazon CloudWatch

**Role in the project:** An IAM Role grants the Lambda function the specific permissions needed to access RDS via VPC, read secrets, interact with DynamoDB, invoke Bedrock, and write logs. CloudWatch Logs records execution results, response times, and errors.

**Why choose this:** These services integrate natively with Lambda. IAM supports the principle of least privilege, while CloudWatch enables performance monitoring and analysis without requiring a separate monitoring system.

| Requirement | Selected Service | Alternative Not Selected | Key Reason |
|---|---|---|---|
| Private network for RDS | VPC + Security Group | Default VPC/Public Access | Control over subnets and sources allowed to access port 5432 |
| RDS credential protection | Secrets Manager | Hard-coding/Env Vars/Parameter Store | Centralized secret management and rotation support |
| Document & vector data | RDS PostgreSQL + pgvector | DynamoDB/OpenSearch | SQL and vector data in one DB; suitable for pilot scale |
| Chat history | DynamoDB | Storing everything in RDS | Fast key-based queries, flexible schema, serverless |
| Task processing | Lambda | EC2/ECS | No server management; suitable for short-lived tasks |
| Embedding generation | Bedrock Titan V2 | SageMaker/External API | No need to self-host models; native IAM integration |
| User management | Cognito | Custom authentication | Reduced security risks and development time |
| Access control and logging | IAM + CloudWatch | Hard-coded keys / custom monitoring systems | Native integration, least privilege, and auditability |


## Expected Outcomes

- RDS operates with `pgvector`.
- DynamoDB successfully stores and queries chat history.
- CRUD Lambda functions execute successfully.
- Vector search returns the expected data segments.
- Performance metrics for RDS and DynamoDB are available.

# Implementation Details

1. [Frontend Architecture Specification](5.1.1%20-frontend-architecture/)
2. [Backend Architecture & RAG Pipeline Specification](5.1.2%20-backend-architecture/)
3. [Overall AWS Architecture Diagram](5.1.3%20-overall-aws-architecture/)
4. [List of AWS Services Used](5.1.4%20-aws-services-used/)