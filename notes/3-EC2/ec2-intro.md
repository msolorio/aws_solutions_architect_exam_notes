# EC2 - Elastic Compute Cloud

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
A VPC's public route table has entries for
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

Connecting to a private instance from the internet by using a public instance as a middle-layer

[see slide]

For example
- SSH into the public instance
- from within public instance SSH into private instance

---

## Exercise - Private Subnets and Bastion Hosts



---
