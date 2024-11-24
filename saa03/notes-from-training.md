- WAF guide
    - design principles
    - best practices
    - [example](https://docs.aws.amazon.com/wellarchitected/latest/operational-excellence-pillar/design-for-operations.html)
    - [and this](https://docs.aws.amazon.com/wellarchitected/latest/operational-excellence-pillar/ops_dev_integ_build_mgmt_sys.html)
- Product guide
    - Check FAQ per product, given in AWS exam guide list of services: what, key features and use cases
    - try to search for: <aws product> application design guide + site:docs.aws.amazon.com
    - try to search for: <aws product> best practices + site:docs.aws.amazon.com
- certification guide: check list of services mentioned at the end of it    

## resiliency

- RTO: acceptable time loss
- RPO: acceptable data loss
- DR strategy
- pillars of resilient workload
    - IaaC
    - App Arch
    - Data movement

## high availability

### design patterns
    
1. ELB to route traffic across AZs
2. HA DB design pattern
    - read replica can be promoted to master
3. Floating IP address pattern
    - elastic IP address can be registered to route-53 and attached to another instance
4. floating interface pattern
    - ENI(ethernet card) can be used to assign private ip
    - ENI can be attached to an instance within same subnet
    - a subnet is within an AZ
5. __State sharing__: sticky session problem
    - move state off your server into a KV store
    - use elastic cache and dynamodb for caching
    - use key as ID/session-id and value as user info
    - think of login info sharing, instead of a cookie in browser, we use a elastic cache
6. scheduled scale out: handle __traffic spike__
    - ASG
    - scheduled or scale by recurrence policy
    - pre-warm your server and keep your servers ready
7. job observer pattern: handle __back pressure__
    - creates competeting consumers using ASG, _depending on queue depth metric_(check how to setup queue depth metric in ASG)

### references    
- [3 patterns](https://bluexp.netapp.com/blog/understanding-aws-high-availability-compute-sql-and-storage)
- [aws article](https://docs.aws.amazon.com/whitepapers/latest/real-time-communication-on-aws/high-availability-and-scalability-on-aws.html)
- [CDP](https://en.clouddesignpattern.org/index.php/Main_Page.html)
- [aws patterns guode](https://docs.aws.amazon.com/pdfs/prescriptive-guidance/latest/cloud-design-patterns/cloud-design-patterns.pdf)

# Security

check your security score in aws security hub
cloudtrail - account wide audit logs(audit trail)
cloudwatch - resource monitoring
secrets manager - key-vault

## IAM

[how IAM works](https://docs.aws.amazon.com/IAM/latest/UserGuide/intro-structure.html)

### STS

AWS Security Token Service(STS) is a web service that enables you to request temporary, limited privilege credentials for IAM Users or Federated Users.STS is the service that is a building block of many identity processes within AWS. If you have used the IAM role, then you already use the service provided by STS without necessarily being aware of it.

- STS generates temporary IAM credentials whenever sts:AssumeRole* operation is used. Whenever you assume an IAM role, you use the sts:AssumeRole* call, and in doing that, you gain access to IAM temporary credentials, which can be used by the identity which assumes the role.


*[check this](https://devopslearning.medium.com/security-token-service-sts-c8d12a04010d#:~:text=STS%20uses%20the%20permission%20policy,identity%20who%20assumes%20the%20role.)* and *[this](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html)*

### access keys
Access keys are long-term credentials for an IAM user or the AWS account root user. You can use access keys to sign programmatic requests to the AWS CLI or AWS API (directly or using the AWS SDK). For more information, see Signing AWS API requests.

use __aws configure__ in cli to use access keys

### identity vs principal
In simpler terms, a principal is a specific type of entity that can take actions in AWS, while an identity is the unique identifier associated with that principal. The principal is defined in IAM policies to grant or deny access, and the identity is used for authentication and authorization purposes.

For example, an IAM user named “Shristi” is a principal, and her IAM username shristi@example.com is her identity. 

*[check this](https://medium.com/@reach2shristi.81/aws-principal-vs-identity-3d8eacc5377f#:~:text=The%20principal%20is%20defined%20in,example.com%20is%20her%20identity.)*

## These services are important

- secrets manager
- inspector
- trusted advisor
- Guard Duty

### Secrets Manager

The best overall AWS Secrets Manager alternative is HashiCorp Vault.
![Alt text](image.png)

#### difference with Hashicorp Vault

AWS Secrets Manager: Provides a managed service for storing, managing, and retrieving secrets. It automates the rotation of secrets and integrates tightly with other AWS services, making it easier to use within the AWS ecosystem. HashiCorp Vault: Provides a centralized place to store and access secrets.

#### difference with AWS KMS

AWS secret-manager uses KMS to access the encryption keys before storing the secrets in it's vault

### Guard Duty

Threat detection service. It checks on below for anamolus behavior:
- VPC flow logs
- Cloudtrail logs
- Route53

### Amazon Inspector

Vulnerability management solution.
![Alt text](image-1.png)

### Trusted Advisor

recommendation engine for helping follow AWS best practices, on 6 pillars

### AWS DDoS protection

- AWS WAF (layer 7 protection only)
- AWS Cloudfront
- always keep your web layer in pvt subnet, and receive your traffic through ELB(in pub subnet) to your EC2
    - WAF --> cloudfront --> ELB --> EC2

#### AWS Shield

Layer 3 and 4 protection.

Managed DDoS protection. Two flavors:
- standard shield (default)
- advanced protection shield (paid/ WaF comes with it)

## Encryption

### encryption choices (check infy slides)

- data at rest: KMS (AWS managed keys, AWS CloudHSM, use own key)
- data at transit: EBS encryption, S3 object lock, KMS
    - AWS certificate manager (ACM)

# Network

## VPC

- SDN(enclave)
- regional construct
- __for prod workload never use default VPC__
- IP CIDR
    - class A n/w vs class B n/w vs class C n/w
- Transit GW: Peering VPC Connection
- Gateway endpoints
- ??? (check infy slides)

### VPC Endpoint

### subnet (micro segment)
- pub subnet (DMZ) will have a route (using route table) to internet g/w, so it is accessible from i/n for opened port in subnet
- pvt subnet (DBLAN) will have a route (using route table) to only within vpc and can interact with public subnets over internal IP in VPC

### Elastic IP

### internet gateway

### NAT Gateway (???)

## Route 53

- located at PoP
- DNS service for AWS
- [FAQ](https://aws.amazon.com/route53/faqs/)
- what, key features and use cases

![Alt text](image-2.png)

## Cloudfront

- located at PoP
- CDN
- at Edge location
- [FAQ](https://aws.amazon.com/cloudfront/faqs/?nc=sn&loc=5&dn=2)
- what, key features and use cases

## Global Accelerator

- [FAQ](https://aws.amazon.com/global-accelerator/faqs/)
- what, key features and use cases


## *IMP:* External Connectivity Options (check infy slides)

# High Performance Architecture

- Performance efficiency
- Multi region
- embrace advanced tech/ buy over build
- no need to manage the infra, if not needed
- cloud first
- SELECTION -MONITORING      -REVIEW cycle
- (lamda-vs-EC2)       -(Cloud Trail)   -(Trusted Advisor)

## Compute

- serverless
- containerization: any service where containerization is possible, choose containers, cloud agnostic
- ECS
- EC2: legacy
- Lambda: Batch activity < 15 mins, stateless, auto scalability
- AWS Batch: HPC
- Elastic Beanstalk: 
    - PaaS service to fastest way of deployment
    - similar to Azure App Service
    - testing of app workload


### ECS

- aws fargate (cheaper): serverless compute for containers
- EC2 cluster
- choose ec2 cluster over fargate for ECS when u need more finer grain control
- choose eks over ecs, if u want cloud agnostic solution or doing a migration from on-prem k8s to aws

## Storage
*look for practical use cases in internet*

- Block(EBS): file storage, App Server, Web Server, Big Data, DB, NOSQL 
- File(EFS, FSx, Lustre): run workload on top of Linux server, sharable/mountable sharable, Hybrid (Linux annd Windows(SMB)), HPC for single Az(Lustre), Netapps migration, Zfs
- Object: 
    - S3 (standard, IA, One Zone IA): bucket, WORM model
    - S3 Glacier(Instant{patient medical record}, Flexible{video footage w/ SLA}, Deep Archive{compliance data})
- Storage GW (Appliance): VMWare on-prem, move several TB of data, __???uses???__
- Snow Family device: Data migration

### S3
- dont directly open ur bucket for public access
- bucket versioning
- lifecycle rules
- amazon eventbridge

### EBS and Instance store
- EBS vol type (gp3 vs io2 etc)
- Amazon EBS multi-attach
- delete on termination
- ebs vol can be attached in only 1 AZ
- AWS instance store: acts like physical HDD, mountable, ephemeral
- ebs vs instance store from PERF PoV
- snapshot: 
    - snapshot of ebs to replicate across region
    - recyclable via retention rule

### EFS
- EFS vs EBS
- EFS vs FSx
- n/w FS for Linux EC2 instances can share via EFS

## Database
*look for the product cards for use case example inspirations*

### RDBMS

#### RDS (OLTP)
- Amazon RDS custom

#### Aurora (OLTP)
- aurora vs mysql
- aurora vs postgresql
- aurora is cheaper and faster and global database(???)

##### Redshift (OLAP): DWH

### Key-Value store: DynamoDB
### In-Memory store: Elastic Cache, MemoryDB (for caching)
### Document store: DocumentDb(Amazon's MongoDb)
### Graph Db: Neptune(Amazon's neo4j)
### Wide col store: Keyspaces(Amazon's Cassandra)
### Time Series Data: TimeStream
### Blockchain/ledger: Quantum Ledger


# Reliability

## Decoupling an Application
- [check this](https://docs.aws.amazon.com/wellarchitected/2023-10-03/framework/rel_prevent_interaction_failure_loosely_coupled_system.html)
- stateful design
- app integration svc : SQS, SNS, Step fn
- lambda
- api gateway

## Step Function: lambda function to be executed in a workflow
## API Gateway
- WebSocket API: web-sock conn
- HTTP API
- REST API

## Amazon Elastic Cache


# Operational Excellence

## Design Principles

## Developer Tools

- [devops](https://aws.amazon.com/products/developer-tools/)
    - check aws console with Dev tool category
    - repo/version control (CodeCommit)
    - CI (CodeBuild)
    - deployment (CodeDeploy)
    - CD (CodePipeline)
    - Microservices (ECS, Lambda)

- review
- monitor
- act

### Cloud Formation

- AWS CDK (terraforming using std langs)
- Cloud Formation
- AWS SAM (serverless app model) <span style="color:red">__!!!IMPORTANT__</span>

# Cost

## Billing and Cost Management

- cost explorer

### compute cost

### network cost

### storage cost

## Budget

### Budget alerts