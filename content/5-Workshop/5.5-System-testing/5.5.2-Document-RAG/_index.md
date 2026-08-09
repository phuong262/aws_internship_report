---
title : "Document Upload and RAG Testing"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.5.2 </b> "
---

### Test Objective

This section tests the main Smart Docs AI workflow, including creating and deleting chat sessions, uploading multiple PDFs, selecting a document, and receiving answers based on the selected content. Each response is compared directly with its source PDF to evaluate RAG accuracy and document-context isolation.

### Test Data

The test document is the three-page file `ke_hoach_gia_dinh_demo.pdf`. It describes a synthetic household budget and schedule, including expenses, appointments, and a planned trip to Vung Tau. All names and figures in the document are fictional.

The reference information includes:

- Remaining emergency fund: `4,400,000 VND`.
- Largest expense: house rent, approximately `34%` of the planned expenditure.
- If heavy rain occurs on `25/07/2026`, the Vung Tau trip is moved to `01/08/2026`.

### Test Procedure

1. Sign in to Smart Docs AI.
2. Select **New session** to create a conversation.
3. Select **Upload document** and upload `ke_hoach_gia_dinh_demo.pdf`.
4. Upload a second document whose content differs from the household document.
5. Verify that both documents appear in the left panel.
6. Select each document in turn and ask a question about its content.
7. Verify that the answer changes with the selected document and does not use information from the other file.
8. Ask questions involving numerical retrieval, summarization, and a conditional situation in the household document.
9. Ask a question whose time scope does not exist in the document to test unsupported-answer handling.
10. Create a temporary chat session, delete it, and verify that the session history is updated.
11. Compare every response with the corresponding PDF.

### Test Results

| ID | Question or action | Expected result | Actual result | Status |
|---|---|---|---|---|
| DOC-01 | Create a new chat session | A new session is created and saved in history | The session appears under **Session history** | Pass |
| DOC-02 | Upload the PDF | The document is accepted and attached to the session | The file appears under **Documents (1)** and can be selected | Pass |
| DOC-03 | Upload two documents with different content | Both files are stored and displayed in the document list | Both documents appear and can be selected independently | Pass |
| DOC-04 | Select the first document and ask a question | The answer uses only the first document | The system answers correctly from the selected first document | Pass |
| DOC-05 | Switch to the second document and ask a question | The context switches to the second document | The answer follows the second document without mixing previous content | Pass |
| DOC-06 | Delete a chat session | The session is removed from history | **Session history** is updated after deletion | Pass |
| RAG-01 | `How much emergency money does the family have left?` | Return `4,400,000 VND` | The system returns the correct emergency amount of `4,400,000 VND` | Pass |
| RAG-02 | `What is the largest expense?` | Identify house rent and approximately 34% | The system states that house rent represents about 34% of planned expenditure | Pass |
| RAG-03 | `How does the Vung Tau plan change if it rains on 25/07?` | Move the trip to `01/08/2026` | The system returns the correct replacement date of `01/08/2026` | Pass |
| RAG-04 | `What was the largest expense during the past year?` | Do not provide an answer because the document contains no “past year” data | The system reports that the information cannot be found in the document | Pass |

### Evaluation

The documents were uploaded and correctly associated with the chat session. With two documents available, the user could switch the selected document and the system used the corresponding content for its answer. This result confirms that retrieval is scoped to the selected document and that contexts are not mixed between files.

The system accurately answered questions involving numerical retrieval, the largest expense, and a conditional travel plan. When the question referred to a “past year” scope that was absent from the document, the system did not make an unsupported assumption and instead reported that the information could not be found. The session deletion function also worked correctly and immediately updated the chat history.

The results show that the RAG mechanism used the selected document correctly and limited unsupported answers. The interface maintained the document list, supported context switching, and allowed the user to manage chat history through session deletion.
