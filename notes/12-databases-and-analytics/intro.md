# Databases and Analytics

## Types of Databases

#### Relational
- organized in tables and columns
- rigid schema
- rules enforced within the database itself
- scales vertically
- examples
  - RDS, MySQL, PostgreSQL

#### Non-Relational
- varied data storage model
- flexible schema
- rules defined in application code
  - not in database
- scales horizontally
- examples
  - DynamoDB, MongoDB, Redis

---

## Operational vs Analytical Databases

### Operational Databases
- used for Online Transaction Processing (OLTP)
- examples
  - customer records
  - stack availability
  - etc.
- Relational examples
  - RDS, Oracle, IBM DB2, MySQL
- Non-relational examples
  - MongoDB, Cassandra

### Analytical Databases
- source data comes from OLTP database
- stored in Data warehouse separate from OLTP database
- used for complex queries
- relational examples
  - AWS RedShift, Taradata, HP Vertica
- non-relational examples
  - EMR, MapReduce

Many Operational Databases (OLTP) can feed into one Analytical Database

---

## Hosting a Database on AWS

#### On EC2
- gives full control over instance and database
- can host any database that is supported by OS

#### RDS
- can host a relational database
- for example
  - Oracle, PostgreSQL, Microsoft SQL, Maria DB, MySQL

#### DyanamoDB
  - noSQL database
  - high I/O needs
  - dynamic scaling

#### Amazon Redshift
  - data warehousing
  - large volumes of aggregated data

#### ElasticCache
  - Fast temporary storage
  - small amounts of data

#### EMR
  - Non-relational
  - analytic workloads
  - uses Hadoop framework

---

## RDS (Amazon Relational Database Service)

- RDS runs on an EC2 instance
- choose an instance type to determine performance characteristcis
- can support these DB engines
  - Amazon Aurora
  - MySQL
  - MariaDB
  - Oracle
  - Microsoft SQL
  - PostgreSQL
- Commonly used for OLTP use cases
  - online store
  - banking systems
- can be encrypted
  - uses AWS Key Management Service (KMS)
  - must be set during creation

---

### Can scale vertically
- change instance type

### Scaling Horizontally - for some characteristic

#### RDS standby
- can have a backup RDS instance that synchronizes with the RDS Master
- used for destaster recovery
- synchronous replication

#### RDS Read-Replica
- Another RDS instance used for read-only
- takes some processing off of the RDS Master
- All writes go to the master
- Read Replica uses asynchronous replication for replicating RDS Master

[Common Exam Questions]
- **Encryption state of the read replica must be the same as the RDS master**
- If Read-Replica in same region
  - same KMS encryption key used
- if in different region
  - different key

- cannot restore unencrypted backup or snapshot to encrypted DB instance

To convert unencrypted RDS to encrypted
- create snapshot of unencrypted EBS volume containing the RDS
- create encrypted snapshot from the unencrypted snapshot
- Create encrypted EBS volume from encrypted snapshot
- [See Slide]

---

## RDS Backup and Desaster Recovery

### Automatic Backups
- backups stored in S3 snapshot
- retained for 0 to 35 days
- snapshot can be used to restore DB
  - within the retention period

### Manual Backups
- for single-AZ DB instances
  - there is brief suspension of I/O during backup

- for multi-AZ
  - backup snapshot is taken from the standby instance

- for multi-AZ Microsoft SQL Server
  - I/O is suspended on the primary

- Snapshots from manual backups do not expire

---

## Maintenance Window

Can specify a maintenace window
- during which the database is taken offline
- example tasks
  - OS and DB patching
- maintanence window is configured by default
- can be customized

---

## Exercise - Create Amazon RDS Database

- in RDS console
- databases tab on left side
  - we don't want to create an Aurora db
- create a database
- standard create
- select MySQL
- select Production template
  - will switch to free tier later
- enter password
- confirm password
- instance type - leave for standard for now
- storage
  - look at storage settings
  - these will change when switch to free tier
- Explore default settings
  - availability
  - conneectivity
  - can connect to DB on port 3306 (for MySQL)
  - authentication - leave on password  

- Additional Config
  - explore default settings

- **notice cost for production template is expensive**

- **Switch to free tier**
- instance - t2.micro
- no multi-AZ
- no option for encryption
- create

- Wait for DB to be available
- explore Database details
  - connectivity
    - endpoint
    - port
  - monitoring

- Snapshots
  - note we have a system generated snapshot
  - create a manual snapshot
    - a few minutes

- explore available actions

Clean up
- leave database for next exercise

---

## RDS - Read Replicas and Multi-AZ

Select database
- actions > create a read replica
- explore options
  - can modify
    - type
    - storage
    - region
    - AZ
    - public
- create read replica

While replica is being created
- modify master RDS
- update to a multi-AZ deployment
- apply immediately

When read-replica created
- note different endpoint
- reads sent to the read-replica
- writes sent to the master DB

In primary DB
- note read-replica listed as a replica
- note secondary zone
  - gives zone for standby instance
- actions > reboot > with failover
  - switch primary over to the standby

Clean up
- delete databases or save for later lessons

---

## RDS Security

If connecting to RDS from an App on EC2 instance
- RDS security group allow traffic from App SG on port 3306 (MySQL)

- Can have SSL/TLS encryption in-transit from RDS to app

- Data can also be encrypted at rest in RDS
  - snapshots, backups, read-replicas will also be encrypted

  - can only enable encrypted at creation

  - cannot disable encryption

  - uses AES 256 encryption
    - minimal performance impact

  - uses AWS KMS
    - managing encryption keys

---

## Exercise - Create Encrypted Copy of ERDS Database

Create encrypted RDS database from unencrypted

Steps
- Create a snapshot of the already created unencrypted RDS database
  - takes a few munites

- select snapshot
- copy
- same region
- set encryption
- use standard KMS key

After created
- select encrypted snapshot
- actions > restore
  - keep same region
  - t2.micro

After created encrypted DB
- note encrypted status
- note different endpoint

Clean up
- delete all databases
- delete snapshots

---

## Amazon Aurora

- Database in the RDS family
- AWS's fastest growing product
- 5 times faster than MySQL
- 3 times faster than PostgreSQL
- features
  - distributed
  - fault-tolerant
  - self-healing
- auto-scales up to 128TB
  - very large

### Aurora - High Performance and Scalability
- high performance
- self healing storage
- scales to 128 TB
- point-in-time recovery
- continuous backups to S3

#### Compatibility
- compatible with MySQL and PostgreSQL

#### Aurora replicas
- read scaling with auto-scaling
- scales within region
- up to 15 replicas

#### MySQL Read Replicas
- can have
  - a cross-region cluster
  - read scaling across a region
  - failover targets across a region

#### Global Database
- a cross-region cluster with **read scaling**
- auto scales the read replicas across a region
- low latency reads

#### Multi-Master
- auto scales writes within a region
- very new feature

#### Serverless
- on-demand database
- not running all the time
- is instantiated when traffic starts
- only accessible through a VPC or direct connect

[See Slides]
- Comparing Aurora Replicas to MySQL replicas

---

## Aurora Deployment Options

[Rewatch this section]
[Take notes on each type]

#### Aurora Serverless use cases
- infrequently used applications
- new applications
- variable workloads
- dev / test databases

---

## RDS Anti-Patterns

When would we not use RDS
- when you need a database other than supported engines
- if you need root access to the OS
  - when need to install tools, patching

- Use S3 instead
  - need to store large binary objects (BLOBs)

- Use DynamoDB instead
  - need automated scaling for writes
  - name / value data structure
  - data structure is not consistant, unpredictabled

- Use EC2 instance
  - need to support database types RDS does not support
    - IBM DB2, SAP HANA
  - need complete control over the database and OS
  - will need to manage everything
    - backups
    - redundancy
    - scaling
    - patching

---

## ElasticCache

In memory database

Used for caching data that comes from other databases

#### ElastiCache
- fully managed implementation of Redis or Memcached
- key / value store
- high performance
- low latency
- used in front of DBs such as RDS nd DynamoDB
- runs on an EC2 instance
  - must choose an instance family / type

#### How it works
- clients write data to RDS
- RDS loads most frequently accessed data to ElastiCache
- client reads data from ElastiCache

#### Redis vs Memcached Implementation
[Common exam question]

Memecached
- data not persisted
- support simple data types
- not encrypted
- no automatic replication, backup
- not highly available
- no snapshots
- **multithreaded**

Redis
- data is persisted
- supports complex data types
- data is encrypted
- automatic replication, backup
- highly available
- **not multithreaded**
- yes snapshots

---

## ElastiCache Use Cases

- data that is static and frequently accessed
- application is tolerant of stale data
  - ElastiCache may have stale data
- if data is slow / expensive to retrieve compared to cache retrieval
- easy scalability
- for storing session state

[See Slides]
- [Excellent use case examples described in slides]

---

## Scaling ElistiCache

#### Useful terms
- partition / shard
  - a subsection of a data collection / table
  - used to break collection / table into many smaller pieces

- Cluster
  - a collection of partitions / shards
  - could be spread across AZs

#### Memcached
- stores data in a cluster made up of partitions / nodes
- can be spread across AZs
- can add nodes to cluster

- to scale vertically
  - change node type
  - create a new cluster manually
- you have to manage creating backup of nodes / partitions

- [See slide - diagram]

#### Redis
cluster mode disabled
- a cluster has a single shard / partition
- shard can have a primary and replica databases
- shard can span across AZs

- to add replica or change node type
  - redis automatically creates new cluster
  - migrates data

- [See slide - diagram]

Cluster mode enabled
- a cluster can have multiple shards / partitions
- each shard can have multiple replicas
- each shard group can span multiple AZs

- online resharding to add or remove shards
- [come back to this]

- [See slide - diagram]

---

## Exercise - Create ElastiCache Cluster

In ElastiCache console
- get started
- explore options available for Memcached

- Choose redis
- selec t2.micro
- create a subnet group with some subnets
- Explore defaults
- will take a few minutes to create

Click on cluster name to get information nodes

actions > modify
- can change node type

Within nodes
- choose primary > actions
- can failover primary
- can promote a replica
- can add node - add a read replica

Select Cluster
- can - actions > create backup

Explore events tab on left

Clean up
- delete cluster

---

## DynamoDB

- Serverless noSQL database
- Fully managed
- key / value store or document store
- push button scaling
  - no downtime when scaling, unlike RDS
  - scales horizontally automatically

#### Read / Write Capacity Units
- With DynamoDB
  - can define read and write capacity units
    - how many reads and writes we want available

#### TTL
- can define when an item in table expires
- can be set on a per item basis
- useful for session state
- **does not contribute to read / write capacity units**
- help reduce storage and manage size

Partition Key
- what we are organizing the table by

Sort Key
- used for sorting

Partition key and sort key together
- form the **primary key**

Attributes
- other columns of the table
- each record does not need a value for each attribute
- **helpful for when structure is unpredictable**

DynamoDB Accelerator
- fully managed in-memory cache for DynamoDB
- microsecond latency

Transaction Options
- strongly consistent reads
- or eventually constistent reads
- supports ACID transactions

Backup
- point-in-time recovery
- down to seconds within last 35 days
- automatic backup

Global Tables
- fully managed multi-region, multi-master

---

## Exercise - Create DynamoDB Table

in dynamoDB console
- create table
- name - mystore
  - matches seed file we use later
- details in provided file in DynamoDB folder
- customize settings
  - explore price calculator
  - explore read / write settings
    - choose on-demand

After created
- explore tabs
  - backups
    - can enable point-in-time recovery
  - monitor
    - Can set CloudWatch alarms

  - exports and streams
    - can export data to S3
    - can enable Kensis or Dynamo DB streams

  - additional settings
    - can configure
      - TTL
      - encryption

Run import data command from provided file

See items in mystore table

perform scan
- command in provided file
- can view data in terminal

Clean Up
- Leave table running for next activity

---

## DynamoDB Streams

- Captures a time-ordered sequence of modifications to a DynamoDB table
- stores this information in a log for up to 24 hours

Steps
- Application writes / reads to a DynamoDB table
- DynamoDB table sends record to DynamoDB streams
- Lambda function picks up item from stream
- Lambda writes to CloudWatch logs

Can configure what is written to stream
- KEYS_ONLY
  - only keys that were modified
- NEW_IMAGE
  - the new image, before modification
- OLD_IMAGE
  - the old image, before modification
- NEW_AND_OLD_IMAGE
  - both new and old image that was modified

---

## DynamoDB Accelerator (DAX)

- Used to improve performance of DynamoDB tables

- fully managed, in-memory caache for DynamoDB

- Can be a
  - read-through cache
  - write-through cache

- improve performance from milliseconds to microseconds

- integrates fully with DynamoDB
  - no need to modify application code

Setup
- DAX lives in the same VPC as EC2 instance
- EC2 instance requests data from DAX first
  - if not found looks in DynamoDB
- DAX given IAM role to access DynamoDB
- EC2 instance given IAM role to access DAX and DynamoDB

DAX vs ElastiCach
- DAX is optimized for DynamoDB
- ElistiCache
  - supports more databases
  - more management overhead
  - have to modify application code to point to cache

---

## DynamoDb Global Table

- Can have a DynamoDB replicated in many regions
- Each copy allows for reads and writes
- Replications happen asynchronously
  - using DynamoDB streams
- Can provide application logic to failover to replica in other region
- Can be solution for desaster recovery

Setup
- Can have many app servers running in different regions
- each app server reads / writes to DynamoDB copy in its same region

---

## Exercise - Create DynamoDB Global Table

Select mystore table
- global tables > create replica
- select another region
- notice we have all the same items in replica
- create an item
  - add clientid, created, and another attribute
- item should be replicated back to primary DynamoDB instance

Clean Up
  - delete replica mystore table
  - delete primary mystore table

---

## RedShift

Fully managed Data warehouse solution
- used for analyzing data
- used with SQL
- used with Business Intelligence (BI) tools
- uses EC2 instances
  - specify instance and family / type
- always keeps 3 copies of data
- allows for incremental backups

## OLAP
Online Analytics Processing
- often sources data from OLTP databases
- seprate from customer facing DB
- used for
  - long transactions
  - complex queries
- examples
  - Redshift
  - EMR

You need to run reporting and analytics on customer facing DB
- [Common exam questions]
- Option 1.
  - can create a read replica
  - use read replica for reporting
  - read-replica is synced with primary

- Option 2.
  - if you have many OLTP databases that you want to aggregate data from
  - aggregate data into Redshift for analysis

## Data sources for Redshift
- DynamoDB
- RDS
- EMR
- S3
- AWS Data Pipeline
- AWS Glue
- EC2
- on-premise server

## Use Cases of RedShift
- [Common exam questions]
- perform complex queries on large collections
- for structured and semi-structured data
- fast performance
- Can use **Spectrum** for direct access of S3 objects in a **data lake**
- for a managed data warehouse with
  - automated provisioning
  - **durability** - continuous backups to S3
  - Exabyte scale query capabilities

---

## Elastic Map Reduce (EMR)

#### Hadoop
- used for perform processing on large data set

#### EMR
- managed implemation of Apache Hadoop or Apache Spark
- used for
  - processing data
    - analytics and business intelligence
  - performing ETL

#### ETL - Extract, Transform, Load
- convert data in one database to another form
  - to be loaded into another database

[See slide]

---

## Kinesis Core Knowledge
- Used for taking in streaming data
- can be used to processing streaming data from IoT devices

#### Amazon Kinesis Data Streams
- [Common Exam Question]
  - processes data realtime
- Used for taking in streaming data
- Data streams can then be processed by Lambda function and then written to database

#### Kinesis Data Firehose
- [Common Exam Question]
  - processes data near realtime
- loads data directly to destination
- can load data to S3 or Redshift for analysis

#### Steps
- producers send data to Kinesis Stream
  - producer can be web / mobile / IoT devices
- Kinesis stores data in a shard
- data send to service (such as EC2) for processing
- after processing
  - data stored in database
- analytics run on data

#### KCL - Kinesis Client Library
- software that runs on the EC2 instances
- to process data from a Kinesis data stream

[See slide]
- KCL enumerates Kinesis shards
  - instantiates a record process for each shard
- KCL worker on EC2
  - manages many record processor
- Each shard corresponds to 1 record processor

[Common exam questions]
- Kinesis Data streams can be processed in order
- order only maintained within a shard
  - not across shards

---

## Kinesis Data Firehose

Firehose captures, transforms and loads streaming data

- Producers send data to Firehose
- Firehose has no sharding of data
  - elastic scalability
- Data not stored on firehose
  - sent directly to another AWS service
- Can be sent to Lambda for processing
- **Near real-time delivery**
  - ~ 60 second latency

#### Supported destinations
- RedShift
- Elasticsearch
- S3
- Splunk
- Datadog
- MongoDB
- New Relic
- HTTP Endpoint

---

## Kinesis Data Analytics

- Allows for running SQL queries on real-time streaming data
- Provides analytics for data coming form Kinesis

[See slide]

---

## Athena and AWS Glue

#### Athena
Serverless service to run SQL queries against data

Can run queries against
- AWS branded data sources
- 3rd party data sources

Athena queries data in S3 natively

Can query other data sources
- by using a Lambda function

Can uses AWS Glue
- a managed Data Catalog
- to store information and schemas
  - about databases and tables

---

## Optimizing Athena for Performance

- partition your data
- use data compression
  - Apache Parquet
  - Apache ORC
- optimize file size
- optimize ORDER BY and GROUP BY
- only include columns that you need

[See Slides for more]

---

## AWS Glue
Fully managed ETL service
- for preparing data for analytics
- run ETL jobs on managed, Apache Spark environment
- discovers data and stores meta-data in **Data Catalog**
  - meta-data - table defintion and schema
- works with
  - data lakes - S3
  - data warehouse - RedShift
  - data stores - RDS, EC2

#### Crawler
- populates the AWS Glue Data Catalog with tables
- ETL jobs defined in AWS Glue use tables as sources and targets

---

## Exercise - Query S3 ALB Access Logs with Athena

#### What we will Build
- An application load balancer pointing to a single EC2 instance
- Enable logging with ALB
- query logs with Athena

#### Prerequisites
- Set up an ALB
- point the ALB to a single EC2 instance

#### Steps
- Select ALB
- actions > edit attributes > Enable Access Logs
- give name for access log location in S3
- create location for me
- see bucket in S3
  - see logs folder

Generate traffic
- visit ALB DNS name
- try visiting from different IPs with a VPN
- see logs in S3

In Athena
- get started
- set up query result location in S3
  - s3://<bucket-name>/queryresults/

Create queryresults folder in S3 bucket

Create Database
- in provided materials, in Athena directory
  - .sql file
- update create command
  - S3 bucket name
  - account number
  - region
- Copy create command
- paste code as a new query
- run query

ab_logs table in left
- note columns of information in created table

Run query for first 100 logs
- note all connection attempts to ALB

Run next qeuries

Clean up
- Delete table in Athena
- delete ALB
- delete EC2 instances

---