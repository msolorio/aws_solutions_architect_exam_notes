# Monitoring, Logging, and Auditing

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

- Unified CloudWatch Agent can be installed on EC2 or on-premise
  - allows EC2 to send system level metrics
  - system level metrics
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
