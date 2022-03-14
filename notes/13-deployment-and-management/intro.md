# Deployment and Management

## Cheat Sheets

#### CloudFormation
https://digitalcloud.training/aws-cloudformation/

#### Elastic Beanstalk
https://digitalcloud.training/aws-elastic-beanstalk/

#### AWS Config
https://digitalcloud.training/aws-config/

#### RAM - Resource Access Manager
https://digitalcloud.training/aws-resource-access-manager/

#### SSM - Systems Manager
https://digitalcloud.training/aws-systems-manager/

#### OpsWorks
https://digitalcloud.training/aws-opsworks/

---

Topics covered
- CloudFormation
- Elastic Beanstalk
- AWS Config
- Storing secret information
- AWS Resource Access manager

---

## CloudFormation

Tool for building infrastructure with code

Steps
- Code is written in CloudFormatin template
- template is given to AWS
- AWS builds infrastructure from template

Languages
- JSON
- YAML

Benefits
- repeatability
- automation
- infrastructure provisioned consistently
- Less room for human error
  - if template has already been tested and works
- less time / effort for repeated stack deployments
- Version control / peer review for templates
- can manage updates and dependancies
- can be used to delete an entire stack

---

## CloudFormation - Components

#### Template
- JSON or YAML file
- contains instructions for building AWS stack / environment

#### Stack
- The environment described by a template
- full set of resources and services to be deployed
- created, updated, deleted as a single unit

#### StackSet
- Allows for creating, updating, deleting stacks across
  - multiple accounts
  - multiple regions

#### ChangeSet
- Summary of proposed changes to a stack
- can see how changes will impact existing resources before implementing

---

## Exercise - Deploy Stack and Change Set

Find demo CloudFormation templates
- in provided materials in CloudFormation folder

#### Example 1

In us-east-1
- in CloudFormation console
- create stack
- select template is ready
- upload template 1 from provided materials
- name stack
- Create Stack

After creation
- observe events
- resources getting created

#### Example 2
- stack actions > create change set
- replace current template
- upload template 2
- create change set
- once complete can view changes
- execute
- view new Elastic IP created
- note Elastic IP is applied to instance

#### Example 3
- Change to us-west-1
- create a stack
- upload a template file
- upload template 3
- Notice resources created
- delete the stack

#### Example 4
- in template 4
  - note the block defining parameters
- in us-east-1
- create a stack
- template is ready
- upload template 4
- note that we have the option to choose which instance type
- choose and deploy
- delete stack

---

## Exercise - Complex CloudFormation

Use template to create multi-AZ Auto Scaling Group with an ALB

Inspect the provided AutoScalingALBTemplate.json file
- many parameters specified for the stack
- note the specified resources
- setting an index.html file with contents
- creates ALB, ASG, instance, security groups

Create stack
- use this URL to deploy template
  - has the most recent code
  - https://s3.amazonaws.com/cloudformation-templates-us-east-1/AutoScalingMultiAZWithNotifications.template

After creation
- May need to update security group of EC2 instance
- add HTTP inbound

---

## Elastic Beanstalk

Platform as a service
- similar to CloudFormation however focuses on web apps
- allows for managing platform as well

Automates creation of web applications running on EC2
- creates
  - instances
  - Auto Scaling Group
  - Load Balancer
  - database
  - development environment
    - for example, Node.js

Manages the environment for you

Supports EC2 instances or Docker containers on ECS

#### Applications
contains
- environment
- evironment configuration
- application version
  - each version is deployable code

Can have multiple application versions and environments held within an application

#### Environment
- a version that has been deployed to AWS
- development or production can be versions

#### Web Servers Environment
- standard application that listens for HTTP requests

#### Worker Environment
- used for offloading background processing tasks from Web server
- listens for messages on an SQS queue

[See Slide for example Application]

---

## Exercise - Deploy Elastic Beanstalk Web Server and Worker

Elastic Beanstalk Console
- create application
- choose Node.js for platform
- create sample application
- configure more options
- test different presets to see different configuration options
- select single instance
- can take 10 minutes to complete

After complete
- click on DNS name
- explore console for app
  - can deploy code to application

Add an environment to application
- create a new evironment
- select worker environment
- node.js
- sample application
- visit link for queue
  - creates main queue and dead letter queue

Clean Up
- delete web app and worker environment

---

## SSM Parameter Store

Used for storing secrets and configuration data
- passwords
- database connection strings
- license codes

Features
- scalable
- highly available
- durable

store values as
- plaintext
- ciphertext - encrypted

Reference the parameter names in application code to pull in values

**No native rotation of keys**
- different than AWS Secrets Manager

---

## AWS Config

- Used for viewing and managing configuration of resources in AWS
- Often used in compliance scenarios

Uses
- see if current resource configurations match desired settings
- get configruations of resources in account
- set notifications when resource is created
- view relationships between resources

Example
- Can have resources in our account send configuration data to AWS Config
- AWS Config regularly takes snapshot of configurations
  - stores in S3
- Have AWS Config trigger a notification when a configuration is set
- Have AWS Config set rules for what configurations are allowed
  - **Automatic remediation**

Can set rules for
- ensuring S3 buckets have encryption enabled
- ensure SSH access is not public
  - disallows anyone from connecting
- ensure databases are not publicly accessible

---

## Exercise - AWS Config Rule with Remediation

What we will build
- Create a rule to ensure S3 bucket has server side encryption enabled
- Set automatic remediation to add encryption if it is not enabled

In AWS Config console
- Note the inventory of resources in our account
- create a rule
- AWS managed rule
- search for s3 bucket server side encryption
- choose Tags as scope of the rule

Add tag to bucket
- choose an existing S3 bucket
- make sure encryption is disabled
- add tag to bucket
  - key - Classification
  - value - sensitive

In AWS Config
- set same key and value as the Tag trigger

Select rule
- action > manage remediation
- Automatic remediation
- choose remediation action
  - AWS-EnableS3bucketEncryption
- resource ID - BucketName
- parameters
  - For SSE Algorithm - AES256

Crete role in IAM
- create role
- use cases - Systems Manager
- select System Manager on bottom
  - Allow SSM to call AWS services on your behalf
- policy
  - AmazonS3FullAccess
- role name - AWSConfigRemediation
- after created
  - copy role ARN

Back in SSM rule creation window
- Parameters
  - Add role ARN to AutomationAssumeRule

After rule creation
- note rule lists S3 bucket is noncompliant
- actions > reevaluate
  - will run automation
- after few minutes
  - encryption should be enabled on bucket

---

## AWS Secrets Manager

Used for storing secrets and configuration data
- passwords
- database connection strings
- license codes

Automatic rotation of credentials for
- RDS, Redshift and DocumentDB
  - different than **SSM Parameter Store**
- other services
  - can write Lambda function for automatic rotation

#### Secrets Manager vs SSM parameter store
[Common Exam Questions]

Secrets Manager
- Automatic rotation of keys for some services
  - RDS
  - RedShift
  - DocumentDB
- pay per secret

SSM Parameter store
- no native rotation
- free for standard
- pay for advanced

---

## OpsWorks

Configuration management service
- managed instances of Chef and Puppet

- Allows for configuration, deployment, and management of servers in the cloud

Use Cases
- moving on-premise configuration management that is already using Chef or Puppet to AWS

How it works
- SysOps admin submits configuration changes to OpsWorks
- Instances controlled under OpsWorks are configured

---

## RAM - Resource Access Manager

Allows for sharing resources within and across accounts

What can be shared
- [https://docs.aws.amazon.com/ram/latest/userguide/shareable.html](https://docs.aws.amazon.com/ram/latest/userguide/shareable.html)


---

## Exercise - Share a Subnet Across Accounts

#### What we will build
- Share a subnet from management account with production account
- Management account is the VPC owner
- Production account is the participant
- participant
  - can create, modify their own resources
  - cannot view resources of other participants or owner

- Ping from instance in participant account
- to instance in owner account

[See Slide]

#### Steps
Going to share subnet 1A from custom VPC

- Create a security group
  - accepts inbound ICMP traffic
    - only from public subnet 1A
  - accept SSH

Launch instance
- in custom VPC
- in subnet 1a
- select security group just created

in Resource Access Manager console
- settings > enable sharing with AWS Organization

Create a resource share
- select public 1a from custom VPC
- Allow sharing with principals in your organization only
- display org structure
  - select production account

Switch to Production account
- Launch instance
  - in shared custom VPC
  - choose shared subnet
  - create security group
    - add inbound ICMP traffic from subnet 1a
    - inbound SSH

Notice that the instance has same subnet IP as other instance

Prod Instance
pub - 34.222.20.253
private - 10.0.1.120

Management Instance
pub - 34.209.103.198
private - 10.0.1.82

Clean up
- delete instances
- delete security groups

---

## EC2 Run Command

Run PowerShell Commands through the AWS console for administering changes on many EC2 instances simultaneously

---