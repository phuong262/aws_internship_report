---
title : "Backend Deployment"
date : 2024-01-01
weight : 4
chapter : false
pre : " <b> 5.4. </b> "
---

In this section, the team will deploy the entire backend processing system and supporting service infrastructure (utilizing Microservices and Serverless architectures) for the Smart Document Chatbot application on AWS. The deployment process encompasses provisioning database services (NoSQL and Vector Database), setting up source document storage and user identity management, building a REST API Gateway, implementing the RAG Engine logic via 15 independent AWS Lambda functions, and integrating AI services such as Amazon Bedrock and Amazon Textract.

### AWS Services Utilized

- **Amazon Cognito**: Manages user sign-up/sign-in, issues JWT tokens for API security, and automatically triggers Lambda functions to synchronize new user data.
- **Amazon DynamoDB**: A high-speed NoSQL database used to store user profiles, document metadata, and complete chat session histories.
- **Amazon S3**: Securely stores users' source document files (PDFs, images) via a direct upload mechanism using Pre-signed URLs.
- **Amazon RDS PostgreSQL**: Serves as the core Vector Database (via the `pgvector` extension), storing text chunks and 1024-dimensional vector embeddings to support high-speed Cosine Similarity search queries.
- **AWS Lambda & Amazon SNS**: Builds a distributed backend system comprising 15 independent microservice functions (packaged as Zip files). Combines an Event-Driven architecture with SNS to automate asynchronous document processing workflows.
- **Amazon API Gateway**: Provides a secure REST API interface, integrates a Cognito Authorizer for access control, and configures strict CORS settings for the frontend. - **Amazon Bedrock & Amazon Textract**: The AI ​​core of the system. Textract handles complex text extraction (OCR), while Bedrock generates vector embeddings (Titan Embeddings V2) and produces natural language responses (Nova Lite).

---

### Implementation Steps

1. [Initialize Amazon Cognito User Pool](5.4.1-creating-amazon-cognito/)
2. [Creating Amazon DynamoDB](5.4.2-creating-amazon-dynamoDB/)
3. [Create Amazon S3 for raw document storage](5.4.3-creating-amazon-S3-for-document-storage/)
4. [Initialize Amazon RDS PostgreSQL & pgvector](5.4.4-creating-amazon-rds-pgvector/)
5. [Set up Amazon API Gateway](5.4.5-creating-API-gateway/)
6. [Integrate API Gateway with the frontend (AWS Amplify)](5.4.6-integrating-api-gateway-frontend/)