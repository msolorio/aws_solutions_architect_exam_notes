# Monitoring, Logging, and Auditing

## Cheat Sheet

#### CloudWatch
[https://digitalcloud.training/amazon-cloudwatch/](https://digitalcloud.training/amazon-cloudwatch/)

#### CloudTrail
[https://digitalcloud.training/aws-cloudtrail/](https://digitalcloud.training/aws-cloudtrail/)

---

## CloudWatch

#### CloudWatch Uses
- collect performance monitoring
- alarms
- log collection
- automated responses to operational changes
- get actionable insights from logs

#### CloudWatch Features
CloudWatch Metrics
- services send data to CloudWatch

CloudWatch Alarms
- Monitor metrics and trigger actions

CloudWatch Logs
- collection of system and application logs

CloudWatch Events
- stream of system events describing changes to resources
- can trigger actions
- similar to EventBridge

---

## CloudWatch Metrics

- Services send metrics to CloudWatch
- EC2 sends metrics
  - every 5 minutes (free)
  - every 1 minute (paid)
  - CPUUtilization, DiskREadOps, etc...

- Unified CloudWatch Agent
  - can be installed on EC2 or on-premise
  - allows EC2 to send system level metrics
    - memory
    - disk usage

Custom Metrics
- can send custom metrics
- 2 resolutions
  - standard resolution (default)
    - 1 minute granularity
  - high resolution
    - 1 second granularity

---

## CloudWatch Alarms

Metric Alarm
- trigger an action based on a single metric
- states
  - OK
  - ALARM
  - INSUFFICIENT_DATA


Composite Alarm
- trigger an action based on multiple metrics
- can trigger many actions

---

## Exercise - Create a Custom Metric

Node the provided file in CloudWatch folder
- commands to add metrics to CloudWatch

Launch an EC2 instance
- note the instance id

Create a role to allow EC2 to add data to cloudWatch
- policy
  - CloudWatchAgentServerPolicy

Attach role to EC2 instance

SSH into instance

Run first and second provided command
- paste in instance ID and region

In CloudWatch
- All metrics
- MyCustomNameSpace
  - this is the namespace defined in the command we just ran

Clean Up
- leave instance running for next exercise

---

## Exercise - Create a CloudWatch Alarm

Continuation from last Exercise

In CloudWatch console
- all alarms
- create alarm
- select metric - MyCustomNameSpace
- choose latency metric
- statistic - Maximum
- period - 1 minute
- Whenever latency is greater than 30
- No notificaiton
- EC2 action
  - in alarm
  - terminate this instance
- name alarm - LatencyTooHigh
- create alarm

SSH into EC2 instance
- run 2nd command
  - notice alarm is not triggered
- run 3rd command
  - the alarm triggers

---

## CloudWatch Logs

We can send application logs and system logs
- from EC2 or our on-premise servers
- to CloudWatch

From CloudWatch
- we can send logs to a data source for storage
- or another service for processing of the logs

---

## Review CloudWatch Logs

In CloudWatch console
- logs > log groups
- inspect the log groups we already have
- log streams inside each of the log groups
  - in each stream are the events
- explore tabs

- Explore logs > log insights
  - can run run query on all logs groups agreggated
---

## Unified CloudWatch Agent

- Allows us to collect system-level metrics from
  - EC2 instances
  - on-premise servers
- Retrieve custom metrics using StatsD and collectd protocols

- Agent is installed on server
  - can be installed on Linux, Windows, or MacOS

- Sends logs in near real-time

[Common exam question]
- If agent is installed on EC2 instance you can get logs sent over before the EC2 instance is terminated

---

## Unified CloudWatch Agent Metrics

Windows
  - metrics from Windows Performance Monitor collected and sent to CloudWatch logs

Metrics for Linux and MacOS
- many metrics associated with disk
  - disk_free
  - disk_used
  - disk_used_percent
- memory
  - mem_percent_available

Metrics Collected
[https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/metrics-collected-by-CloudWatch-agent.html](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/metrics-collected-by-CloudWatch-agent.html)

---

## AWS CloudTrail

Logs data about actions occuring in AWS account

Logs all AWS API activity for auditing

- Who
- did what
- when

Retaied for 90 days
- if need to store for more than 90 days
  - can create CloudTrail Trail that logs events to S3

Can trigger CloudWatch Events
- based on API calls in CloudTrail
- events can be streamed to CloudWatch logs

## How it works
- CloudTrail logs API actions to account for 90 days

- Can be sent to an S3 bucket for indefinite retention
  - S3 bucket can enable **log file integrity**
    - ensures CloudTrail logs have not been tampered with
    - Logs can be used for auditing / compiance

- CloudTrail can send an SNS topic for API actions

- CloudTrail can send logs to CloudWatch logs
  - can then trigger CloudWatch Alarms

- Can send CloudTrail logs to CloudWatch Events

---

## Types of Events in CloudTrail

#### Management Events
- provide information about
  - who performed action
  - what time
  - on what resource
- must be enabled

#### Data Events
- provides more detailed information about
  - the action performed on a resource

#### Insights Events
- identify and respond to
  - unusual activity
  - associated with write API actions

---

## Exercise - Create a CloudTrail Trail


Explore CloudTrail dashboards
- In CloudTrail console
  - can see
    - trails
    - event history
- explore events in history

Create a Trail
- trails on left
- Create a trail
- Create new S3 bucket
- create new managed KMS key
- Enable log file validation
- Enable CloudWatch logs
- Create new IAM role
  - name - CloudTrailRoleForCWLogs
- Event type - management events
- read and write API activity

---

## EventBridge

The successor to CloudWatch Events

Steps
- Action is performed on an AWS service
- AWS service (event source) sends an event to EventBridge
- Event Bridge can define rules
  - what to do when a particular event is recieved
  - can trigger
    - Lambda
    - send data to Kinesis Firehose
    - SNS topic
    - etc...

---

## Exericse - Create EventBridge Rule for CloudTrail API calls

#### What we will build
- trigger an event in EventBridge in response to an action being recieved by CloudTrail 
- When an EC2 instance is stopped
  - EventBridge triggers a lambda function
  - to send information to CloudWatch logs

#### Steps

Create Lambda function
- use hello-world blueprint

- In provided materials CloudTrail folder
  - get Lambda function code
  - paste in as code for lambda function
  - name - LogEC2StopInstance
- Deploy

In EventBridge console
- create rule
- name - LogEC2StopInstance
- event pattern
  - pre-defined pattern by service
  - AWS
  - EC2
  - event type - AWS API call via CloudTrail
  - specific operation - StopInstances

- Target
  - Lambda function
  - LogEC2StopInstance

Test
- launch an instance
- stop an instance
- see event in event history in CloudTrail

in EventBridge
- select rule
- metric for rule
- see metric for stop intance action

In Log Groups
- See log group for LogEC2StopInstances
  - see log stream
  - inspect information in logs

Clean up
- terminate EC2 instance

---