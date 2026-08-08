---
title : "Backend Architecture"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.1.2 </b> "
mermaid: true
---

### 1. Backend Directory Structure Explanation
The backend system does not utilize a monolithic architecture; instead, it adopts the Serverless Microservices standard on AWS. All business logic is decoupled into independent AWS Lambda functions, interconnected via an event-driven mechanism.

#### 1.1. Overall Structure

![](/images/5-Workshop/5.1-Workshop-overview/ImageFunction.png)
---

#### 1.2. Detailed Function Explanation

#### Document Ingestion Pipeline
This module operates entirely asynchronously, preventing the frontend from hanging while processing large PDF or image files.

* **`upload-files`**: Sits behind API Gateway. It receives a list of filenames from the frontend, generates a unique identifier (`document_id`), and returns an array of pre-signed URLs. The frontend uses these links to upload files directly to Amazon S3, thereby minimizing bandwidth load on API Gateway (which has a 10MB payload limit).
* **`textract-start`**: A Lambda function automatically triggered (via S3 Event Trigger) as soon as a file is successfully uploaded to S3. It initializes a document status record in Amazon DynamoDB and invokes Amazon Textract to begin text extraction.
* **`textract-result`**: Receives a signal (via SNS) upon Textract's completion. It retrieves the raw text output and executes a smart chunking algorithm (segmenting the text based on defined lengths without disrupting context). Finally, it sends these chunks to the Vector module. ---

#### Vector Management Module (Vector Operations & RDS pgvector)
Handles the transformation of natural language into numerical arrays (vectors) and manages specialized storage to optimize search speed.

* **`create-vector`**: Interfaces with the Amazon Bedrock service (using the `amazon.titan-embed-text-v2:0` model) to convert each text chunk into a 1024-dimensional vector embedding.
* **`rds-vector-insert`**: Connects to Amazon RDS PostgreSQL using the pure-Python `pg8000` library. Stores chunk content, metadata (file name, page number), and the vector field in the `document_chunks` table.
* **`rds-vector-search`**: Executes SQL queries to calculate similarity between the query vector and the vector store in the `document_chunks` table. Supports filtering by `user_id` to ensure data privacy in a multi-tenant environment.

---

#### Q&A Module (Chat & RAG Domain)
Manages real-time user interactions and orchestrates backend systems.

* **`chat-get-history` (API Get Handler)**: Aggregates data from two sources: retrieves the document list from the `Documents` table (for the frontend's left-hand panel display) and fetches message history for the current `session_id` from the `ChatHistory` table (for the right-hand conversation interface).
* **`chatbot-rag`**: The core of the RAG system. 
* Step 1: Receives the question and calls Bedrock to generate a vector. 
* Step 2: Calls the `rds-vector-search` function (via Boto3 Invoke) to retrieve the Top-K most relevant chunks. 
* Step 3: Packages these chunks into the prompt (within the `<context>` section). 
* Step 4: Sends the prompt to the Large Language Model (LLM) on Bedrock to generate a natural language response. * Step 5: Save the entire transaction to DynamoDB along with reference information.

---

### 2. Data Flow and Backend Architecture

{{< mermaid >}}
graph TD
%% Frontend Block
Client["Client (React Frontend)"]

%% API & Auth Block
APIGW["AWS API Gateway (/api)"]
Cognito["Amazon Cognito"]
Client -->|Authenticate| Cognito
Client -->|REST API Calls| APIGW

%% Flow 1: RAG Chat
subgraph Luong1 [Synchronous Q&A Flow]
APIGW -->|POST /chat| ChatbotRAG["Lambda: chatbot-rag"]
ChatbotRAG -->|1. Generate Question Vector| Bedrock1["Amazon Bedrock (Titan Embed)"]
ChatbotRAG -->|2. Invoke VectorSearch Lambda| VectorSearch["Lambda: rds-vector-search"]
VectorSearch -->|3. Cosine Similarity Search| RDS[("Amazon RDS PostgreSQL<br/>(pgvector)")]
ChatbotRAG -->|4. Generate Answer| Bedrock2["Amazon Bedrock (LLM)"]
ChatbotRAG -->|5. Save Chat History| DynamoChat[("DynamoDB<br/>(ChatHistory)")]
end

%% Flow 2: File Upload
subgraph Luong2 [Asynchronous Raw Data Processing Flow]
Client -. "Direct File PUT" .-> S3Bucket[("Amazon S3 Bucket")]
S3Bucket -->|S3 Event Trigger| TextractStart["Lambda: textract-start"]
TextractStart -->|Launch Textract| Textract["Amazon Textract"]
Textract -->|SNS Notification| TextractResult["Lambda: textract-result"]
TextractResult -->|Chunking & Invoke| CreateVector["Lambda: create-vector"]
CreateVector --> Bedrock1
CreateVector -->|Invoke| VectorInsert["Lambda: rds-vector-insert"]
VectorInsert --> RDS
end
{{< /mermaid >}}
<script src="https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js"></script>
<script>mermaid.initialize({startOnLoad:true});</script>
---

### 3. Interaction with AWS Infrastructure (AWS Services Summary)
| AWS Service | Purpose & Role in the Backend |
| :--- | :--- |
| **AWS Lambda** | Distributed serverless execution environment. Independent functions minimize the risk of system-wide outages. |
| **Amazon Bedrock** | Generative AI service responsible for generating responses (LLM) and creating vector embeddings (Amazon Titan Embeddings V2). |
| **AWS Cognito** | Manages user registration and authentication. |
| **Amazon S3** | Stores raw document files (e.g., PDFs, images). |
| **Amazon DynamoDB** | NoSQL database optimized for storing conversation history and user metadata with ultra-fast read/write speeds. |
| **Amazon RDS PostgreSQL** | Database featuring the powerful pgvector extension for high-speed indexing and semantic search. |
| **AWS API Gateway** | REST API gateway; integrates CORS protection and token authentication mechanisms prior to Lambda invocation. |
| **Amazon Textract** | OCR computer vision service; extracts text from challenging formats such as images or scanned PDFs. |