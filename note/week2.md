# 클라우드 컴퓨팅과 AWS의 개요 (Chapter 1)

## AWS 클라우드 컴퓨팅의 장점
1. 민첩성 확보
2. 용량에 대한 걱정 불식 (auto scaling)
3. 대규모 자본적 비용에서 소규모 유동 비용으로 전환
4. 규모의 경제 달성
5. 데이터 센터 구축 비용 절약
6. 기술 혁신성의 유지
7. 단기간 내 세계화 가능

## 글로벌 인프라
- Region
- Availability Zones (AV)
- Local Zones
- Points Of Presence (POP) AKA - Edge Location
  - Regional Edge Cache Locations

## AWS 주요 제품/서비스

### Compute
- Amazon Elastic Compute Cloud (EC2)
- Amazon EC2 Auto Scaling
  - Elastic Load Balancer (ELB)
- AWS Lambda
  - e.g. event trigger for `S3 upload`, `DyanamoDB update`, `Kinesis streaming`, `API gateway requests`
- Amazon EC2 Container Service (ECS)
- Amazon Elastic Kubernetes Service (EKS)
- AWS Fargate
- AWS Elastic Beanstalk
- Amazon Lightsail
- AWS Batch
- AWS Outposts

### Network
- Amazon Virtual Private Cloud (VPC)
- Amazon Route 53
- Elastic Load Balancing (ELB)
- AWS Direct Connect
- AWS App Mesh
- AWS Global Accelerator

### Security
- AWS Identity and Access Management (IAM)
- Amazon Inspector
- AWS Certificate Manager (ACM)
- AWS Directory Service
- AWS Web Application Firewall (WAF)
  - AWS Firewall Manager for Organization  
- AWS Shield
- Amazon GuardDuty
- Amazon Macie
- AWS Secrets Manager
- AWS SSO
- AWS CloudHSM
- AWS Key Management Service (KMS)

### Storage & Contents Delivery
- Amazon Simple Shared Storage (S3)
- Amazon Glacier
- Amazon Elastic Block Store (EBS)
- Amazon Elastic File System (EFS)
- AWS Storage Gateway
- Import/Export Options
  - AWS Snowball
  - AWS Direct Connect
  - Amazon Kinesis Firehose
- Amazon CloudFront

### Database
- Amazon Relational Database Service (RDS)
- Amazon DynamoDB
- Amazon Redshift
- Amazon ElasticCache
- Amazon Aurora
- Amazon Neptune
- Amazon QLDB
- Amazon DocumentDB
- Amazon Keyspaces

### Data Analysis
- Amazon Athena
- Amazon EMR
- Amazon Elasticsearch
- Amazon CloudSearch
- AWS Data Pipeline
- Amazon Kinesis
- AWS Glue
- Amazon MSK
- AWS Lake Formation
- Amazon QuickSight

### Application Service
- Amazon API Gateway
- AWS Step Functions
- Amazon Simple Workflow Service
- Amazon Elastic Transcoder

### Development Tools
- AWS CodeCommit
- AWS CodePipeline
- AWS CodeBuild
- AWS CodeDeploy

### Management Tools
- AWS CloudFormation
- AWS Service Catalog
- AWS OpsWorks
- Amazon CloudWatch
- AWS Config
- AWS CloudTrail

### Messaging
- Amazon Simple Notification Service (SNS)
- Amazon Simple Email Service (SES)
- Amazon Simple Queue Service (SQS)

### Migration
- AWS Application Discovery Service
- AWS Database Migration Service
- AWS Snowball
- AWS Server Migration Service

### Artificial Intelligence
- Amazon Lex
- Amazon Polly
- Amazon Rekognition
- Amazon SageMaker

### Internet of Things
- AWS IoT Platform
- AWS Greengrass
- AWS IoT Button

### Mobile Service
- Amazon Cognito
- AWS Mobile Hub
- AWS Device Farm
- Amazon Mobile Analytics
