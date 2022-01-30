# EC2 - Elastic Compute Cloud

## EC2 Cheat Sheet
[https://digitalcloud.training/certification-training/aws-solutions-architect-associate/compute/amazon-ec2/](https://digitalcloud.training/certification-training/aws-solutions-architect-associate/compute/amazon-ec2/)

## VPC Cheat Sheet
[https://digitalcloud.training/certification-training/aws-solutions-architect-associate/networking-and-content-delivery/amazon-vpc/](https://digitalcloud.training/certification-training/aws-solutions-architect-associate/networking-and-content-delivery/amazon-vpc/)

---

A virtual server in the cloud on AWS

- AWS has a pool of physical hardware
  - AWS manages
- EC2 instances run on top
  - You can manage

## instance type
- the hardware configuration
  - combination of
    - CPU
    - networking
    - memory
    - storage

## AMI
- image that contains
  - OS
  - configurations
- can create custom or use existing

EBS Snapshot
- AMI created from EBS snapshot
- point in time backup of instance

ec2 instance --> snapshot --> AMI --> ec2 instance

---

## Benefits of EC2

- easily launch many instances in minutes
- complete control of instance - full root access
- high level of avaibility
- secure - integrated with VPC and security features
- inexpensive compared to owning your own infrastructure

- EC2 is a regional service
- must select region we want to run services

region --< vpc --< az --< subnet --< EC2 instance

[See slide]

---

## Exercise - Launch EC2 Instance

- Create Linux EC2 instance
  - give ssh
- Create Windows EC2 instance
  - give rdp

---

## Exercise - Connect to Linux Instance with SSH

- Connect to Linux EC2 instance with EC2 instance connect
- Connect with SSH

add permissions private key
- user can read and write
- `chmod 600 path/to/private/key`

---

## User Data and Metadata

User data
- shell script a user passes to the instance
- instance will run script upon booting up
- limited to 16KB

Metadata
- data about the instance
- available on instance
- Access EC2 meta data
  - `curl http://169.254.169.254/latest/meta-data/`
  - `curl http://169.254.169.254/latest/meta-data/<category>`

EC2 Metadata CLI

download CLI
`wget https://s3.amazonaws.com/ec2metadata/ec2-metadata`

Add Executable permissions
`chmod u+x ec2-metadata`

Can now ec2-metadata to retrieve meta data about ec2 instance
`ec2-metadata -help`

---

## Exercise - Launch EC2 instance with User data and meta data

- Launch an EC2 instance
  - pass in user data that gets instances ID from meta data
  - prints instance ID to index.html file

---

## Exercise - Access Keys

- Launch EC2 instance
  - Web Access
  - ssh

- Create S3 bucket
- upload small images to s3 bucket

- SSH in instance
- `aws s3 ls`
- not allowed because we don't have credentials
- create access keys
  - IAM console > Users > security credentials > create access key
- in instance `aws configure`
  - paste in access key and secret access key
- now run `aws s3 ls`
  - see bucket
- `aws s3 ls s3://<bucket-name>`

- see credentials
  - `cat .aws/credentials`
  - not secret

---

## Exercise - Connect EC2 to S3 with IAM Roles Instead

- SSH'ed into EC2 instance
- delete credentials file

In IAM console
- create role
  - add policy - `AmazonS3FullAccess`
  - give role a name   
- After creation can view
  - trust relationship - trusted entities that can assume the role
  - policy

In EC2 console
  - instance security > modify IAM role
  - Attach role to instance

- `aws s3 ls`
- we can now see buckets

---

## EC2 Status Checks and Montiring

system status checks
- hardware is working operationally
- AWS needs to repair

instance status checks
- checking if
  - os is configured correctly
  - connecting on network
- You need to repair


CloudWatch

Basic Monitoring
- Every 5 minutes

detailed monitoring (every 1 minute)
  - must pay for

---

## Placement Groups

Cluster
- places instances physically close together in a data center
- possibly in same server rack
- allows for ultra low-latency

Partitioning
- spread instances across partitions
- they do not share the same underlying hardware
- used by large distributed workloads
- can be spread across multiple AZs or same AZs

Spread
- Each instance is in a separate rack
- highly available / fault talerant

Can Add EC2 instance to a placement group on its creation

---

## Network Interfaces

Network Interface
  - When an EC2 instance is added to subnet
    - a network interface is added to the subnet
  - if in a public subnet
    - network interface has public and private IP address
  - if in a private subnet
    - network interface has private IP only

- Instance can be attached to multiple subnets

- Cannot be attached to a subnet in a different AZ

## Network Instance Types

#### Elastic Network Interface
- Basic adapter
- Can be used with all instance types

#### Elastic Network Adapter
- for high performance
- higher bandwidth
- must choose supported instance type

#### Elastic Fabric Adapter
- Very high performance
- tightly coupled applications
- used with all instance types

---

## Exercise - ENI

- Launch EC2 instance

- In network interface console
  - explore ENI details for existing ENI
  - create new network interface
    - place in same subnet
  - Attach to instance

- In EC2 console you can see 2 network interfaces attached

## Public, Private, Elastic IP Addresses

#### Public IP Address
- dynamic IP
  - lost when instance is stoped
- used only public subnets
- used to ID instance on the internet
- free
- mapped to a private IP in the Internet Gateway

#### Private IP Address
- Retained when instance is stopped
- Used to route traffic within a VPC
- is mapped to a public IP with Internet Gateway

#### Elastic IP
- A public IP address
- static IP
  - retained when instance is stopped
- Can be moved across instances and network adapters

---

## NAT for Public Addresses

When traffic enters the internet gateway
- internet gateway looks at public destination IP
- finds associated private IP by looking at instance's network interface
- routes traffic to private IP

---

## Exercise - Working with EC2 IP Addresses

Public IPs are dynamic
- Create EC2 instance
- note public ip
- stop instance
- public ip changes
- reboot instance
- public ip stays the same

Add Elastic IP
- create network interface
- associate network interface with the EC2 instance
- Alocate an elastic IP
- Associate elastic IP to network interface
- Notice instance has Elastic IP address

Public IP behavior
- stop the instance
- Notice we lose the dynamic public IP
- start the instance
- Notice the dynamic public IP does not return
  - because we have the Elastic IP in place

Reassign Elastic IP
- Terminate instance
- Create instance in different AZ
- disassociate Elastic IP
- associate with new instance in different AZ

Clean Up
- Delete
  - interface
  - network interface
  - dissassociate Elastic IP
  - release elastic IP

---

## Private Subnets

#### Private Subnets
- When creating a private subnet we don't configure the option to assign public IP addresses
- Private subnets don't have public IPs
- cannot send / receive traffic from internet

#### Public Route Table
A public route table has entries for
- CIDR block of VPC
  - 127.31.0.0./20 - Local
- catch all route for internet gateway
  - 0.0.0.0/0 - igw-id
- Is associated with the public subnets

#### Private Route Table
A private subnet route table has entries for
- CIDR block of VPC
  - 127.31.0.0./20 - Local
- No route to the internet gateway
- Is associated with the private subnets

---

## Bastion Host

A middle layer that allows us to connect from the internet to a private instance

[see slide]

For example
- SSH into the public instance
- from within public instance SSH into private instance

Agent Forwarding<br>
[https://digitalcloud.training/ssh-into-ec2-in-private-subnet/](https://digitalcloud.training/ssh-into-ec2-in-private-subnet/)

---

## Exercise - Private Subnets and Bastion Hosts

Create private subnet
- name - private-default-1a
- in AZ 1a
- give it the proper CIDR block

Create a route table
- name - private-rt1
- associate private subnet to private route table

Launch public EC2 instance
- give ssh

Launch private EC2 instance
- add to private subnet
- ssh

Store default keypair
`ssh-add -K path/to/private/key.pem`

Agent Forwarding
- ssh into public instance while passing private key to the instance
`ssh -A username@ip-address`

- from within public instance
- ssh into private instance
- `ssh username@ip-address`

- ping a site on the internet
- not able to because private instance cannot communcate to outside world

---

## NAT Gateways and NAT Instances

- Allow private instances to send outbound traffic to the internet
- Allows private instance to
  - download software installs, updates
  - send a request outbound

NAT Gateway
- AWS managed service
- **NAT gateway is deployed in public subnet**
- has elastic public IP
- private route table has catch-all route pointing to the NAT Gateway
- scales automatically
- automatic high availability within AZ
- No security groups
- cannot access via SSH

NAT Instances
- EC2 instance configured to be NAT instance
- use AMI - `amzn-ami-vpc-nat`
- **disable source/destination checks**
  - allows instance to act as middle layer
  - private route table has catch-all route pointing to NAT instance ID
- Scales manually
- no high availability
- assigned Security groups
- Can use as a bastion host

---

## Exercise - Private Subnet with NAT Gateway

- Create private subnet
- Create private route table
- Create EC2 instance in public subnet
- Create EC2 instance in private subnet
- Deploy NAT gateway into public subnet
  - Allocate Elastic IP to it
- Update private route table to add catch-all route to NAT gateway

Clean Up
- delete NAT gateway (paid)
- detach and release elastic IP
- delete instances

---

## EC2 Instance Lifecycle

### Stopping EC2 instance
  - only available for EBS backed instances
  - Data in RAM is lost
  - no charge for stopped instances
  - EBS volues attached are chargeable
  - allow for moving an instance between hosts
  - public dynamic IP are lost

If instance is using instance store it cannot be moved to a "stopped" state

### Hibernating EC2 instances
- Allows for saving the state of an instance
- only for on-demand and reserved Linux instances
- contents of RAM saved to EBS volume
- hibernation capability must be enabled when launched
- upon starting back up
  - EBS is restored
  - RAM is reloaded
  - processes resumed
  - data volumes re-attached

### Rebooting EC2 instances
- equivalent to OS reboot
- DNS name, all IP addresses retained
- does not affect billing

### Retiring EC2 instance
- if underlying hardware has irreparable failure
- instance reaches scheduled retirement date, stopped/terminated by AWS

### Terminating an instance
- cannot be recovered a terminated instance
- by default root EBS volumes are deleted

### Recovering EC2 instance
- CloudWatch can be used to monitor system status checks and recover instance if needed
- If instance is impaired due to hardware issues

---

## Nitro Instances and Nitro Enclaves

#### Nitro Instances
- next generation of EC2 instance hardware
- improved performance and security
- breaks hardware into specialized functions
  - vpc
  - ebs
  - instance storage
  - [see slides for more]

#### Nitro Enclaves
- provide highly secure infrastructure to run code
- uses **cryptographic attestation** to ensure only authorized code is running
- Good for securing highly sensitive data
  - PII
  - healthcare data
  - financial data

Instance types<br>
[https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html)

---

## EC2 Pricing Options

#### On-Demand
- no commitment
- no discount
- best for short term or unpredictable workloads
  - dev/test


#### Reserved
- 1 or 3 year commitment
- Up to 75% discount
- best for steady, predictable workloads

#### Spot Instance
- Bid for unused capacity on AWS
- up to 90% discount
- can be terminated at any time
- best for workloads with flexible start and end times

#### Dedicated Instance
- runs in a VPC or hardware that is dedicated to a single customer
- not on its own server entirely
- pay per instance

#### Dedicated Host
- a physical server dedicated for your use
- best for
  - need to accomodate existing server-bound licenses
  - address corporate / regulartory complience

#### Savings Plan
- commitment to consistent amount of usage
- pay by hour
- 1 or 3 year commitment

---

## EC2 Billing

#### billed per second
- Amazon Linux and Ubuntu instances
- on-demand instances
- reserved instances
- spot instances
- EBS volumes

#### billed per hour
- Red Hat EL instances
- SUSE ES instances
- Windows instances

---

## Reserved Instances (RI)

Can pay for 1 or 3 years
  - all upfront
  - partial upfront
  - no upfront

RI are purchased for a specific AZ and a region

Specify the Tenancy
- dedicated - running on dedicated hardware
- default - running on shared hardware

When the attributes of your used instance match the attributes of a Reserved Instance (RI) the discount is applied.

##### Standard RI
  - allows you to change
    - AZ
    - instance size (linux)
    - networking type

##### Convertable RI
  - allows you to change
    - all features from standard RI
    - instance family
    - OS
    - tenancy
    - *payment option*

---

## Scheduled RI

- **AWS no longer has Scheduled RIs available**
- reserving capacity based on recurring schedule
- minimum of 1200 hours/year
- example
  - reporting app that runs
    - 6 hours/day
    - 4 days/week

---

## Savings Plan

Compute Savings Plan
- 1 or 3 year commitment
- hourly commitment to useage of Fargate, Lambda, and EC2
- Any region, family, size, tenancy, OS

EC2 Savings Plan
- 1 or 3 year commitment
- hourly commitment to useage of EC2 within selected region and instance family
- Any size, tenancy, and OS

---

## Spot Instances

- Bid for unused capacity at up to 90% discount

- AWS gives you 2 minute warning if they need to reclaim capacity
  - available via
    - metadata
    - CloudWatch Events

#### spot instances
- one or more EC2 instances

#### Spot Fleet
- launches and maintains a number of spot and on-demand instances to meet specified target capacity

#### EC2 Fleet
- launches and maintains a number of spot, on-demand, and reserved instances to meet specified target capacity

---

## Spot Block

- reserve 1 - 6 hours of uninterrupted time
- 30% - 45% discount
- specify
  - block duration
  - instance count
  - bid price

---

## Dedicated Instances and Dedicated Hosts

Both
  - enable use of dedicated physical servers

Dedicated host
  - visibility of sockets, cores, host ID
  - targeted instance placement
  - can add capacity using an allocation request
  - per host billing / not billed by instance

[See slide]

---
