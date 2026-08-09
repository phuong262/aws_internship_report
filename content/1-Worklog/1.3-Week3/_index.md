---
title: "Week 3 Worklog"
date: 2026-08-08
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

**Period:** From **06/07/2026** to **11/07/2026**

### Week 3 Objectives

* Study Amazon S3, AWS Lambda, and Amazon Textract in depth.
* Design and test the S3 document-ingestion flow.
* Build a Lambda function that validates uploaded files and invokes Textract.

### Tasks for This Week

| Day | Task | Start Date | Completion Date | Reference Material |
|---|---|---|---|---|
| 2 | - Review S3 buckets, object keys, prefixes, and metadata<br>- Design logical areas for source files, processing data, and output | 06/07/2026 | 06/07/2026 | [Amazon S3](https://docs.aws.amazon.com/s3/) |
| 3 | - Configure S3 Event Notifications to invoke Lambda for new documents<br>- Limit triggers by prefix and event type | 07/07/2026 | 07/07/2026 | [S3 Event Notifications](https://docs.aws.amazon.com/AmazonS3/latest/userguide/EventNotifications.html) |
| 4 | - Parse bucket, object key, size, and metadata from an S3 event<br>- Review least-privilege IAM permissions | 08/07/2026 | 08/07/2026 | [Using Lambda with S3](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html) |
| 5 | - Validate supported file types<br>- Decode URL-encoded object keys and Vietnamese filenames | 09/07/2026 | 09/07/2026 | |
| 6 | - Call synchronous Textract APIs for PNG/JPEG samples<br>- Read PAGE, LINE, and WORD blocks | 10/07/2026 | 10/07/2026 | [Amazon Textract API](https://docs.aws.amazon.com/textract/latest/dg/API_Reference.html) |
| 7 | - Save raw Textract output to S3<br>- Review CloudWatch Logs and identify issues with multi-page PDFs<br>- Attend **Cloud Architect x Meet up 11/07** at the AWS office; observe the KLKAT vs. Ngũ Đại Hiệp competition, learn AWS certification tips, and explore Frontier Agent | 11/07/2026 | 11/07/2026 | [CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/), [Event 2](../../4-EventParticipated/4.2-Event2/) |

### Week 3 Achievements

* Completed an S3 Event Notification → Lambda → Textract prototype.
* Extracted text from sample images and stored raw results in S3.
* Added object-key decoding and prefix filtering to prevent unintended invocation loops.
* Attended Cloud Architect x Meet up and learned about AWS certification strategies and Frontier Agent applications in security.
