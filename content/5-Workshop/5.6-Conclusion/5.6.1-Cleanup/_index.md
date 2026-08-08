---
title : "Resource Cleanup"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.6.1 </b> "
---

### Important Note Before Deletion

Deleting the resources in this guide is irreversible: all data (user profiles, uploaded documents, vector databases, system logs) will be permanently lost, as the system does not have backup or snapshot mechanisms configured. If you need to retain data, perform the following steps before deletion:

- Export DynamoDB tables (Profile, Chat History).
- Download original PDF/Image files from Amazon S3.
- Dump vector data from Amazon RDS PostgreSQL.
- Export CloudWatch logs for future analysis.

---

### Cleanup Process - Resource Deletion Order

**Rule:** You must delete connections (Traffic/Triggers) first, then compute resources (Lambda), and finally storage resources (S3, RDS, DynamoDB).

**Summary of Stages:**

| Stage | Resources | Key Actions | Duration |
|-------|-----------|-------------|------|
| **Stage 1: Stop traffic** | CloudFront, API Gateway, S3 Triggers, SNS | Disable CloudFront (⏱️ wait 30 mins) → Delete API Gateway → Remove event triggers | ~35 mins |
| **Stage 2: Remove compute** | AWS Lambda | Delete 15 Lambda functions (Upload, Vector, ChatbotRAG...) | ~5 mins |
| **Stage 3: Delete data** | Amazon RDS, S3 Buckets, DynamoDB, Cognito | Empty S3 buckets → Delete RDS database → Delete DynamoDB tables → Delete Cognito User Pool | ~5 mins |
| **Stage 4: Monitoring** | CloudWatch Logs, IAM Roles | Delete Lambda log groups → Delete IAM roles with granted permissions | ~2 minutes |

**Total time:** ~47 minutes (primarily waiting for CloudFront to disable)

---

## Verification of cleanup

Quickly review the key services in the AWS Console (especially the Billing Dashboard) to ensure no lingering resources are incurring costs.

**Expected final state:** All commands above return an empty result or "not found" status.

---

## Troubleshooting common issues

### Issue 1: "Cannot delete S3 bucket - not empty"

**Cause:** The bucket still contains files or hidden files (versioned objects).

**Solution:**
```powershell
# Force empty bucket (including versioned objects)
aws s3api delete-objects `
--bucket document-chatbot-files-dev-273 `
--delete "$(aws s3api list-object-versions --bucket document-chatbot-files-dev-273 --query '{Objects: Versions[].{Key:Key,VersionId:VersionId}}' --output json)"

# Then delete bucket
aws s3api delete-bucket --bucket document-chatbot-files-dev-273 --region us-east-1
```

### Issue 2: "Cannot find the AWS Amplify app delete button or deletion fails"

**Cause:** Unlike other services that feature a delete button directly in the list view, Amplify's delete button is tucked away in the settings to prevent accidental deletion. Additionally, AWS blocks deletion if the app is currently undergoing an automated deployment or build process.

**Solution:**
1. Ensure the app is not currently building (if a build is in progress, enter the process and click **Cancel**).
2. In the AWS Amplify Console, select your app.
3. Look at the left-hand menu, scroll to the bottom, and select **App settings** > **General**.
4. Scroll to the bottom of the **General** page; you will see the red **Delete app** button. Click there and confirm to permanently delete.
---