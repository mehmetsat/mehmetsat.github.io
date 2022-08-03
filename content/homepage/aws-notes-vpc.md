+++
title = "Virtual Private Clouds(VPC)"
description = "AWS Notes for the Certification Exam I"
date = "2022-08-03"
author = "mehmet sat"
+++


- You can think of VPCs as a logical data center in AWS
- It consists of :
    - Internet or Virtual Private Gateways
    - Route Tables
    - Network Access Control Lists
    - Subnets
    - Security Groups

## An Example of VPC Architecture

<figure>
  <img src="/aws-vpc/1.png" alt="Trulli" style="width:100%; align:center">
</figure>

- Here you can see a custom VPC created in us-east-1 region with the CIDR Block Range of 10.0.0.0/16

<aside   style="border:solid; border-radius:10px; margin:15px; padding:10px">
💡 You can think of a CIDR block as a group of IP Addresses that has the same prefix up to some bits. For example 10.0.0.0/16 represents the group of IPs that starts with the prefix 10.0.X.Y

An IP address consists of 4 x 8-bit numbers. So if the CIDR block is /16 that means first two numbers are the same for that block.

</aside>

- In this VPC, there are two subnets — one is public that has access to the internet, and the other one is private subnet
- We have 2 Network Access Control Lists one for the public and one for the private subnet. We should know that one subnet only can associate with one NACL.

## Network Access Translation (NAT Gateways)

- By default if you have created a Private Subnet, it cannot access to the internet and other services in the AWS
- By creating a NAT Gateway, you can access to internet and/or other AWS Services outside your subnet without allowing external services to initiate connection to your subnet

<figure>
  <img src="/aws-vpc/2.png" alt="Trulli" style="width:100%; align:center">
</figure>

- NATs are redundant in the Availability Zone
- It is sufficient for heavy workloads
- No need to patch, not associated with security groups, and automatically assigned a public IP Address
- If you have resources that share an NAT Gateway in different AZs, if NAT Gateway’s AZ is down, resources in the other AZs lose internet access

## Network Access Control Lists (NACL)

- Default NACLs → Your VPC comes with default NACL, and by default it allows all inbound and outbound traffic
- Custom NACLs → You can create custom NACLs, by default they deny all inbound and outbound traffic until you add rules
- Each subnet in a VPC must be associated with a NACL. If you didn’t associate it explicitly the subnet will automatically associated with default NACL
- You can block IP addresses using NACLs, not security groups
- You can associate a NACL with multiple subnets
- NACL contains a numbered list of rules that is executed from first to last.
- NACL have separate inbound and outbound rules in other words they are stateless

## Direct Connect

- Direct Connect directly connects your data center to AWS
- Useful for high-throughput workloads
- Helpful when you need stable and secure connection

## VPC Endpoints

- When you want to connect AWS Services without leaving the Amazon Internal Network
- 2 types of VPC Endpoints:
    - Gateway Endpoints → Support S3 and DynamoDB
    - Interface Endpoints → Support almost all services

## VPC Peering

- Allows you to connect a VPC with another via a direct connect network route using private IP addresses
- Instances behave as if they on the same private network
- You can peer VPCs with other AWS Accounts as well as the other VPCs in the same account
- Peering is in star configuration e.g. 1 central VPC peers with other VPCs. Transitive pairing is not allowed
- You can peer VPC between regions

## AWS Private Link

- It allows to peer tens to thousands VPCs
- Doesn’t require VPC peering, no route tables, NAT gateways, internet gateways etc.
- Requires a Network Load Balancer on the service VPC, and Elastic Network Interface(ENI) on the customer VPC

## AWS Transit Gateway

- It is used for simplifying network topologies by using root tables to communication between VPCs talk to one another
- It works with Direct Connect as well as VPN connections

<figure>
  <img src="/aws-vpc/3.png" alt="Trulli" style="width:100%; align:center">
</figure>

## VPN Hub

- It simplifies VPN Network topology, it allows to talk from different locations with the same VPN hub