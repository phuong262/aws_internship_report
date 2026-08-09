---
title : "Setting up Amazon API Gateway"
weight : 5
chapter : false
pre : " <b> 5.4.5 </b> "
---

### 1. Initializing Amazon API Gateway
In this project, Amazon API Gateway is set up to serve as the central entry point and coordinator for the entire system.
*   **Initialization Details:** The system utilizes the REST API protocol and is named `AWS_test`.
*   **Endpoint Type:** The endpoint type is configured as "Regional" to optimize access within the deployment region.

### 2. Configuring Resources and Methods
The API system is designed with a clear separation of business functions, comprising specific resources and data manipulation methods:
*   **Resource `/chat`:** Handles chat-related tasks. It supports the `GET` method for retrieving data and the `POST` method for sending content.
*   **Resource `/delete-chat`:** Provides the `DELETE` method to remove specific chat sessions.
*   **Resource `/delete-document`:** Provides the `DELETE` method to handle the removal of documents from the system.
*   **Resource `/delete-session`:** Supports the `DELETE` method to clean up active sessions.
*   **Resource `/upload`:** Provides the `POST` method to accept data uploaded by the user.

### 3. Configuring CORS (Cross-Origin Resource Sharing)
To ensure the frontend can call the API seamlessly without being blocked by the browser, CORS configuration has been integrated:
*   The `OPTIONS` method has been set up on all key endpoints, including `/chat`, `/delete-document`, `/delete-session`, and `/upload`.
*   The `OPTIONS` method at the root path (`/`) is configured with the "Mock" integration type and "None" for authorization, enabling rapid responses to client preflight requests. ### 4. Deployment and Traffic Limiting
Upon completion of the configuration, the API has been successfully deployed to the production environment:
*   **Deployment Stage:** The entire configuration has been created and deployed to the environment named `devv1`.
*   **Invoke URL:** The Base URL provided for frontend integration is `https://wzie3iseyk.execute-api.ap-southeast-1.amazonaws.com/devv1`.
*   **System Protection:** Traffic limiting parameters have been configured to prevent overload (Throttling), with a Rate limit of 10,000 and a Burst limit of 5,000.