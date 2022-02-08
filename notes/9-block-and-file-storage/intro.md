# Block and File Storage

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

