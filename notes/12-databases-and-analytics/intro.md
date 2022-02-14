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