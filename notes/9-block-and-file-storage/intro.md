# Block and File Storage

## Cheat Sheets

#### EBS Cheat Sheets
[https://digitalcloud.training/amazon-ebs/](https://digitalcloud.training/amazon-ebs/)

#### EFS Cheat Sheets
[https://digitalcloud.training/amazon-efs/](https://digitalcloud.training/amazon-efs/)

---

## Block vs File vs Object Storage

---

### Block Based Storage
- can think of as similar to
  - having a physical hard drive attached to your computer tower
- OS sees blocks and sections of the hard drive
- low level way of communicating with the disk
- OS gives you ability to partition disk to different Volumes

### File based storage systems
- NAS - Network Attached Storage Server
- Computer connects to over a network
  - OS sees a filesystem that is mapped to a local drive level or file path

### Object Based Storage
- User connects to storage system via the web
- can perform CRUD over HTTP
- objects are stored without heirarchy

---

## Higher Level Overview

### Block Storage
- EBS - Elastic Block Store
- OS sees volumes that can be partitioned and formatted
- OS reads/writes to block storage at block level
  - very low level
- Disks can be internal to computer or networked attached

### File Storage
- EFS - Elastic File System
- file system shared by many computers
- file system is mounted to OS using a **"network share"**
- OS can partition or manage file storage
- only read/write to it

### Object Storage
- S3 - Simple Storage Service
- massively scalable
- low cost
- no hierarchy
- Uses REST HTTP API

---

## EBS Deployment

EBS multi-attach allows for connecting multiple EC2 instances to a EBS volume
  - New feature - may not be on exam

EBS volumes are replicated in AZ

EBS volume must be in same AZ as EC2 instance
- to move volume across AZs
  - create snapshot

---

## EBS multi-attach
- only available for Nitro system-based EC2 instances
- EBS volume must be IOPS io1 volume
- up to 16 EC2 instances attached

## EBS SSD-Backed Volumes

[See slide - chart]
[Common exam questions]

- gp2 is the default volume attached when creating EC2 instance
- Provisioned IOPS SSD
  - higher performance
  - higher IOPS
- io2 Block Express
  - even higher IOPS
  - higher throughput
  - more storage

---

## HDD-Backed Volumes
- **Cannot use as a boot volume**
- Spinning disk
- cheaper
- much lower IOPS

#### Cold HDD
- Cheapest option
- best for infrequently accessed data

#### Throughput Optimized HDD
- Supports high throughput usecases
- Can save cost
- Cheaper than Provisioned IOPS SSD

---

## EBS - Additional Notes

- EBS volumes do not need to be attached to an EC2 instance
- can attach multiple EBS volumes to an EC2 instance
- must be in same AZ as the attached EC2 instance
- by default - when EC2 instance deleted
  - Root (boot) EBS volume deleted
  - non-boot volumes are not deleted

---

## EBS Copying, Sharing, and Encrypting

#### Snapshot
- a frozen in time copy of an EBS volume
- snapshots are stored in S3 buckets
  - they are stored in a region
- can be used to copy EBS volume to another AZ
- can be used to restore an EBS volume

[Common Exam Question]
- How to free up space
  - Delete old snapshots
  - keep most recent snapshot

#### AMI
Amazon Machine Image
- Template for configuring an EBS volume
- A snapshot can be used to create an AMI
- AMI can be used to create a volume in another AZ

---

## Copying and Sharing AMIs and Snapshots

[See Slide]
[Common Exam Questions]
[Practice these scenarios in console to memorize]

---

## EBS Snapshots and DLM

### DLM - Data Lifecycle Manager
- automates creation, retention, deletion of
  - EBS snapshots
  - EBS-backed AMIs

Features
- protects data by enforcing regular backups
- creates AMIs that can be refreshed at regular intervals
- retain backups for auditing and internal compliance
- automatically deletes outdated backups, saving space
- can create disaster recovery backup policies
  - back up data to isolated accounts

---

## Instance Store
- physically resides on same host machine as EC2 instance
- very high performance / low latency
- Instance store is **ephemeral**
  - when the instance is powered down all data in instance store is lost
- ideal for
  - temporary storage of information that changes frequently
  - buffers, caches, scratch data
- Instance store volume root devices are created from AMI templates stored in S3
- Cannot be detached/reattached

---

## Exercise - Creating and Attaching EBS Volumes

Launch EC2 instance
- AMI - Windows Server 2019 Base
- Security group
  - SSH / RDP
  - HTTP / HTTPS

Go to EBS Volumes console
- notice volume currently being launched

Explore EBS options
- in create volume window
- notice options for configuring
- options change for volume types
- different
  - size to IOPS ratios
  - size limits
  - IOPS limits
- Can create volume from snapshot

Create EBS volume
- gp2
- same AZ as instance

RDP to EC2 instance
- search 'disk management'
- select 'Create and format hard disk partitions'
- note we have a C drive (root volume)

Attach new EBS volume to instance in EBS console

Bring new disk online
- in Windows desktop
- in disk management window
- right click new disk > online
- right click new disk > Initialize
- right click disk > "New Simple Volume"
  - click through defaults
- create text file in new D drive
  - right click on drive > open > right click > new > text document
- create new text file in C drive
  - will exist in the AMI

Clean Up
- leave instance running for next lesson

---

## AMIs - Amazon Machine Images

- Provides the information required to launch an instance
- includes
  - EBS snapshots
  - for instance-store-backed AMIs
    - template for root volume
  - Launch permissions
    - who can use AMI to launch instances
  - Block device mapping - specifies volumes to attach

#### 3 Categories of AMIs
- community AMIs
  - free to use
  - select OS
- AWS Marketplace AMIs
  - pay to use
  - packaged with additional software
- My AMIs
  - AMIs that you create

[See Slide]

To create AMI
- Manually
  - create snapshot from volume
  - create AMI from snapshot
- Automated
  - Actions > image and templates > create image
  - will shut down OS first

---

## Exercise - Using Amazon Machine Images (AMIs)

Create AMI from EC2 Instance
- Actions > image and templates > create image
- Will include root volume and secondary volume

In AMIs
- AMI is being created
  - OS shutting down
  - snapshot being created
  - image created from snapshot

Explore image tabs for AMI
- permissions
  - can make public
  - or give permissions to an account

Once complete create new instance from custom create AMI
- in different AZ

RDP connect to new instance
- notice the DRP password is the same as for the original instance


Clean Up
- delete instances

---

## EC2 Image Builder

A tool for building AMIs
- can set to automate AMI building process

## Exercise

Create Role
- IAM console
- Create a role
  - EC2
  - add permissions
    - `AmazonSSMManagedInstanceCore`
    - `EC2InstanceProfileForImageBuilder`
  - Role name - EC2ImageBuilderRole
  
Create Image Pipeline
- AMI console
- EC2 Image builder
- Create Image Pipeline
  - name - myImagePipeline
  - build schedule - Manual
  - create new recipe
    - Output type - Amazon Machine Image
    - name - MyAMIRecipe
    - version - 1.0.0
    - managed Image - Amazon Linux 2 AMI
    - add components
      - amazon-cloudwatch-agent-linux
      - update-linux
    - add test component
      - simple-boot-test-linux
        - checks if image boots correctly
  - Define infrastructure config
    - Create a new infrastructure config
    - name - myVPCInstanceConfig
    - role
      - role created earlier
      - EC2ImageBuilderRole
    - instance - t2.micro
  - Create Pipeline

After created
- run pipeline
- could take 15 - 30 minutes

---

## Exercise - Migrate EBS volume between AZs

Prerequisites
- have a volume created
- can create an EC2 instance which will create a volume

In EBS console
- select volume
- actions > create snapshot

Create volume from Snapshot
- In Snapshots console
- see new snapshot
- select snapshot > actions > create volume
  - select new AZ
  - choose to Encrypt
  - use aws/ebs master key

in EBS console
- see new volume

---

## Exercise - Working with EBS Snapshots and 

DLM - Data Lifecycle Manager

Using DLM to automate backing up EC2 instances with snapshots

Prerequisite
- Have at least one volume created
- Add tag to volume
  - Key - DLM
  - Value - Weekly

Create DLM Policy
- in Lifecycle Manager console
- create EBS Snapshot Policy
- Target resource type - Volumes
- target resource tags
  - key - DLM
  - value - weekly
- policy description - WeeklyEBS
- default IAM role
- Create Schedule
  - set a weekly backup
  - retention type - age
  - expires - 21
  - browse other options
  - create

Clean up
- delete lifecycle policy
- deregister AMIs
- delete snapshots
- delete AMI
- delete volumes

---

## Using RAID with EBS

### RAID
Reduntant Array of Independent Disks
- agragate many disks on a machine together

Benefit
- better performance
- redundancy

RAID not provided by AWS
- must be configured through OS

RAID 0 and RAID 1 are options for EBS

RAID 5 and RAID 6 not recommended by AWS

### RAID 0
- used for striping data across multiple volumes
- uses 2 or more disks / drives / EBS volumes
- Data is writen across volumes
  - Even for a single write

Pros
- Higher performance of reading and writing data

Cons
- if one disk fails
  - entire array fails

### RAID 1
- used for mirroring data across multiple volumes / disks / drives
- Exact copy of data is written to 2 or more volumes simultaniously

Pros
- redundancy / fault talerance
  - can handle if one drive goes down very easily

Cons
- using twice as much space with 2 volumes

[Common Exam Questions]
- In this case
  - RAID is configured through OS not through AWS
- understand RAID 0 and RAID 1

---

## Amazon Elastic File System

EFS File System allows multiple computers to connect
- connect via the NFS protocol
- Can connect thousands of instances to EFS
- can connect
  - EC2 instances from the same VPC
  - EC2 instances from another VPC using VPC peering
  - EC2 instances from another AWS Account
  - on-premise data center using VPN or Direct Connect

[Common Exam Question]
- Can only choose encryption setting when creating the EFS
- Cannot encrypt an unencrypted file system after it's created
- Solution
  - Create new EFS with encryption
  - migrate from old EFS to new

---

## Exercise - Create EFS Filesystem

Create 2 EC2 instances
- given SSH, HTTP access

Security group for EFS
- Create a new security group
- name EFSFileSystem
- Add inbound rule
  - NFS
  - from web access security group

Create EFS
- In EFS console
- create file system
- customize
- network access
  - set NFS security group
  - set for AZs where instances live

- File System policy
  - [common exam questions]
  - browse various policies
    - can prevent root access
    - enforce read-only access
    - prevent anonymous access
    - Enforce in-transit encryption for all clients

Explore File System Window

For each instance
- SSH into instance
- create directory /efs in home directory
- install EFS utils
  - `sudo yum install -y amazon-efs-utils`
- Mount File system
  - `sudo mount -t efs <file-system-id> efs/`

Test
- cd to efs dir
- create file
- in other EC2 instance SSH shell
- view file created

Clean Up
- Delete EC2 instances
- Delete EFS

---

## Amazon FSx
Provides fully managed 3rd party file systems

2 options
- Amazon FSx for Windows File Server
- Amazon FSx for Lustre
  - compute intensive workloads

### Amazon FSx for Windows File Server
- fully managed Windows file system
- supports
  - SMB protocol
  - NTFS
  - Microsoft Active Directory

- **Does not integrate with S3 like Lustre** 

- supports ACLs

- high availability
  - can replicate data within AZ

- multi-AZ
  - can have active and standby in separate AZs

- Can connect from on-premise data center with VPN or Direct Connect

### Lustre
- high performance file system
- for fast processing workloads
  - machine learning
  - High performance computing
  - video processing
  - financial modeling
  - electronic design automation
- with a POSIX compliant file system interface

Can connect on-premise data center to Lustre
  - via a VPN or Direct Connect
  - used for
    - cloud bursting 
    - data migration

[Common Exam Question]
- Lustre works with S3
  - can access S3 objects as files
  - writing to Lustre will write back to S3

---

## AWS Storage Gateway

Allows for connecting on-premise storage to AWS

---

### File Gateway

An AWS storage gateway
- installed in on-premise data center
- Provides a file system interface
- runs on Hyper-V, VMWare, or EC2
- **local cache provides low latency access to recently used data**

S3 buckets
- stored in AWS cloud

Client can use a file system interface
- while still using low cost object based storage with S3

Provides NFS or SMB based access to data in S3

---

## Volume Gateway

Uses iSCSI
- Block based storage protocol

#### Cached Volume Mode
cache of most recently used data stored on-premise

#### Stored Volume Mode
- Entire data set stored on-premise
- point-in-time snapshots are created and stored in S3 in AWS

---

## Tape Gateway

Used for integrating with popular backup software to be used in on-premise server

- backup software such as
  - NetBackup
  - Backup Exec
  - Veeam
  - used in on-premise server

- After data expires from on-premise server
  - can be sent to S3

---

## AWS Backup

Service for backing up multiple AWS resources
- volumes
- databases
- file systems

Can create a backup plan

Can create backups across multiple AWS accounts

## Exercise

Create a Backup Plan
- in Backup console
- create a backup plan
- start with template
- Daily 35 Day Retention
- Explore wizard options
- Create a plan

After Created
- assign resources

---

[Common Exam Questions]
- specifications for
  - gp2
  - io1

Others are newer
  - less likely to see on exam

---