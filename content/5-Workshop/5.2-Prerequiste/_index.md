---
title: "Preparation"
weight: 2
chapter: false
pre: "<b>5.2. </b>"
---

# Overview

## Accounts and Regions

- Use an IAM User instead of the Root account.
- Enable MFA for the account.
- Select the `ap-southeast-1` region to create the database.
- Select the `us-east-1` region to use Amazon Bedrock.
- Check AWS Credits, Cost Explorer, and AWS Budgets.

![AWS Region](/images/2-Proposal/region.png)

## IAM Role for Lambda

The IAM Role requires the following permission sets:

- Write logs to Amazon CloudWatch.
- Read from and write to the DynamoDB table.
- Invoke the Amazon Bedrock Runtime.
- Attach the Lambda function to the VPC.
- Read the specific secret containing RDS connection details from AWS Secrets Manager.

![Lambda IAM Role](/images/2-Proposal/policy.png)

## Initializing VPC and Security Groups

### Step 1: Create VPC

1. Open the **VPC Console** and select **Create VPC**.
2. Select **VPC and more** to create the VPC and subnets simultaneously.
3. Enter the VPC name: `document-chatbot-vpc-dev`.
4. Select an appropriate IPv4 CIDR, for example, `10.20.0.0/16`.
![Create VPC](/images/2-Proposal/vpc1.png)
5. Select at least two Availability Zones so that RDS can create the required DB Subnet Group.
6. Create private subnets for RDS and Lambda.
![Create VPC](/images/2-Proposal/vpc2.png)
![Create VPC](/images/2-Proposal/vpc3.png)
7. VPC endpoints -> S3 Gateway; select **Enable DNS hostnames** and **Enable DNS resolution**.
![Create VPC](/images/2-Proposal/vpc4.png)
8. Review the configuration and select **Create VPC**. 9. Select **View VPC**.
![Create VPC](/images/2-Proposal/vpcflow.png)
10. Display the newly created VPC.
![Create VPC](/images/2-Proposal/vpc.png)


### Step 2: Create DB Subnet Group

1. Open the **Amazon RDS Console**.
2. Select **Subnet groups** and then **Create DB subnet group**.
![DB subnet group](/images/2-Proposal/subnetg1.png)
3. Select the VPC `document-chatbot-vpc-dev` created in the previous step.
![DB subnet group](/images/2-Proposal/sng3.png)
4. Select subnets spanning at least two Availability Zones.
![DB subnet group](/images/2-Proposal/subnetg2.png)
5. Create the DB Subnet Group and use it when creating the RDS PostgreSQL instance.
![DB subnet group](/images/2-Proposal/subnetg4.png)

### Step 3: Create Security Groups for Lambda and RDS
Go to **VPC** -> **Security Groups** -> **Create**.

- **Lambda Security Group:** Name it `document-chatbot-lambda-rds-sg` and attach it to the Lambda functions that need to connect to RDS; typically, no Inbound Rule is required. Configuration:

![Lambda security group](/images/2-Proposal/lambdasg.png)
![Lambda security group](/images/2-Proposal/lambdasg2.png)

- **RDS Security Group:** Name it `document-chatbot-rds-sg` and add an Inbound Rule with type **PostgreSQL**, TCP port **5432**, and the **Lambda Security Group** as the Source. Do not set the Source for port `5432` to `0.0.0.0/0`. Referencing the Security Group ensures that only Lambda functions belonging to the authorized group can connect to the database.

![RDS inbound rule](/images/2-Proposal/rdssg1.png)
![RDS inbound rule](/images/2-Proposal/rdssg2.png)

### Step 4: Attach Lambda to VPC

1. Open the Lambda function that needs to connect to RDS. ![Lambda VPC configuration](/images/2-Proposal/vpclambda1.png)
![Lambda VPC configuration](/images/2-Proposal/vpclambda2.png)

2. Select **Configuration → VPC → Edit**.
3. Select the newly created VPC.
4. Select the appropriate private subnets.
5. Select the Lambda security group and save the configuration.
![Lambda VPC configuration](/images/2-Proposal/vpclambda3.png)

Lambda functions within a private subnet require appropriate connectivity to call Secrets Manager, DynamoDB, and Bedrock. You can use a NAT Gateway or the corresponding VPC Endpoints. This workshop describes the actual configured setup; do not claim to have created a NAT Gateway or VPC Endpoints if you have not actually done so.

## Using RDS-managed secrets

When initializing the Amazon RDS PostgreSQL instance, the "Manage master credentials in AWS Secrets Manager" option is enabled. Consequently, RDS automatically creates and manages the secret containing the Master User credentials. You do not need to create the secret manually.

1. Open the RDS instance.
2. Check the **Configuration** section.
![Secret k](/images/2-Proposal/sec1.png)
3. Open the **Master credentials** link to the secret managed by RDS.
![Secret k](/images/2-Proposal/sec2.png)
4. Copy the secret's ARN to configure `DB_SECRET_ARN` for the Lambda function.

## Amazon Bedrock

Embedding model used:

```text
Model ID: amazon.titan-embed-text-v2:0
Dimensions: 1024
Normalize: true
Region: us-east-1
```

## Source Code and Dependencies

Prepare the following Lambda functions:

- Lambda for RDS initialization.
- Lambda for chat history CRUD operations.
- Lambda for adding vectors.
- Lambda for vector search.
- Lambda for generating test data. ## Environment variables

Example of variables requiring configuration:

```text
DB_HOST = chatbot-postgres-dev.*****.ap-southeast-1.rds.amazonaws.com
DB_PORT = 5432
DB_NAME = chatbot_db
DB_SECRET_ARN = arn:aws:secretsmanager:ap-southeast-1:043272859712*****
CHAT_TABLE_NAME = ChatHistory-dev
BEDROCK_REGION = us-east-1
```

Always use `DB_SECRET_ARN`. You may omit `DB_HOST`, `DB_PORT`, and `DB_NAME` only if these fields actually exist within the secret's JSON.


### Implementation steps

1. [Preparing source code](5.2.1-source-code-preparation/)
2. [Preparing AWS account](5.2.2-aws-account-preparation/)
3. [Creating an IAM user](5.2.3-creating-an-IAM-user/)