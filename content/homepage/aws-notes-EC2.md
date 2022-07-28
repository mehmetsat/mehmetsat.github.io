+++
title = "Amazon EC2"
description = "AWS Notes for the Certification Exam II"
date = "2022-07-27"
author = "mehmet sat"
+++

- EC2 is like a VM, hosted in AWS instead of your own data center
    - Select the capacity you need right now. Grow and shrink when you need
    - Pay for what you use

## EC2 types and Pricing options

- **On-Demand Instances**
    - Pay by the hour or the second
    - Great for flexibility, but expensive
- **Spot Instances**
    - Purchase unused capacity at a discount of up to %90
    - Prices fluctuate with supply and demand
    - Great for applications with flexible start and times
- **Reserved Instances**
    - Reserved capacity for 1 or 3 years
    - Up to %72 discount on the hourly charge
    - Great if you have known, fixed requirements
- **Dedicated Instances**
    - A physical EC2 server dedicated for your use
    - Great if you have server-bound licenses to reuse or compliance requirements
    
    ## AWS CLI
    
- **Least Privilege:** Always give your users the minimum amount of access required to do their job
- **Use Groups for permissions:** Create IAM groups and assign your users to groups. Group permissions are assigned using IAM Policy documents. Your users will automatically inherit the permissions of the group
- **Secret Access Key** : You will see this once. Don’t lose it. You will need to run aws configure again.

## IAM Roles

- The preferred option from the security perspective
- Roles allow you to provide access without the use of access key IDs and secret access keys
- You can update a policy and see the immediate effect
- You can attach and detach roles to your running EC2 instances without having to stop or terminate these instances

## Security Groups

- Changes to security groups take effect immediately
- You can have any number of instances within a security group
- you can have multiple security groups attached to EC2 instances
- All inbound traffic is blocked by default
- All outbound traffic is allowed

## Bootstrap Scripts

- A bootstrap script is a script that runs when the instance first runs.
- It passes user data to the EC2 instance and can be used to install applications(like web-servers and databases), as well as do updates and more

## Data vs Metadata

- User data are simply bootstrap scripts
- Metadata is data about your EC2 instances
- You can use bootstrap scripts to access metadata

## Networking with EC2

- **Elastic Network Interfaces (ENI):**
    - It is for basic networking comes default with EC2.
    - Perhaps you need separate management network from your production network or a separate logging network, and you need to do this at a low cost: Use multiple ENIs for each EC2 instance
- **Enhanced Networking:**
    - When you need speeds between 10Gbps and 100Gbps
    - Anywhere you need reliable, high throughput
- **Elastic Fabric Adapter:**
    - When you need to accelerate High Performance Computing(HPC) and machine learning applications or if you need to do an OS-bypass.
    - If you see a scenario question mentioning HPC or ML and asking what network adapter you want choose EFA

## Placement Groups

- **Cluster Placement Groups**
    - Low network latency, high throughput (In the same AZ)
- **Spread Placement Groups**
    - Individual critical EC2 instances to separate them into different regions
- **Partition Placement Groups**
    - Multiple EC2 instances: HDFS, HBase, and Cassandra

- A cluster placement group cant span multiple AZs, whereas a spread placement group and partition placement group can
- Only certain types of instances can be launched in a placement group: Compute optimized, GPU, memory optimized, storage optimized
- AWS recommends homogenous instances within cluster placement groups
- You can’t merge placement groups
- You can move an existing EC2 instance into a placement group if it is in stopped state.
- You can move or remove an instance using the AWS CLI or an AWS SDK, but you cannot do it via the console yet

## Dedicated Hosts

- It is important for licensing requirements
- An Amazon EC2 dedicated host is a physical server with EC2 instance capacity fully dedicated to your use. Dedicated Hosts allow you to use your existing per-socket, per-core, or per-VM software licenses e.g. Windows Server, Microsoft SQL Server, and SUSE Linux Enterprise Server

## Spot Instances and Spot Fleet

- Spot Instances save up to 90% of the cost of On-Demand Instances
- You can block Spot Instances from terminating by using Spot Block
- Useful for any type of computing where you don’t need persistent storage
- A Spot Fleet is a collection of Spot Instances and , optionally On Demand Instances