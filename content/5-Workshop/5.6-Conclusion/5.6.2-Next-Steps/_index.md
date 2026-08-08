---
title : "Next Steps & References"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 5.6.2 </b> "
---

The workshop has achieved its goal of deploying and testing SmartDocAI at a demo or pilot scale. This section outlines items to consider adding if you wish to move the system to a true production environment handling higher traffic volumes. ### 1. Production Deployment Considerations
- Deploy Lambda within a VPC with a NAT Gateway
- Add API Gateway resource policies (IP whitelisting) for administrative endpoints
- Implement per-user rate limiting (AWS WAF recommended)
- Enable Cognito MFA
- Implement blue/green deployment for Lambda

### 2. Cost Optimization
- Implement DynamoDB auto-scaling (or reserved capacity)
- Add CloudFront caching for API responses (on suitable endpoints)
- Lambda SnapStart (reduces cold start time to ~100ms) — *note: currently supports Java/.NET only; Python not yet supported*
- Compress documents before uploading (reduces S3 storage usage)

### 3. Extended Features
- Implement multi-region deployment (ap-southeast-1 Singapore)
- Real-time collaboration (WebSocket API)
- Document OCR support (Textract)
- Full-text search (OpenSearch)
- Email notifications (SES)
- Audit logs (CloudTrail + S3)

### 4. Additional References
- [AWS Serverless Application Model (SAM)](https://aws.amazon.com/serverless/sam/)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [Amazon Bedrock Best Practices](https://docs.aws.amazon.com/bedrock/)
- [LangChain Documentation](https://python.langchain.com/)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)

---

**Thank you for completing the SmartDocAI workshop!**