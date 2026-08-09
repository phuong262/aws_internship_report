---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

### Overview

This chapter details the step-by-step process of building, configuring, deploying, testing, and automating the complete SmartDocAI application on AWS Serverless infrastructure.

The system combines a Serverless Container Architecture using AWS Lambda (FastAPI Container), Amazon API Gateway, AWS Cognito, Amazon DynamoDB, Amazon S3, AWS CloudFront, and advanced Generative AI models from Amazon Bedrock (Titan Embeddings V2 & Qwen 3 Next 80B A3B).

Learners will practice from resource preparation, Docker Container packaging, cloud service configuration, IAM security permission setup, CI/CD automation pipeline integration via AWS CodePipeline/CodeBuild, to load testing and resource cleanup.

---

#### Detailed Workshop Guide Table of Contents

1. **[Workshop Overview](5.1-Workshop-overview/)**
   - 5.1.1 [Frontend Architecture Specification](5.1-Workshop-overview/5.1.1%20-frontend-architecture/)
   - 5.1.2 [Backend Architecture & RAG Pipeline Specification](5.1-Workshop-overview/5.1.2%20-backend-architecture/)
   - 5.1.3 [Overall AWS Architecture Diagram](5.1-Workshop-overview/5.1.3%20-overall-aws-architecture/)
   - 5.1.4 [AWS Services Summary](5.1-Workshop-overview/5.1.4%20-aws-services-used/)

2. **[Prerequisites](5.2-Prerequiste/)**
   - 5.2.1 [Source Code Preparation](5.2-Prerequiste/5.2.1-source-code-preparation/)
   - 5.2.2 [AWS Account & Amazon Bedrock Access Setup](5.2-Prerequiste/5.2.2-aws-account-preparation/)
   - 5.2.3 [IAM User Creation & AWS CLI Configuration](5.2-Prerequiste/5.2.3-creating-an-IAM-user/)

3. **[Frontend SPA Deployment](5.3-Frontend-deployment/)**


4. **[AWS Backend Infrastructure Deployment](5.4-Backend-deployment/)**
   - 5.4.1 [Creating an Amazon Cognito User Pool](5.4-Backend-deployment/5.4.1-creating-amazon-cognito/)
   - 5.4.2 [Creating Amazon DynamoDB](5.4-Backend-deployment/5.4.2-creating-amazon-dynamoDB/)
   - 5.4.3 [Creating Amazon S3 for raw document storage](5.4-Backend-deployment/5.4.3-creating-amazon-S3-for-document-storage/)
   - 5.4.4 [Creating Amazon RDS PostgreSQL & pgvector](5.4-Backend-deployment/5.4.4-creating-amazon-rds-pgvector/)
   - 5.4.5 [Setting up Amazon API Gateway](5.4-Backend-deployment/5.4.5-creating-API-gateway/)
   - 5.4.6 [Integrating API Gateway with the Frontend (AWS Amplify)](5.4-Backend-deployment/5.4.6-integrating-api-gateway-frontend/)

5. **[System Testing](5.5-System-testing/)**
   - 5.5.1 [Authentication Testing](5.5-System-testing/5.5.1-Authentication/)
   - 5.5.2 [Document Upload & RAG Testing](5.5-System-testing/5.5.2-Document-RAG/)

6. **[Conclusion](5.6-Conclusion/)**
   - 5.6.1 [Resource Cleanup](5.6-Conclusion/5.6.1-Cleanup/)
   - 5.6.2 [Next Steps & References](5.6-Conclusion/5.6.2-Next-Steps/)
