# Security in the Cloud

## Cheat Sheets

#### IAM
[https://digitalcloud.training/aws-iam/](https://digitalcloud.training/aws-iam/)

#### Cognito
[https://digitalcloud.training/amazon-cognito/](https://digitalcloud.training/amazon-cognito/)

#### Directory Services
[https://digitalcloud.training/aws-directory-services/](https://digitalcloud.training/aws-directory-services/)

#### KMS
[https://digitalcloud.training/aws-kms/](https://digitalcloud.training/aws-kms/)


#### CloudHSM
[https://digitalcloud.training/aws-cloudhsm/](https://digitalcloud.training/aws-cloudhsm/)

#### WAF and Shield
[https://digitalcloud.training/aws-waf-shield/](https://digitalcloud.training/aws-waf-shield/)

---

## AWS Directory Service

- A database of user accounts and credentials
  - used for authorization / authentication

_
- Can create directory in AWS
- or connect existing directory

---

## AWS Managed Microsoft AD (Active Directory)

Can deploy a managed instance of Microsoft AD in AWS
- run on windows servers

Can be used to authenticate to AWS services

Can be integrated with Azure AD
- to synchronize users

Can be integreated with Microsoft AD hosted on-premise
- via VPN

Often best choice if
  - you have more than 5000 users
  - need a trust relationship set up

Can set up trust relationships with on-premises ADs

Can be as a standalone AD in AWS

---

## AD Connector

- Used to connect an on-premise AD to AWS
- Allows for using AD to sign in to
  - AWS applications
  - AWS management console


How it works
- AD connector maps AD credentials to IAM roles
- redirect directory requests to on-premise AD
- Requires VPN or direct connect

Use case
- Want to use existing AD with AWS services

---

## Simple AD

Use Case
- less than 5000 users
- don't need advanced features

---

## Identify Federation

Having trust relationships between multiple parties

Can be used by on-premise clients to access AWS services

[See Slide for explaination]

---

## IAM Federation

[Come back to this]

---

## Single Sign-on

- Can be confifured to protect access to various applications behind a single sign-on

- Many SaaS application have built in support for SSO
  - Slack
  - Dropbox
  - Adobe
  - Zoom

AWS Single Sign-on can integrate with
- Active Directory
- Azure AD

---

## Amazon Cognito

For adding sign in / sign up functionality
- to web / mobile applications

#### Cognito User Pools
- where identities are stored
- or where we federate to an identity source

Steps
- Client / Mobile app logs in
- request sent to Cognito User Pool
- Cognito User pool athenticates the user
  - can authenticate with credentials stored locally
  - or with credentials pulled from 3rd party identity provider
    - example, OAuth, facebook, etc...
  - sends back a JWT
- Client / Mobile app sends JWT with request to API Gateway in AWS
- Lambda function picks up JWT for authorization
- If authorized request is send to API gateway to access services

---

#### Cognito Identity Pool

Used for optaining temporary credentials from AWS STS service and granting them to a client

Steps
- Cognito pool authenticates user with Cognito User Pool
- Once authenticated, Cognito uses AWS STS to be granted IAM role
- Role can be used by the client to access AWS services

[See slides for different configurations]

---

## Encryption Primer

Different types if encryption

#### Encryption In Transit
Example
- Using SSL/TLS to encrypt HTTP requests and responses as they traverse internet

#### Encryption at Rest
- Data is encrypted before it is stored
- done using an encryption key
- Data unencrypted before it is retrieved from store

#### Asymmetric Encryption / Public Key Cryptography
- Messages are encrypted with the public key
- Those messages can only be decrypted with the private key
- Examples
  - SSL/TLS
  - SSH

#### Symmetric Encryption
- Uses only one encryption key
- The data encryption key is used to
  - encrypt document
  - decrypt the document

---

## AWS KMS - Key Management Service

Used for creation an management of encryption keys

### CMK - Customer Master Key
- contains the material for encrypting / decrypting data
- Can only encrypt data up to 4KB
- Can be used to generate
  - DEK - Data Encryption Key
  - DEK can be used to encrypt larger amounts of data
- Symmetric CMKs and private keys of asymmetric CMKs
  - never leave KMS unencrypted

#### CMK Creation
- Created in AWS KMS
- can also import your own key material

#### Managed CMKs
- created, managed, used on your behalf
  - by AWS service integrated with KMS
  - for example, S3
- Cannot be rotated

Customer Managed CMK
- managed by you
- optional rotated every 365 days

AWS managed CMK
- AWS manages
- required rotated every 1095 days

#### Data Encryption Keys
- used for encrypting
  - large amounts of data
  - other data encryption keys
- CMKs are used to generate, encrypt, and decrypt data keys

- AWS does not store or use data keys
- you must use and manage data keys outside KMS

---

## Exercise - Create Encryption Key

in KMS console
- explore console
- keys for services that we enabled KMS keys for

Create a key
- symmetric
- single region
- give alias
- can give permission to
  - user that can administer the key
  - user that can use the key
- choose my account as key administrator
- allow key administrator to delete the key
- inspect the policy

Delete key
- select key
- schedule key deletion

---

## CloudHSM

Creating and managing encryption keys

different than KMS
- single tenant
  - dedicated hardware for storing keys
- you have full control over encryption keys
  - AWS has no visibility of encryption keys

Use Cases
- offload SSL/TLS processing from web server
- protecting private keys for your in-house Certificate Authority
- can be used as a key store for KMS
  - CloundHSM stores the keys
  - KMS integreates keys to AWS services

Broad 3rd party support
- does not have broad AWS service support

---

## ACM - AWS Certificate Manager

Used for issuing SSL/TLS certificates for encryption in transit

Integrates with
- Load Balancer
- CloudFront
- Elastic Beanstalk
- Nitro Enclaves

Certificates are signed by the AWS certificate authority

Can also create a private Certificate Authority with ACM
- can then issue private certificates

Can import certificates from 3rd party issuers

---

## Exercise - SSL/TLS Certificte in ACM

In ACM console

Explore options requesting a certificate

Explore options for importing a certificate

Explore options for creating a Private CA

---

## AWS WAF - Web Application Firewall

- Can create rules to filter web traffic
- filter by
  - IP address
  - HTTP headers
  - request body
  - custom URI

[Common exam question]
Can bock common web exploits
- SQL Injection
- Cross Site Scripting

WAF can be placed in front of
- Load Balancer
- CloudFront
- API Gateway
- AWS AppSync

#### Web ACLs - Access Control Lists
- protect a set of resources
- ACL contains rules

#### ACL Rule
- each rule
  - defines the inspection criteria
  - defines action to take if criteria is met

#### Rule Groups
- rules can be stored and applied in a rule group

#### IP Sets
A set of IP addresses and IP ranges to be used in a Rule statement

#### Regex Pattern Set
A collection of regular expressions to use in a rule statement

#### Rule Action
- determintes what WAF will do when it matches a rule criteria
- Can Count
  - will count the request in a tally
- Can Allow
  - forwards request to application
- Can Block
  - the request
  - AWS resource responds with 403 - Forbidden status code

#### Match Statement
- Compare the request or its origin against a condition
- can match for
  - geographic match
  - IP set
  - regex pattern set
  - size constraints [look more into]
  - SQLi Attack - check for malicious SQL code in components of the request
  - String match a component of the request
  - XSS attck - inspect for cross-site scripting attack in components of the request

---

## AWS Shield

A managed service for protecting against DDoS attacks

also helps to minimize
- downtime
- latency

#### DDoS Attack
- Distributed Denial of Service Attacks
- an attempt to bring down a server by overloading it with requests
- sending malformed data
- using automated bots to send requests

#### Service Tiers
Standard
- no cost
- included with CloudFront by default

Advanced
- $3k per month
- 1 year commitment
- comes with additional features

---

## Exercise - AWS Shield

in WAF console
- select AWS Shield
- getting started
- **do not subscribe!**
  - very expensive
- Inspect features available with advanced

---

## Amazon Inspector

Run assessments on an EC2 instance
- checks for security exposures
- volnerabilities

Can configure to run on a schedule

- price based on number of instance assessments

#### Network Assessments
- Does not require agent to be installed on EC2
  - checks which ports on EC2 are available from outside VPC

- if Agent is installed
  - can check which processes are reachable on each port


#### Host Assessments
- Requires agent to be installed
- assessments
  - Vulnerable software (CVE)
  - host hardening (CIS)
  - security best practices

---

## Amazon Macie

Fully managed data security and data privacy service

- uses machine learning to discover and help you protect sensitive data **on S3**

- used for
  - security compliance
  - preventive security

identifies
  - PII
  - Protective Health Information (PHI)
  - API keys
  - secret keys
  - regulatory documents

identifies
- changes to policy and ACLs

monitor security settings of S3

Can send logs to message to
- EventBridge
- CloudWatch Events

Can manage multiple accounts

---

## GaurdDuty

Intelligent threat detection service

- detects
  - account compromise
  - instance compromise
  - S3 compromise
  - milicious reconnaissance
    - attacker attempting to gain information

Monitors events across
- CloudTrail
  - Management events
  - S3 data events
- VPC flow logs
- DNS logs

---

## Defense In-Depth

Implementing Security at multiple layers within applicaiton

[See Slide]

#### Add SSL/TLS connections between
- clients and CloudFront
- CloudFront and Load Balancer
- done by
  - add SSL certificate to
    - CloudFront
    - Load Balancer
  - with Certificate Manager

#### WAF
- Add WAF - Web Applicaiton Firewall to CloudFront distribution
- protect against common Attacks
  - SQL injection
  - Cross Site Scripting
- Allow for fine grained blocking of traffic

#### AWS Shield
- Add Shield to CloudFront distribution
- Protect against DDoS attacks

#### Security Groups
- Add SG to the Load balancer
- Add SG to EC2 instances
- Lock down flow of traffic to
  - CloudFront --> ALB --> EC2

#### Network ACLs
- Attached to subnets

#### Logging
- Access logs for ALB and CloudFront
  - stored in S3

#### AWS Config
- Ensure S3 is encrypted
- Ensure ALB conforms to standards

#### Amazon Inspector
- monitor EC2 instances
- performs assessments for 
  - network
  - host assessments
  - security exposure
  - volnerabilities
- can run on a schedule
- agent must be installed for host and some network assessments
- some assessments don't require agent

#### KMS
Generate KMS keys to encrypt EBS volumes for EC2 instances

#### SNS Notifications
- Create SNS topic to trigger when alarm triggered to
  - Inspector
  - AWS Config
  - WAF
  - Shield

---
