---
title: "Week 6 Worklog"
date: 2026-08-08
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

**Period:** From **27/07/2026** to **01/08/2026**

### Week 6 Objectives

* Complete error handling and monitoring for the S3, Lambda, and Textract flow.
* Review IAM permissions and test multiple document types.
* Complete the assigned document-processing component for integration.

### Tasks for This Week

| Day | Task | Start Date | Completion Date | Reference Material |
|---|---|---|---|---|
| 2 | - Standardize CloudWatch Logs by document ID, status, and processing step | 27/07/2026 | 27/07/2026 | [CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/) |
| 3 | - Handle invalid files, missing permissions, and Textract errors<br>- Add controlled retries | 28/07/2026 | 28/07/2026 | [Lambda Error Handling](https://docs.aws.amazon.com/lambda/latest/dg/invocation-retries.html) |
| 4 | - Test PNG, JPEG, and multi-page PDF files<br>- Test spaces, Vietnamese characters, and URL-encoded object keys | 29/07/2026 | 29/07/2026 | |
| 5 | - Compare processed text with source documents<br>- Fix ordering, whitespace, line-break, and duplication issues | 30/07/2026 | 30/07/2026 | |
| 6 | - Review least-privilege IAM permissions for S3, Lambda, Textract, SNS, and CloudWatch | 31/07/2026 | 31/07/2026 | [AWS IAM](https://docs.aws.amazon.com/iam/) |
| 7 | - Test the complete flow with the team<br>- Document the input/output contract and hand off the component for integration | 01/08/2026 | 01/08/2026 | |

### Week 6 Achievements

* Standardized logging and added error handling and retries.
* Successfully processed images and multi-page PDFs, including Vietnamese filenames.
* Reviewed IAM permissions and completed the document-processing handoff.
