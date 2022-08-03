+++
title = "Amazon EBS"
description = "AWS Notes for the Certification Exam II"
date = "2022-07-28"
author = "mehmet sat"
+++



- Highly available and scalable **persistent** storage volumes that you can attach to your EC2 instances

## EBS Types

### SSDs

- **General Purpose SSDs**
    - **gp2:**
        - Suitable for **boot** disks and general applications
        - Up to **16000 IOPS** per volume
        - Up to **99.9%** durability
    - **gp3:**
        - Suitable for **high performance** applications
        - Predictable **3000 IOPS** baseline performance
        - **125 MiB/s** regardless of volume size
        - Up to **99.9%** durability
- **Provisioned IOPS SSDs**
    - **io1:**
        - Suitable for **OLTP(online transaction processing)** and **latency sensitive** applications
        - **50IOPS/GiB**
        - Up to **64000 IOPS** per volume
        - High performance and **most expensive**
        - Up to **99.9%** durability
    - **io2:**
        - Suitable for **OLTP(online transaction processing)** and **latency sensitive** applications
        - **500 IOPS/GiB**
        - Up to **64000 IOPS** per volume
        - **99.999%** durability
        - Latest generation Provisioned IOPS Volume
    
    ### HDDs
    
- **Throughput Optimized HDD**
    - **st1:**
        - Suitable for **Big Data, warehouses, ETL**
        - Max throughput is **500 MB/s** per volume
        - **Cannot be a boot volume**
        - Up to **99.9%** durability
- **Cold HDD**
    - **sc1:**
        - Max throughput of **250 MB/s** per volume
        - **Less frequently** accessed data
        - **Cannot be a boot volume**
        - **Lowest cost**
        - Up to **99.9%** durability

## EBS Volumes and Snapshots

- Volumes exist in EBS but **Snapshots** exist in **S3**.
- Snapshots are **point-in-time** photographs and **incremental** in nature
- For consistent snapshots stop the instance and detach the volume
- You can share snapshots between AWS accounts as well as between regions, but first you **need to copy** that snapshot to the target region
- You can **resize** EBS volumes **on-the-fly** as well as **changing the volume types**

## EBS vs Instance Store

- Instance store volumes sometimes called as **ephemeral storage**
- Instance store volumes **cannot be stopped**. If the underlying host fails, you will **lose your data**
- EBS-backed instances can be stopped. You will not lose the data on this instance if it is stopped.
- You can **reboot both EBS and instance store** volumes and you **will not lose your data**
- **By default** both root volumes will be **deleted on termination**. However with EBS you can choose to keep it on termination

<aside style="border:solid; border-radius:10px; margin:15px; padding:10px">
💡 AMI is just a blueprint for an EC2 instance

</aside>

## Encryption on EBS

- With encrypted volumes
    - Data at rest encrypted inside the volume
    - All data in transit are encrypted also
    - **All snapshots are encrypted**
    - All volumes **created from the snapshot are encrypted**
- To encrypt volumes
    - Create a snapshot of the unencrypted root device volume
    - Create a copy of this snapshot and select the encrypt option
    - Create an AMI from the encrypted copy
    - Launch new encrypted instance using this AMI
    

## EC2 Hibernation

- EC2 hibernation preserves the **in-memory RAM on persistent storage (EBS)**
- Much faster to boot up because you don’t need to reload operating system
- Instance RAM must be **less than 150 GB**
- Instance families include : C3, C4, C5, M3, M4, M5, R3, R4, R5
- Available for Windows, Amazon Linux 2 AMI, and Ubuntu
- Instanced cannot be hibernated for more than **60 days**
- Available for **on-demand and reserved instances**

## EFS (Elastic File System)

- Basically **distributed storage** that can be used by **multiple instances simultaneously**
- Supports the Network File System version 4(**NFSv4**) protocol
- Can support **thousands of concurrent NFS connections**
- Only pay the storage you used (no pre-visioning required)
- Data is stored **across multiple AZs**
- Can scale up to **petabytes**
- **Read-after-write** consistency

<aside style="border:solid; border-radius:10px; margin:15px; padding:10px">
💡 If you have a scenario based question around highly scalable, shared storage using NFS it is EFS

</aside>

## EFS vs FSx for Windows vs FSx for Lustre

- EFS → Distributed highly resilient storage for **Linux instances and Linux-based** applications
- Amazon FSx for Windows → Centralized storage for **Windows-based applications,** such as SharePoint, Microsoft SQL Server, Workspaces, IIS WebServer, or any other native Microsoft Application
- Amazon FSx for Lustre → **High-speed, high-capacity distributed storage**. Applications for HPC, financial modeling. FSx also store the **data at S3**

## General Storage Options Use Cases

- **S3** → Used for serverless object storage
- **Glacier** → Used for archiving objects
- **EFS** → Network File System for Linux Instances. Centralized storage solution for instances in multiple AZs
- **FSx for Lustre** → File storage for high-performance computing Linux file systems
- **EBS Volumes** → Persistent storage for EC2
- **Instance Store** → Ephemeral storage for EC2
- **FSx for Windows** → File storage for Windows instances , centralized solution across multiple AZs

## AWS BACKUP

- **Consolidation**: Consolidated environment for backing up AWS services e.g. EC2,EBS,FSx, AWS Storage Gateway
- **Organizations**: You can use AWS Organizations in conjunction with AWS Back up to back up your different AWS Services across multiple AWS accounts
- **Benefits**:
    - Centralized control
    - Automation with lifecycle policies
    - Better compliance with enforcing backup policies
    - Ensure your backups are encrypted and audit them once complete