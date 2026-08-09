---
title : "Creating an Amazon Cognito User Pool"
weight : 1
chapter : false
pre : " <b> 5.4.1 </b> "
---

**Amazon Cognito User Pool** helps build an identity management and user authentication system, as well as secure API endpoints for the SmartDocAI application.

### 1. Purpose of Creation
Amazon Cognito User Pool acts as a fully managed user directory on the AWS platform. Integrating Cognito enables the system to:
*   Securely store user account information (Email, Password).
*   Issue authentication tokens (**JSON Web Tokens - JWT**) upon successful login, which the frontend can attach to API requests.
*   Reduce the development burden of implementing password encryption mechanisms and session management at the backend layer.

### 2. Configuration Steps on the AWS Console

*   **Step 1 - Create User Pool:**
Create a new User Pool via the AWS Cognito interface. Configure **Email** as the primary sign-in method and establish password standards (requiring special characters, uppercase letters, numbers, and a minimum length) to ensure basic account security.
![Cognito User Pool](/images/5-Workshop/5.4-Backend-deployment/cognito.jpeg)
*   **Step 2 - Configure Sign-up Experience:**
Enable the email verification feature to validate email addresses when users register for new accounts. (as shown above)

*   **Step 3 - Integrate Lambda Trigger (User Post Confirmation):**
To synchronize newly registered user data to the system database, configure the `Post Confirmation` event to trigger the `user-post-confirmation-dev` Lambda function. Once a user successfully authenticates their account, this Lambda function automatically executes to record metadata into the management table.

### 3. Results Achieved
Upon completing the configuration and deployment, the system provides key identifiers—specifically the `User Pool ID` and `Client ID`. These parameters are integrated into the environment configuration files for the frontend and API Gateway, enabling the system to accurately distinguish valid requests and completely block unauthorized, unauthenticated access.