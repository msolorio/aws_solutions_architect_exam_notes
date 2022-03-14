# Serverless Applications

## Cheat Sheets

#### AWS Lambda
[https://digitalcloud.training/aws-lambda/](https://digitalcloud.training/aws-lambda/)

#### API Gateway
[https://digitalcloud.training/amazon-api-gateway/](https://digitalcloud.training/amazon-api-gateway/)

#### Application Integration Services
- Many services in here
- [https://digitalcloud.training/aws-application-integration-services/](https://digitalcloud.training/aws-application-integration-services/)

#### CloudWatch
[https://digitalcloud.training/amazon-cloudwatch/](https://digitalcloud.training/amazon-cloudwatch/)

---

Allows for AWS to manage any infrustructure related concerns
- managing EC2 instances
- updating
- installing patches

Topics covered
- Lambda
- application integration
  - message busses
  - notification services
  - API gateway

---

## Serverless Services and Event-Driven Architecture

Serverless
- you don't manage underlying servers and infrustructure
- there are no instances to manage
- don't need to provision hardware
- no management of OS
- all capacity provisioning and patching handled automatically

- automatic scaling and high availability
- can be very cheap

Example
[See Slide]

---

## AWS Lambda

- Serverless compute service
- for use in event driven architecture
- running code based on triggers

Lambda functions has maximum execution time of 15 minutes

#### Steps
- Create a lambda function from console or CLI
- upload a zipfile to the lambda function
  - the code that will eventually run when lambda function is truggered
- Event trigger lambda function via CLI, API, SDK, or another service
- Code is executed

#### Pros
- executes code only when needed
- scales automatically and continuously
- pay only for what you use
  - pay nothing if code is not running
- no servers to manage
- billed by the millisecond
- integrates with other AWS services

#### Primary use cases
- data processing
- real-time file processing
- real-time stream processing
- serverless backends for
  - web
  - mobile
  - IOT
  - 3rd party API requests

## Function Invocation

### Synchronous Funciton Invocation

- Function is triggered by CLI, SDK, API Gateway
- wait for function to process the event and then return a response
- error handling is client side

---

### Asynchronous Function Invocation

- function is triggered by a AWS service
- event is queued for processing
- response returned immediately
- lambda retries 3 times
- processing must be idempotent
  - the response after processing must be a response we expect

---

### Event Source Mapping

Lambda triggers itself

Lambda can regularly poll a data source
- if it finds a new item in the data source
- lambda will pick it up and process it
- records are processed in order

data sources can be
- SQS
- Kinesis Data Streams
- DynamoDB Streams

---

## Lambda Concurrency

Multiple Lambda functions invokations can be executed in parallel
- multiple concurrent functions

Concurrent functions are initialized up to burst limit or account limit

Lambda burst concurrency quotas
- 3000 - us-west-2, us-east-1, [see slide]
- 1000 - [see slide]
- 500 - [see slide]

If limit is execeeded
- throttling occers
- error - 429 - `TooManyRequestsException`

---

## Other notes

### Permissions

If Lambda needs to interact with other services it needs privileges
- by default has no privileges
- principal of least privilege

### Trigger
- can set another AWS service to trigger function

### Destination
- where the lambda function will send a response to

---

## Exercise - Create Simple Lambda Function

What we will build
- Create a lambda function
  - has default hello world code
- function invokation will write event data to CloudWatch Logs

Create function
- in lambda console
- create function
- Author from scratch
- Create new role with basic privileges

Explore console for our new function

Run a test
- in test tab
- use hello world template
- save
- test
- explore details
  - see response
  - billed milliseconds
  - memory used
  - Log output
- explore monitor tab
  - these metrics available in CloudWatch
  - click "view logs in CloudWatch"
    - click on the log stream created
    - can see info for function invokations
- explore configuration tab
  - permissions
    - view permissions assigned to lambda function

---

## Application Integration Services Overview

### Decoupled Application
[common exam question]

- Components are not directly communicating with each other
  - not dependent on one another
- communicate via a queue
- communication can now happen **asynchronously**

---

## Types of services

### SQS - Simple Queue Service
A message queue service
- messages come into its queue via another service (EC2, lambda)
- another service reads queue and processes data from records
- can have as many workers / consumers as needed
- ordering is not gauranteed
- with FIFO queue ordering is gauranteed

For building distributed and **decoupled** applications

**Can scale other services based on the number of messages in the SQS queue**

### Simple Notifications Services
configure a service to send a notification
- can send email when CloudWatch alarm is triggered
- Publisher / subscriber model
- up to 10 Million subscribers
- up to 100 thousand topics
- data not persisted

### Step Functions
Can coordinate a workflow with multiple cascading Lambda functions
- recommended over simple workflow service if possible

### Simple Workflow service
- old version of step functions
- for use with human-enabled workflows
  - for example a fullfilment center
  - communication taken off automated system
  - brought back on to automated system

### Amazon MQ
- Queueing service
- based on Apache Active MQ or RabbitMQ

### Amazon Kinesis
- collect, process, analyze streaming data

---

## Kinesis vs SQS vs SNS
[See Slide]
[Common Exam Questions]

___
- SNS pushes data to subscribers
- Where as Kinesis and SQS
  - consumers pull data from its queue

---

## SQS

- Allows for decoupling components
- requests can be written to a queue to be process asynchronously
- allows for handling bursts of traffic

[Common Exam Question]
- SQS is **poll based**

---

### SQS Queue Ordering

#### Standard Queue
- records are not processed in order

To have records processed in order
- include data in record to establish an order
- picked up by the processing layer

#### FIFO Queue
- Keeps first-in, first-out order
- guarantees **exactly once processing**

---

### Throughput

#### Standard Queue
- supports unlimited number of messages per second

#### FIFO Queue
- High Throughput
- up to 300 messages per second
- can batch 10 messages per operation for 3000 messages per second

---

### Douplicates

#### Standard Queue
- At least once delivery
- message could be sent multiple times

#### FIFO Queue
- Exactly-once processing
- message is delivered only once every time

---

## FIFO Queue

#### Message Group ID
- every message has one
- assigns a message to a group
- messages within the same group are processed in FIFO mannter

#### Message Deduplication ID
- token used for deduplication of messages within the set interval

---

## Dead Letter Queue

A configuration of a FIFO or standard queue
- messages not processed successfully are moved to **dead-letter queue**
- from dead-letter queue they can be analyzed

For handling message failure

---

## Delay Queue

Can set a delay within which a message is invisible in the queue and not processed 

Within the **delay seconds** period the message is not seen by the consumer and not processed

---

## Visibility Timeout

The amount of time the SQS queue will give to a consumer to process a message.

When message is picked up by comsumer
- it is made invisible on the queue
- if the consumer finishes processing the message
  - message is removed from the queue
- if consumer doesn't finish processing before the **visibility timeout**
  - message is made visible and picked up by another consumer

Could lead to messages being processed more than once if visibility timeout is too low

FIFO queue guarantees **exactly once processing**

---

## Short Polling

Queue consumer
- polls the queue and returns immediately
- processes whatever available records it finds
- may not return a message at all

---

## Long Polling

Queue consumer
- polls queue for set amount of time
  - `WaitTimeSeconds`
- Waits for messages to come in
  - eliminates empty responses
- could reduces the number of poll API calls needed to be made

Set Long polling by setting receive message wait time to greater than 0

---

## SNS - Simple Notification Service

Follows a Publisher / Subscriber model

A Publisher configured to send a message to an SNS Topic

Subscribers that subscribe to a particular topic

When SNS topic receives a message it sends out a message for each subscription

Subscribers can be
- Lambda functions
- A queue service
- web application
- email
- text

[Common Exam Question]
- SNS is pushed based
- SNS pushes message to subscribers
- instead of having consumers poll it

Allow many-to-many messaging

SNS has easy integratin with applications

Supports delivery over multiple transport protocols

[Common Exam Question]
- SNS and SQS Fan-Out
  - A topic has multiple SQS queues as subscribers
  - When message received by Topic
    - message is sent to each of the subscribing queues

---

## Exercise - Simple Event Driven App (Serverless)

What We'll Build
- An SQS that sends a message to a Lambda function
- Lambda function
  - adds a record to a DynamoDB table
  - writes to CloudWatch logs

Steps
- [see provided instructions in Simple Event Driven App]

---

## AWS Step Functions

Coordinates the components of an application

Used to build distributed applications as a series of steps in a visual workflow

How it works
- define steps of workflow in Amazon State Language (JSON)
- execute the application to visualize and verify steps

---

## Exercise - Create a State Machine

Step Functions
- get Started
- view hello world example
- leave defaults
- create state machine
- start execution
- view state machine executing

Use Lambda Function with Step Functions
- [See provided instructions in Step Functions]

---

## EventBridge

Serverless Event Bus
- for building distributed event driven applications

Steps
- A change in an event source triggers an **event**
- event is sent to an **Event Bus** om **Evemt Bridge**
- In the event bus, the event triggers a rule
- the rule sends a message to another Service

[See slide]

---

## Exercise - Create an Event Bus and Rule

What We'll Build
- A termination on an EC2 instance triggers an event
- event sent to Event Bus in EventBridge
- event triggers a rule
- rule says to trigger notification in SNS topic to send email

Steps
- Launch EC2 instance
- copy instance ID

In EventBridge
- create a rule
- name - EC2StateChange
- choose event pattern
  - pre-defined pattern
  - service provider - AWS
  - service name - EC2
  - event type - EC2 instance state-change notification
  - specific states - terminated
  - add instance ID

  - specify a target
    - SNS
    - topic - myEmailNotification from earlier in course
  - create

Test
- terminate EC2 instance
- check email

---

## API Gateway

Used for creating REST / HTTP APIs or web socket APIs

- Provides an endpoint for accessing API
- API Gateway can then forward request to any number of services
- Can integrate with SWagger / Open API definition

---

## API Gateway Deployment types

Edge-optimized endpoint
- **default deployment type**
- leverages the CloudFront infrastructure
- provides clients to the API reduced latency

Regional Endpoint
- API Gateway exists in a region
  - reduced latency for requests originating in that region
- Can configure a CDN
- Can protect with WAF

Private Endpoint
- Provides a private secure connect to the API
- Allow access to API only to services in the same VPC
- can connect via VPN or via direct connect

Structure of a REST API
- [See Slide]
- within API gateway
  - Map request parameters to a format required by various AWS services
  - can send to Lambda, EC2, etc...

---

## API Gateway Integrations

Lambda Function
- Lambda Proxy
  - sends request straight through to Lambda

- Lambda Custom Integration
  - allows for custom

HTTP Endpoint
- HTTP Proxy
  - sends request straight to HTTP endpoint

- HTTP Custom Integration
  - allows for custom

---

## API Gateway - Caching

Can provision an API Gateway Cache
- specify the size in GBs
- can reduce number of calls to backend
- improve latency of requests to API

Steps
- client makes request
- API Gateway checks API cache first
- if not found in API Cache
  - request goes to **Production Stage**
  - from there forwarded to service backend endpoint

---

## API Gateway - Throttling

API Gateway has a limit on requests per second

- steady-state request rate limit - 10,000 request / second
- Maximum concurrent requests - 5,000 requests
  - across all APIs in account

Going over limit
- API responds with
  - `429 Too Many Requests` error

After hitting rate limit
- client can resubmit request at a rate limit
- delay before sending next request
- comply with API Gateway rate limit

---

## API Gateway - Usage Plans and API Keys

Can set different usage plans for our API
- each has a different rate limits
- Can assign different users to different usage plans

API Keys
- Clients are then given an API key to access the API
- used to distinguish their plan with the API

Different usage plan users sent to different **Production Stage** and different set of backend endpoints

---

## Exercise - Simple REST API

Create basic hello world API Gateway

Create REST API
- In API Gateway console
- build REST API
- use example API
- endpoint type - regional

Explore API Gateway interface
- note routes and methods listed in dropdown style
- Each route can be configured for
  - integration
  - the backend endpoint / service it is connected to
- can
  - create routes
  - add methods

Deploy API
- Actions > deploy
- deployment stage - new stage
- name - prod

Visit API
- note the invoke URL
- paste in browser
- test /pets/1
- test /pets/2

Explore tabs in production stage console
-settings
  - can
    - enable API cache
    - enable encryption
    - change throttling settings
    - connect to WAF
    - enable certificate

- logs
  - can enable CloudWatch
- left hand side
  - can
    - enable usage plans / tiers
    - configure API keys for clients

Practice creating a Usage plan

Clean Up
Actions > Delete

---

## Web App with HTTP API

### What we will Build
- An API Gateway
- API Gateway configured to listen on particular routes
- Each route hooked to a Lambda function
- Lambda functions perform actions on a DynamoDB table
- Build a front end static website and store in S3

### Steps
[Provided instructions in API-Gateway folder]

---
