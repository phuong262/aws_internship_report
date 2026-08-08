---
title : "Bước tiếp theo & Tài liệu tham khảo"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 5.6.2 </b> "
---

Workshop đã hoàn thành mục tiêu triển khai và kiểm thử SmartDocAI ở quy mô demo/thực tập. Phần này liệt kê các hạng mục nên cân nhắc bổ sung nếu muốn đưa hệ thống lên môi trường production thực sự với traffic lớn hơn.

### 1. Các cân nhắc khi triển khai Production
- Triển khai Lambda trong VPC kèm NAT Gateway
- Thêm API Gateway resource policy (IP whitelist) cho các endpoint quản trị
- Triển khai rate limiting theo từng user (khuyến nghị dùng AWS WAF)
- Bật Cognito MFA
- Triển khai blue/green deployment cho Lambda

### 2. Tối ưu chi phí
- Triển khai DynamoDB auto-scaling (reserved capacity)
- Thêm CloudFront caching cho API response (ở những endpoint phù hợp)
- Lambda SnapStart (giảm cold start còn ~100ms) — *lưu ý: hiện chỉ hỗ trợ Java/.NET, chưa hỗ trợ Python*
- Nén tài liệu trước khi upload (giảm dung lượng S3)

### 3. Tính năng mở rộng
- Triển khai multi-region (ap-southeast-1 Singapore)
- Cộng tác thời gian thực (WebSocket API)
- Hỗ trợ OCR tài liệu (Textract)
- Tìm kiếm full-text (OpenSearch)
- Thông báo qua email (SES)
- Audit logs (CloudTrail + S3)

### 4. Tài liệu tham khảo thêm
- [AWS Serverless Application Model (SAM)](https://aws.amazon.com/serverless/sam/)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [Amazon Bedrock Best Practices](https://docs.aws.amazon.com/bedrock/)
- [LangChain Documentation](https://python.langchain.com/)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)

---

**Cảm ơn bạn đã hoàn thành workshop SmartDocAI!**