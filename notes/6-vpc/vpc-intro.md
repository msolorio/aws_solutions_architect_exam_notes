# VPC

An isolated portion of the AWS cloud to deploy resources into

## IPv4 Addressing Primer

IP
- ids computer on the internet or a network

#### Domain Name Resolution
process by which a client can request from a DNS the IP address for a domain name

domain name --> IP address

---

## Structure of IPv4 Address

#### Dotted Decimal notation
xxx.xxx.xxx.xxx

#### Binary Octet
- Each section between dots
- A number between 0 and 255
- The number represented by 8 zeros and ones (8 bits or 1 byte)

---

## Network and Hosts

#### Network ID
- first set of bits within an IP address
- Identifies the network on the internet
- All computers within a network share the same network ID

#### Host ID
- last set of bits in the IP address
- Identifies the computer within a network

#### Subnet Mask
- [come back to this]
- declares
  - how many bits of the IP address are allocated to the network ID
  - how many bits are allocated to the host id

For example
- a subnet mask of /24 or 255.255.255.0 means
  - the first 24 bits of the IP address are allocated for the network id
  - the last 8 bits allocated for the host id

For example
- a subnet mask of /20 means
  - first 20 bits are allocated to the network ID
  - last 12 bits are allocated to the host id
  - XXXXXXXX XXXXXXXX XXXXYYYY YYYYYYYY
    - in this case
      - the X's represent the network id
      - the Y's represent the host id
    - these binary numbers can be converted to decimal to get the IP address

## Class A
- /8 bit subnet mask
  - 8 bits allocated for network id
  - 24 bits allocated for host id
- 10.0.0.0
- first assignable address - 10.0.0.1
- last assignable address - 10.255.255.254
- 16 mil usable host addresses per network

## Class B
- /16 bit subnet mask


## Class C
- /24 bit subnet mask

## Private IP Address Range
- not on the internet
- internally within company
- has 3 reserved IP ranges
  - 10.0.0.0 -> 10.255.255.255
  - 172.16.0.0 -> 172.31.255.255
  - 192.168.0.0 -> 192.168.0.255

---

## Classless Interdomain Routing
- breaks away from traditional class subnet masks
/20 is an example

[see slides]

---

# Amazon VPC overview

Logically isolated private portion of AWS cloud within a region

VPCs cannot span across regions

subnets cannot span across across AZs

Within VPC we have full control over
- defining CIDR blocks
- settting security
- setting routing
- subnets

**VPC Router**
  - handles connections that that are leaving a subnet
  - ensures data connection is sent to correct place
  - VPC route tables configure the VPC router

Internet Gateway
- sends data out to internet - **egress** traffic
- sends data from in from internet - **ingress** traffic

Can create up to 5 vpc within a region

Each VPC has a CIDR block
- IP range for resources and subnets within VPC

Each subnet has an IP range
- fits within CIDR block of VPC
- IP range for resources within subnet

[see slide]

#### Peering Connection
- direct connection between two VPCs

#### NAT instances
- allows outbound internet connectivity from private instances
- uses an EC2 instances

#### NAT Gateway
- allows outbound internet connectivity from private instances
- managed by AWS

#### Security Groups
- instance level firewall
- attached to the network interface attached to EC2 instance

#### Network ACL
- Subnet level firewall
- like a security group for a subnet

[more VPC components to cover later]

[see slide]

---

## Defining VPC CIDR Blocks

### Rules for Defining CIDR Block

- size can be between /16 and /28 subnet mask

- cannot overlap with existing CIDR block that's associated with the VPC

- cannot be changed after it is created

- first 4 and last IP addresses not available for use

- AWS recommends using CIDR blocks from RFC 1918 ranges
  - allocated for private networks
  - 10.0.0.0/8 - 10.255.255.255
    - 10/8 prefix
  - 172.16.0.0 - 172.31.255.255
    - 172.16/12 prefix
  - 192.168.0.0 - 192.168.255.255
    - 192.168/16 prefix

---

### An Example 
VPC CIDR block
- 10.0.0.0/16
- using 16 bits for the network ID

Subnet IP ranges
- 10.0.1.0/24
- 10.0.2.0/24
- 10.0.3.0/24
- using 24 bits for the network ID

Available instance IPs
- 254 - 5 = 249
- 249 IPs available in each subnet

---

## Considerations

- ensure you have enough networks and hosts
- bigger CIDR blocks are better
- smaller subnets ok for most use cases
- split Highly Available resources across subnets in different AZs
- VPC peering requires non-overlapping CIDR blocks
- Avoid overlapping CIDR blocks as much as possible

---

## Subnet Mask Calculator
[https://network00.com/NetworkTools/IPv4SubnetCreator/](https://network00.com/NetworkTools/IPv4SubnetCreator/)

---

## VPC Wizard

In VPC console
- Launch VPC Wizard
- Notice 4 options
  - 1). create VPC with single public subnet
    - creates /16 network with /24 subnet

  - 2). create VPC with public and private subnets
    - creates /16 network with 2 /24 subnets
    - creates NAT Gateway

  - 3). create VPC with public, private subnets and hardware VPN access
    - private subnet connected to your corporate network via VPN tunnel
  
  - 4). create VPC with only private subnet with VPN tunnel to your corporate network

---

## Exercise - Create Custom VPC

What we'll build

VPC with
- 10.0.0.0 CIDR block 
- /16 bit subnet mask
- 2 private subnets
- 2 public subnets
- Main route table
  - has route to Internet Gateway
  - automatically assigns IPv4 public addresses

In VPC console

create VPC
- use values from `custom-vpc.md`
- no IPv6 for now
- leave tenancy to default

Once created
- actions > add DNS hostnames > enable
  - assigns DNS hostnames for EC2 instances

Create Subnets
- specify custom VPC
- create 2 public in
  - AZ A
  - AZ B
- create 2 private in
  - AZ A
  - AZ B
- use designated CIDR blocks for subnets

For public subnets
- enable auto-assign IPv4 addresses

Create private route table
- select custom VPC

edit subnet associations for route table
- add private subnets

Create Internet gateway

After IG created
- attach to VPC
- Add route to IG in main route table

---
