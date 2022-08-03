+++
title = "AWS Databases"
description = "AWS Notes for the Certification Exam I"
date = "2022-07-30"
author = "mehmet sat"
+++


## RDS(Relational Database Service)

- 6 different types :
    - SQL Server
    - Oracle
    - MySQL
    - PostgreSQL
    - MariaDB
    - Amazon Aurora
- RDS is generally used for OLTP(online transaction processing) workloads
    - Great for processing lots of small transactions like customer orders, banking transactions, payments, and booking systems
- It is not suitable for OLAP(online analytics processing) workloads. There are better alternatives like Amazon Redshift for OLAP.

### Read Replicas

- It is good for scaling the read performance, not disaster recovery!!
- Requires automatic back-up to be enabled.
- Up to 5 read replicas for each db instance is allowed

### Multi-AZ vs Read Replicas

| Multi-AZ | Read Replicas |
| --- | --- |
| An exact copy of your production database in another Availability Zone | A read-only copy of your database in the same or different AZ, even different region |
| Used for disaster recovery | Used to increase read performance |
| In the even of failure, RDS will automatically failover to the standby instance | Great for read-heavy workloads and takes the load off from your primary database for read-only workloads e.g. suitable for business intelligence |

## Amazon Aurora

- This is Amazon’s proprietary database that compatible with both MySQL and PostgreSQL.
- As a default you have at least 6 copies of your database in at least 3 different Availability Zone
- You can share Aurora Snapshots with other AWS Accounts
- 3 types of replicas available:
    - Aurora Replicas → Automatic failover is only available for this type of replicas
    - MySQL Replicas
    - PostgreSQL Replicas
- Aurora has automated back-ups turned on by default. You can also take snapshots with Aurora.
- There is Aurora Serverless for relatively simple, cost-effective option for infrequent, intermittent, or unpredictable workloads

## DynamoDB

- Amazon’s solution for NoSQL databases
- It is stored on SSD storage
- It is spread across 3 geographically distinct databases
- Eventually consistent reads are enabled by default
- You can also enable strongly consistent reads optionally

<aside  style="border:solid; border-radius:10px; margin:15px; padding:10px">
💡 

**Eventually consistent reads** → Consistency usually reach to all copies of data within a second. Repeating read after a short time should return the updated data and this gives you the best read performance

**Strongly consistent reads →** It returns a result reflects all writes that received a successful response prior to the read

</aside>

## DynamoDB Transactions

- It is a method for applying consistency ‘all-or-nothing’
- It is good for financial transactions, fulfilling orders
- 3 options for reads:
    - eventual consistency
    - strong consistency
    - transactional
- 2 options for write
    - standart
    - transactional
- It is limited to 25 items or 4MB of data
- It is very good for scenarios that require ACID consistency

<aside style="border:solid; border-radius:10px; margin:15px; padding:10px">
💡 ACID Consistency for Databases

**Atomicity**. In a transaction involving two or more discrete pieces of information, either all of the pieces are committed or none are.

**Consistency**. A transaction either creates a new and valid state of data, or, if any failure occurs, returns all data to its state before the transaction was started.

**Isolation**. A transaction in process and not yet committed must remain isolated from any other transaction.

**Durability**. Committed data is saved by the system such that, even in the event of a failure and system restart, the data is available in its correct state.

</aside>

## DynamoDB Back-ups

- Full back-ups for any time
- Zero impact on table performance or availability
- Consistent within seconds and retained until deleted
- Operates within the same region that db operates

## DynamoDB Point-in-Time-Recovery (PITR)

- It protects the db against accidental writes or deletes
- Restore to any point in the last 35 days
- Incremental back-ups
- Not enabled by default
- Latest restorable write is 5 minutes in the pastü

## DynamoDB Streams

<figure>
  <img src="/aws-databases/DynamoDB_streams.png" alt="Trulli" style="width:100%; align:center">
</figure>

- It stores the first-in-first-out changes to your data with streams of records.
- Stream is time ordered sequence of item-level changes in a table
- It stored for 24 hours the inserts, updates, and deletes

## DynamoDB Global Tables

- Managed Multi-Master, Multi-Region Application
- Globally distributed applications
- Based on DynamoDB streams
- Multi-Region redundancy for disaster recovery or high availably
- No application rewrites
- Replication latency under one second