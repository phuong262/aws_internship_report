---
title: "Week 4 Worklog"
date: 2026-08-08
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

**Period:** From **13/07/2026** to **18/07/2026**

### Week 4 Objectives

* Develop Textract processing for images and PDFs.
* Normalize blocks and combine extracted text into complete content.
* Complete the string-processing logic for Textract results.

### Tasks for This Week

| Day | Task | Start Date | Completion Date | Reference Material |
|---|---|---|---|---|
| 2 | - Analyze PAGE, LINE, WORD, Geometry, and relationships in Textract output | 13/07/2026 | 13/07/2026 | [Textract Response Objects](https://docs.aws.amazon.com/textract/latest/dg/how-it-works-document-layout.html) |
| 3 | - Filter LINE blocks, group them by page, and remove unnecessary data | 14/07/2026 | 14/07/2026 | |
| 4 | - Sort lines by Geometry<br>- Normalize spaces, line breaks, and Vietnamese text after OCR | 15/07/2026 | 15/07/2026 | |
| 5 | - Study asynchronous Textract processing for multi-page PDFs and its JobId workflow | 16/07/2026 | 16/07/2026 | [Asynchronous Operations](https://docs.aws.amazon.com/textract/latest/dg/async.html) |
| 6 | - Normalize output into JSON containing document ID, page number, and text<br>- Store processed data in S3 output | 17/07/2026 | 17/07/2026 | |
| 7 | - Test paragraph assembly and Vietnamese-character handling<br>- Record remaining edge cases | 18/07/2026 | 18/07/2026 | |

### Week 4 Achievements

* Extracted and assembled text in the correct relative order.
* Preserved page numbers for source tracing.
* Standardized the JSON output and completed image/PDF string-processing logic.
