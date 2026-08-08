---

title: "Frontend Deployment"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

In this section, the **team** will deploy the **SmartDocs AI** frontend to the AWS platform to bring the application into a real-world operating environment, allowing users to access the website quickly and reliably over the Internet. The deployment process not only ensures the storage and delivery of the application's static files but also optimizes access performance while establishing an automated deployment process to minimize manual operations when updating to new versions.

To implement the deployment, the team uses the following AWS services:

* **Amazon S3** to store the static files of the Frontend application after the build process.
* **Static Website Hosting** to configure the S3 Bucket as a static website accessible over the Internet.
* **Bucket Policy** and **Block Public Access** to configure appropriate access permissions, allowing users to access the website while maintaining security.
* **Amazon CloudFront** to distribute content through a CDN network, reducing latency, improving page loading speed, and enhancing the user experience.
* **AWS CodePipeline** combined with **GitHub** to build a CI/CD pipeline that automatically deploys a new version of the application whenever the source code is updated in the repository.

After completing this chapter, the system's Frontend will be successfully deployed on AWS and accessible through CloudFront with high performance. It will also support automatic, fast, and consistent application updates.

### Implementation Contents

