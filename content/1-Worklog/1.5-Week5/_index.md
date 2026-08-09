---
title: "Week 5 Worklog"
date: 2026-08-08
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

**Period:** From **20/07/2026** to **25/07/2026**

### Week 5 Objectives

* Complete asynchronous Textract processing for multi-page PDFs.
* Build the Lambda function that retrieves results and normalizes text.
* Integrate the processed output with downstream system components.

### Tasks for This Week

| Day | Task | Start Date | Completion Date | Reference Material |
|---|---|---|---|---|
| 2 | - Invoke `StartDocumentTextDetection` and retain the `JobId` and document metadata | 20/07/2026 | 20/07/2026 | [Textract Asynchronous Operations](https://docs.aws.amazon.com/textract/latest/dg/async.html) |
| 3 | - Configure SNS completion notifications<br>- Trigger a result-processing Lambda and retrieve output by `JobId` | 21/07/2026 | 21/07/2026 | [Amazon SNS](https://docs.aws.amazon.com/sns/) |
| 4 | - Handle pagination in `GetDocumentTextDetection` and collect all blocks | 22/07/2026 | 22/07/2026 | [GetDocumentTextDetection](https://docs.aws.amazon.com/textract/latest/dg/API_GetDocumentTextDetection.html) |
| 5 | - Sort and merge fragmented lines and paragraphs<br>- Normalize spaces, line breaks, and Vietnamese characters | 23/07/2026 | 23/07/2026 | |
| 6 | - Produce document ID, page number, and normalized text<br>- Hand the result to the vector-storage and database components owned by another member | 24/07/2026 | 24/07/2026 | |
| 7 | - Test the complete S3 → Lambda → Textract → SNS → result Lambda flow<br>- Review CloudWatch Logs and fix integration errors | 25/07/2026 | 25/07/2026 | [CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/) |

### Week 5 Achievements

* Completed asynchronous Textract processing and pagination for multi-page PDFs.
* Combined fragmented text into normalized content.
* Successfully integrated the output with the next system component.
