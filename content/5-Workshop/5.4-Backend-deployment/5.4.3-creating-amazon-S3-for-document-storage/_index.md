---
title: "Creating an Amazon S3 Bucket for Raw Document Storage"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 5.4.3 </b> "
---

In this section, the team uses Amazon S3 to store raw PDF documents and images uploaded to the Smart Docs AI system.

### S3 Bucket Information

| Property | Value |
|---|---|
| Bucket Name | `document-chatbot-files-dev-273` |
| AWS Region | Asia Pacific (Singapore) - `ap-southeast-1` |
| Bucket Versioning | Disabled |
| Object Ownership | Bucket owner enforced |
| ACL | Disabled |
| Block Public Access | All enabled |
| Default Encryption | SSE-S3 - Amazon S3 managed keys |

### Storage Structure

The bucket is organized into two main folders:

```text
document-chatbot-files-dev-273/
├── uploads/
└── textract-output/
```

- `uploads/`: Stores raw PDF files and images uploaded by users.
- `textract-output/`: Stores results generated during the document content extraction process.

### Steps to Create the Bucket

1. Navigate to **Amazon S3** in the AWS Management Console.
2. Select **Create bucket**.
3. Enter the bucket name: `document-chatbot-files-dev-273`.
4. Select the region: **Asia Pacific (Singapore) - ap-southeast-1**.
5. Select **ACLs disabled** and **Bucket owner enforced**.
6. Keep **Block all public access** enabled.
7. Select default encryption: **Server-side encryption with Amazon S3 managed keys (SSE-S3)**.
8. Keep **Bucket Versioning** disabled.
9. Select **Create bucket**.
10. Inside the newly created bucket, create two folders: `uploads/` and `textract-output/`. ### Current Configuration

When a new object is created in the `uploads/` directory, an S3 Event Notification triggers the `textract-start-dev` Lambda function to initiate document processing. The bucket's CORS configuration permits `PUT`, `POST`, `GET`, and `HEAD` methods, enabling the application to upload documents and access files based on granted permissions.

### Outcome

Upon completion, the system features a private S3 bucket that stores both raw documents and processing results. Objects are automatically encrypted using SSE-S3, are not publicly accessible, and are ready for the next stage of document processing.