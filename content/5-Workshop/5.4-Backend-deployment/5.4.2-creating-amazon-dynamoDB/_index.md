---
title: "Creating Amazon DynamoDB"
weight: 2
chapter: false
pre: "<b> 5.4.2 </b>"
---

## Create the `ChatHistory-dev`, `Documents-dev`, and `Users-dev` tables.

| Table | Partition Key | Sort Key | Function |
| --- | --- | --- | --- |
| `ChatHistory-dev` | `sessionId (S)` | `messageKey (S)` | Stores chat history |
| `Documents-dev` | `documentId (S)` | None | Stores document metadata |
| `Users-dev` | `userId (S)` | None | Stores user information |

1. Open Amazon DynamoDB.
2. Select **Create table**.
3. Enter the table name and configure the Partition Key and Sort Key. Ensure the keys for each table are set correctly.
![DynamoDB table active](/images/5-Workshop/5.4-Backend-deployment/DynamoDb/12.png)
4. Create the table and wait for the status to become **Active**.
![DynamoDB table active](/images/5-Workshop/5.4-Backend-deployment/DynamoDb/11.png)

## Data Design

# AMAZON DYNAMODB

Amazon DynamoDB is used to store chat history, document metadata, and user information.

In this system, vector embedding data is not stored in DynamoDB; instead, it is stored in the `document_chunks` table of Amazon RDS PostgreSQL using the `pgvector` extension.

The DynamoDB tables used are:

- `ChatHistory-dev`: stores chat history and session information.
- `Documents-dev`: stores document metadata.
- `Users-dev`: stores user profile information.

---

## Creating DynamoDB tables

The tables are designed as follows:

| Table | Partition Key | Sort Key | Function |
| --- | --- | --- | --- |
| `ChatHistory-dev` | `sessionId (S)` | `messageKey (S)` | Stores chat history |
| `Documents-dev` | `documentId (S)` | None | Stores document metadata |
| `Users-dev` | `userId (S)` | None | Stores user information |

### Implementation Steps

1. Open the **AWS Management Console**.
2. Search for and select the **Amazon DynamoDB** service.
3. In the left-hand menu, select **Tables**.
![DynamoDB](/images/5-Workshop/5.4-Backend-deployment/DynamoDb/1.png)

4. Select **Create table**.
5. Enter the table name in the **Table name** field.
6. Enter the **Partition key** according to the design for each table.
7. For the `ChatHistory-dev` table, also enter the **Sort key**:
![DynamoDB](/images/5-Workshop/5.4-Backend-deployment/DynamoDb/2.png)
![DynamoDB](/images/5-Workshop/5.4-Backend-deployment/DynamoDb/3.png)
8. Select **Create table**.


```text
messageKey
|---|---|
| `userId` | User ID |
| `sessionId` | Chat session ID |
| `messageId` | Message ID |
| `messageKey` | Key based on `createdAt#messageId` |
| `role` | `user` or `assistant` |
| `content` | Message content |
| `references` | Document sources |
| `createdAt` | Creation timestamp |
| `updatedAt` | Update timestamp |
```
![DynamoDB sample item](/images/5-Workshop/5.4-Backend-deployment/DynamoDb/4.png)

## Verifying the Table

- Open **Explore table items**.
- Check keys and attributes.
- Filter by user or chat session.
- Check message order.

> **Expected result:** Data for each session can be queried and sorted by time. ![DynamoDB explore items](/images/5-Workshop/5.4-Backend-deployment/DynamoDb/5.png)

## Verifying Data
Verifying DynamoDB data
1. Open AWS DynamoDB.
2. Select the table to verify: `ChatHistory-dev`.
3. Check the settings for `ChatHistory-dev` – Items returned.

![DynamoDB table active](/images/5-Workshop/5.4-Backend-deployment/DynamoDb/13.png)

Verifying RDS data
1. Open AWS Lambda.
2. After completing the `rds-init-dev` Lambda function -> Deploy -> Test.
3. Data = `{}` -> Test.

![DynamoDB table active](/images/5-Workshop/5.4-Backend-deployment/DynamoDb/14.png)