## Elasticity - Scaling Up vs Out

Scalability
- ability to add resources to accomodate more load

Elasticity
- ability to add and remove resources to accommodate changing load

### Scaling Up - Vertical Scaling
- Add resource to a single instance
- weakness - there is still a single point of failure

### Scaling Out - Horizontal Scaling
- provides more resiliency
- can scale to unlimited capacity

---

## EC2 Auto Scaling

Elasticly horizontally scale EC2 instances

- can launch and terminate instances dynamically

- can replace instance if it goes offline
- can trigger adding / removing instances based on cloudWatch metrics
  - if average CPU hits certain threshold

**Scaling Policy** - defines how to respond to changes in demand

**Launch Template** - specifies configuration of EC2 instances to be launched by ASG.

**Launch config** - can also specify configuration for ASG, however, launch template provides more option.

Creating a Auto Scaling Group
- Choose launch template
- configure purchase option
  - on demand
  - spot
  - resevered
  - combination
- Choose VPC and subnets
  - ASG must live within single VPC
- Can attach a load balancer

---

## Health Checks

#### EC2 status checks
  - enabled by default with ASG

#### ELB Health checks
  - can be used in addition to EC2 status checks with an ASG
  - recommended to enable EBL health checks if load balancer is being used

#### Health Check Grace Period
- how long to wait before checking health status of instance
- auto scaling waits until grace period completes to launch or terminate instances

#### Group Metrics
- data about ASG
- 1 minute granularity
- no charge
- must be enabled

#### Basic Monitoring
- instances individually sending metrics to CloudWatch
- 5 minute granularity

#### Detailed Monitoring
- instances individually sending metrics to CloudWatch
- 1 minute granularity
- charge applied

#### Cooldowns
- prevent auto scaling from launching or terminating before effects of previous activities have been applied
- defaults to 5 minutes

#### Termination policy
- determines which instances to terminate first on scale-in event

#### Termination Pretection
- can protect certain instances from being terminated

#### Standby State
- puts instance from `InService` state to `Standby` state to update or troubleshoot instance
- [look into further]

#### Lifecycle Hooks
- perform custom actions when ASG launches or terminates an instance 
  - for example
    - run script to install software after instance launches
    - pull data metrics from instance before it is terminated

**Warm Pool** - a set of instances already activated ready to handle traffic

---

## Exercise - Create Auto Scaling Group

- Create Launch Template
  - Amazon Linux 2 AMI
  - t2.micro instance
  - add key pair
  - add SSH access
  - advanced details
    - Add user data
      - found in provided code in 'Auto Scaling and ELB'
      - writes the AZ of the instance to index.html file

- Create Auto Scaling Group
  - select launch template
  - select public subnets
  - enable group metrics with CloudWatch
  - set group size
    - desired - 2
    - minimun - 2
    - maximum - 4

- Explore ASG console
  - details
  - activity
  - automatic scaling
  - instnace management
  - monitoring
  - instance refresh

clean up
- leave instances running for next exercise

---

## Load Balancing and High Aailability

High Availability
- if an instance goes down, traffic can be directed to different instance
- instances spread across AZs application has high availability

---

## Types of Elastic Load Balancers

#### Application Load Balancer
- Operates at the request level / HTTP / layer 7
- targets can include IP addresses, instances, Lambda Fxns, or containers
- can route based on
  - HTTP headers
  - path
  - host
  - query string parameter
  - source IP address
- does not support static IP addresses as target nodes
- source IP in `x-forwarded-for` header

#### Network Load Balancer
- Operates at connection level / Layer 4
- Targets can be IP addresses, and instances
- listens on
  - TCP
  - TLS
  - UDP
  - TCP_UDP
- Routes based on IP protocol data
- ultra high performance
- low latency
- TLS offloading
  - processing encryption for TLS connections
- does support static IP addresses as target nodes
  - good for whitelisting and firewalls
- source IP native in IP protocol data

#### Classic Load Balancer
- Not on the exam
- depricated
- performs on layer 4 and 7
- used for existing applications running in EC2-classic

#### Gateway Load Balancer
- used in front of virtual appliances
  - firewalls
- operates at layer 3
[see slide for more information]

---

### Load Balancer Type Use Cases

Application Load Balancer
- Web apps with L7 routing (HTTP/HTTPS)
- microservices architecture (docker containers)
- lambda targets

Network Load Balancer
- TCP and UDP based applications
- ultra-low latency
- static IP addresses as targets

Gateway Load Balancer
- Can load balancer virtual appliances
  - intrusion detection systems (IDS)
  - intrusion prevention systems (IPS)
  - next generation firewalls (NGFW)
  - Web application firewalls (WAF)
  - distributed denial of service protection systems (DDoS)
- can use with ASGs
- Can apply network monitoring and logging for analytics

--- 

## Routing with ALB and NLB

ALB
- Routing based on HTTP path and host
- [see slides]

NLB
- Routed based on IP protocol data
- Routing based on port number
- NLBs nodes can have elastic IPs for each subnet
- [see slides]



---
