+++
title = "AWS Fundamentals"
description = "AWS Notes for the Certification Exam I"
date = "2022-07-24"
author = "mehmet sat"
+++

# AWS Fundamentals

- A **Region** is a physical location in the world that consists of two or more **Availability Zones(AZs)**
- An **AZ** is one or more discrete data centers — each with redundant power, networking, and connectivity — housed in separate facilities
- **Edge Locations**  are endpoints for AWS that are used for caching content. Typically, this consists of **CloudFront,** Amazon’s **Content Delivery Network. Edge locations is more than Availability Zones**
- **Shared Responsibility Model:** Ask this question for the answer if you are responsible or not
    - Can you do this yourself in the AWS Management Console?
    - If Yes, you are likely responsible.
        - Security Groups, IAM Users, patching EC2 operating systems, patching databases running on EC2, etc. (patching means fixing bugs improving system performance)
    - If Not, AWS is likely responsible.
        - Management of Data Centers, security cameras, cabling, patching RDS operating systems, etc.
    - **Encryption is a shared responsibility**
- Key Services to know for the Exam
    - **Compute: EC2, Lambda, Elastic Beanstalk**
    - **Storage: S3, EBS, EFS, FSx, Storage Gateway**
    - **Databases: RDS, DynamoDB, Redshift**
    - **Networking: VPCs, Direct Connect, Route 53, API Gateway, AWS Global Accelerator**
- **Before the exam read the white-paper Well-Architected Framework**