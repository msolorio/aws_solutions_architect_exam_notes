# Deployment and Management

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



---