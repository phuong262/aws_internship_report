---
title : "System Testing"
date : 2024-01-01
weight : 5
chapter : false
pre : " <b> 5.5 </b> "
---

After completing the Frontend and Backend deployment, the team tested the main functions of **Smart Docs AI** in the deployed environment. The testing scope focused on the two most important workflows: user authentication and document question answering using RAG.

The test cases were performed directly through the web interface at `smart-docs-jet.vercel.app`. Each result was compared with the input data to evaluate the system's accuracy and error-handling capability.

> **Test data:** the file `ke_hoach_gia_dinh_demo.pdf` contains a synthetic household budget, shopping list, family schedule, and Vung Tau travel plan. No real personal information is used.

### Contents

1. [Authentication Testing](5.5.1-Authentication/)
2. [Document Upload and RAG Testing](5.5.2-Document-RAG/)
