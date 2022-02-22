# Migration and Transfers

Transfering existing resources to AWS

Transfering from one AWS service to another

---

## Migration Tools Overview

Most migration services require either
- VPN
- or direct connect

Some services can migrate over the internet

---

## Application Discovery Service

Collects data about servers in On-premise data center

#### Discover Connector
- Software installed on VM
- Supported by VMware
- Supported by any OS running in VMware
- import a OVA file to VMware vCenter
- sends metrics to Application Discovery Service
  - CPU, RAM, Disk I/O
  - IP Addresses
  - Disk resource allocation
- Deployed ver vCenter

#### Discovery Agent
- If using dedicated servers or Hyper-V
- supports a more limited number of OSs
- install Agent on machine
- sends metrics
- Deployed per server

Data can be sent to S3
- queries with Athena / QuickSite

##### Exercise - Explore Application Discovery Service

- in Tools
- click import template link
- inspect template
  - shows example data populated for VMware instance
  - format for presenting data to migrate virtual machine

---

## DMS - Database Migration Service

Allows for migrating databases to AWS

#### Schema Convertion Tool
- [Common Exam Question]
- Used for heterogeneous migrations
- when source database is a different engine than destination
  - 2 incompatable databases
    - for example, Oracle and Redshift
- Converts the schema of the source database engine to destination engine

Steps
- use Schema Conversion tool to convert schema
- migrate data with **DMS - Database Migration Service**

#### Cloud to Cloud
- EC2 --> RDS
- RDS --> RDS
- RDS --> Aurora

#### On-premises to Cloud

#### Heterogeneous migration
- Oracle --> Aurora
- Orace --> PostgreSQL
- Microsoft SQL --> RDS MyQL
- Will need to use **Schema Conversion Tool**

#### Use Cases
- Dev and Test
  - creating copy for dev / tests

- Consolidation
  - consolidating many source DBs to single target DB

- Continuous Data Replication
  - for distaster recovery
  - dev / test

#### Exercise - Database Migration Service

Explore the console

Creating a replication instance
- will allocate compute services that will perform the migration
- explore creation options

Creating a Database Migration Task
- setting for configuration for actual migration
- explore creation options

Creating Endpoints
- Allows for writing to / reading from databases

---

## Server Migration Service 

Used for migrating
- VMs
  - VMware vSphere
  - Microsoft Hyper-V
  - Azure virtual VM
- to EC2 in  AWS

Steps
- install SMS connector on VM
- Server Migration Service manages
  - automated
  - scheduled migrations
- Server Migration Service creates a Machine Image
- Machine image can be used to create EC2

Server Migration Service can also trigger
- CloudWatch Events

#### SMS - Application Migration

Service that enables full application migrations to AWS

[See slide]

Steps
- Database, App server, web server send information to Server Migration Service
- SMS create a CloudFormation template for application
- template sent to CloudFormation
- generates EC2 instances and resources

#### Exercise

View options in Server Migration Service

---

## DataSync

Allows for migrating data from File systems to AWS

Option 1
- install DataSync agent on file server
- agent forwards data to dataSync
- DataSync sends data to destination
  - data is encrypted in transit to destination
  - S3
  - Amazon FSx for Windows
  - Elastic File System

Option 2
- use AWS SnowCone
  - a device to use on-premise
  - has DataSync agent installed
  - sends data to DataSync

#### Exercise

Explore DataSync options
- can migrate between
  - file systems in AWS
  - on-premise and AWS

Locations
- can create a destination for migrations

---

## AWS Snow Family

Physical devices for loading data onto on-premises
- tamper resistant
- rugged
- data is encrypted

#### Use case
- company needs to
  - migrate large amounts of data to AWS
  - do it quickly
- don't have extra high-bandwidth connectivity
  - or internet link is highly burdened
- remote location

Content Distribution
- company needs to physically send data to clients

Tactical Edge Computing
- collect data at edge and compute
- run ML directly on device

Manufacturing
- data collection and analysis at a factory

#### Snowball
  - a suitcase size device
  - 50TB or 80TB

#### Snowmobile
  - a truck filled with storage
- 100PB (Petabytes)

#### Snowball Edge Compute Optimized
- for machine learning, processing
- for environments with intermittent connectivity
- 100TB

#### Snowball Edge Storage Optimized
- for large scale data transfer
- 100TB

#### Snowcone
- smallest device in the family
- can transfer data offline
- has DataSync agent installed
  - for online data transfer

#### Snowball Client
- software installed on on-premise computer
- compresses, encrypts, and transfers data
  - to device

Uses 256 bit encryption

Steps
- Company installs **Snowball Client** to local computer
- Company loads data to physical devide
- from physical devide
  - data can be uploaded to AWS
    - online (Snowcone)
    - offline - physically sending it to AWS

Optimizing transfer performance
- use latest version of client
- perform multiple transfer operations in parallel
- transfer directories, instead of files

---

## Cheat Sheets

#### Migration Services
- Snowball
- DMS
- DataSync
- [https://digitalcloud.training/aws-migration-services/](https://digitalcloud.training/aws-migration-services/)

---