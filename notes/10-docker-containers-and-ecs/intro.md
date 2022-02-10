# Docker Containers and ECS

### Topics Covered
- Docker
- Elastic Container Service
  - allows us to run docker containers
- Amazon Elastic Kubernetes Service

---

## Virtual Machines Review

Server virtualization

decoupled server instance from the underlying hardware

Every VM/instance needs a separate OS
- uses a lot of resources

VM lives on top of hypervisor

## Docker Containers

Docker engine runs on top of OS

docker containers run on top of Docker engine

docker containers share the same OS

docker container contains
- code
- settings
- dependancies for application

each container isolated

containers
- start up very quickly
- very resource efficient

---

## Monolithic Applications

All components of an application
- user interface
- mobile interface
- account management
- payment services
- shipping service
- database
- live within the same application or codebase

All components deployed together

Cons
- if component needs to be updated
  - may need to update in multiple components
  - more room for error
  - harder to understand
- if error occurs
  - can be hard to track down error

---

## Microservices

Application broken into separate smaller codebases
- Microservices organized around business capabilities
- deployed separate

connections are simplified

loosely coupled
- components are not directly integrated
- there is layer between them

### Microservices with Containers

Each microservice can live in a separate container on the same host machine

All microservices share the same OS

Microservices application can live on many host machines
- redundancy
- fault tolerant

---

## Amazon Elastic Container Service

Service used for managing containers in AWS

### Key Components

ECS Cluster
- logical grouping of EC2 instances running tasks / containers
- can span many AZs

Container Instance
- EC2 instance running ECS agent / task

Task
- A running docker container

Task Definition File
- specifies
  - the configuration for launching the Task
  - the container image from container registry to use for the task
  - links to other tasks
  - and more

Elastic Container Registry
- stores container images

ECS Service
- maintains a desired count of tasks
- based on parameters we specify
- handles auto scaling **for tasks**

ECS Contianer Instance
- EC2 instance to house 1 or more tasks

Auto Scaling Group
- Auto scales ECS containers

---

## Key Features

### ECS
- Fully managed container orchestration
  - control plane is managed for you by AWS
- integrates with Docker Compose CLI
- integrates with a Load balancer
  - distribute traffic across containers
- ECS Anywhere
  - use ECS control plane to manage on-premises implementation

### AWS Farget
- Allows for serverless management of ECS
- AWS manages all underlying infrastructure of ECS
  - good for scaling
  - don't have to worry about capacity management

---

## ECS Launch Types

### EC2 Launch Type
- you
  - explicitly provision EC2 instances
  - responsible for managing EC2 instances
  - handle cluster optimization

Charged for running EC2 instance

Pros
- More granular control over infrastructure

### Fargate Launch Type
- Fargate
  - provisions EC2 instances
  - charged for running task
  - handles cluster optimization
  - limited control

---

## Exercise - Launch Task on AWS Fargate

Create a Cluster
- in ECS console
  - use new console
- Get Started
- Create cluster
- name - FargateCluster
- Enable CloudWatch Container Insights
- View Cluster

After creation
- explore tabs

Create Task Definition
- In Task Definition Tab
- Create new Task Definition
- Fargate
- name - nginx
- Add Container
  - container name - nginx
  - image - nginx
    - will pull down image for nginx
  - port - 80
  - Add
 
Add Task Definition to Cluster
- back in custer console
- add task definition to cluster we created
  - run new task
  - family - nginx
  - desired task - 1
- security group
  - HTTP, SSH
- public IP enabled

Get IP of task
- paste in browser
- see nginx default page

Clean up
- stop task

---

## ECS and IAM Roles

### EC2 Launch Type

ECS container instance (EC2 Instance)
- Has its own role attached to it
- IAM Instance Role
- Grants instance permissions it needs as a host

Task / Container
- runs inside of Container instance
- has its own role attached to it
- provides permissions to Container
- If different containers have different permissions
  - should use different roles

---

### Fargate Launch Type

Only need to configure IAM Task Role

IAM Task Role can be defined
- in the task defintion
- when running the Task

---

## Scaling Amazon ECS

2 types of scaling
- Service Auto Scaling
- Cluster Auto Scaling

#### Service Auto Scaling
- scales the tasks or containers running in ECS cluster
- Tasks send metrics to CloudWatch
- CloudWatch uses metrics to launch and terminate tasks
- can use scaling policies
  - target tracking
  - step scaling
  - scheduled scaling

#### Cluster Auto Scaling
- scales the EC2 instances running in the ECS cluster
- **Capacity Provider Reservation**
  - determines how many EC2 instances are needed
  - sends metrics to CloudWatch
  - CloudWatch sends to **Auto Scaling Group**
  - Capacity Provider is associated with ASG

[Come back to this]

---

## ECS with ALB

ALB has container awareness

A container instance can house many containers / tasks
- each task has a dynamic port associated with it
  - gets associated with task internally to instance
- ALB knows which dynamic port to direct traffic to

[See Slide]

---

## Deploy ECS Cluster - EC2 Launch Type

Create a Cluster
- ECS management console
- create cluster
- EC2 Linux + networking
- name - EC2Cluster
- choose a key pair
- VPC - myVPC - the custom VPC
- choose 2 subnets
- number of instances - 1
- enable Auto assign public IPs
- security group
  - SSH, HTTP
- Container Instance IAM role
  - create new role
- set CloudWatch Container insights

In CloudFormation console
- Infrastructure automation service

- Can see
  - cluster
  - ASG
  - Launch configuration
- is being created

create new task definition
- EC2
- name - httpd
- Add container
  - container name - httpd
  - image - httpd:2.4
  - port mapping
    - 80 and 80
  - memory limit
    - hard limit - 32

Cluster > EC2Cluster
- note container instance is running
- in services tab
  - create service
    - EC2
    - number of tasks 2
  - no load balancer
  - no auto scaling yet

notice 1 task does not run
- if we want to have 2 tasks on same instance
- they must have different host ports

Task Definition console
- revision
- network mode - revision
- in container
  - host port - 0
  - container port - 80

in EC2Cluster
- update service
- set to use revision 2

If you want to run multiple containers in container instance
- you must enable bridge mode

Clean Up
- delete service
- drain ECS instance
- delete cluster

** [Come back to this] **

---

## Exercise Use ALB with Fargate Cluster

Prerequisite
- have a task definition created
  - nginx
  - network mode - awsvpc
  - compatible with Fargate

Deploy containers
- select the Fargate Cluster
- deploy service
- Application type - service
- family - nginx
- desired tasks - 4
- Choose an Application Load Balancer
  - Create a load balancer
  - name - myEcsAlb
- Target group - tg-ecs-app2
- health check grace period - 30 seconds
- Networking
  - custom VPC
  - public subnet 1A and 1B
- security group
  - HTTP
  - SSH
- Public IPs

Clean Up
- Delete service in Fargate Cluster
- Delete Load Balancer that was created

[Come back to this]

---

## Elastic Kubernetes Service

Open source container orchestration platform
- similar to ECS
- 3rd party platform

can run on
- EC2 Launch Type
- Fargate Lauch Type
- AWS Outposts

Supports Load balancing with
- ALB
- NLB
- CLB

[common exam question]
Pods - groups of containers in Kubernetes

#### Use Cases
- [common exam question]
  - A hybrid between on-premise and cloud containers
- Could be used when a company who is used to using Kubernetes migrates to the cloud
- Batch Processing - can plan and execute batch workloads
- Machine learning
- Web applications

---

## Cheat Sheets

#### ECS and EKS Cheat Sheet
[https://digitalcloud.training/certification-training/aws-solutions-architect-associate/compute/amazon-ecs/](https://digitalcloud.training/certification-training/aws-solutions-architect-associate/compute/amazon-ecs/)
