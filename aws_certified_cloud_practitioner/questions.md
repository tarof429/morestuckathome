# Questions

- Which IAM entity can be used to delegate permissions: User, Group, Role or Policy? Answer: Role.

- How can you get metadata for an EC2 instance? Answer: At the command prompt in the instance, type `curl http://169.254.169.254/latest/meta-data/local-ipv4` for example.

- You want to access an s3 bucket from your EC2 instance in a secure fashion. Should you use access keys or roles? Answer: Don't use access keys, as they are stored in plain text on the EC2 instance. Instead, create an IAM role with S3 read only permissions and attach it to the EC2 instance. You do this by first creating the role under IAM, selecting the EC2 instance, going to Security | Modify IAM role, and selecting the role

- Can you move an EBS volume from one availability zone to another? Answer: No, an BES volume is created in an availability zone that can't be changed later.

- Where are EBS snapshots created: a region or an availability zone? Answer: a region because it's created in S3.

- Which storage device is physically attached to an EC2 instance: EBS or instance store volume? Answer: Instance store volume (think SSD). Data on an instance store is lost when the instance is powered off. Note: only a few intance types support an instance store volume as the root device.

- How does the Elastic Load Balancer (ELB) assist with fault tolerance? Answer: By distributing connections (think traffic) to multiple back-end instances. 

- Can the Elastic Load Balancer distribute connections accross regions? Answer: no, only across availability zones.

- Which services does Amazon Route 53 provide: Health checking, DNS, IP routing, domain registration, or content distribution? Answer: health checking, DNS, and domain registration. Note that Route 53 provides several types of health checking.

- Which type of firewall operates at the instance level? Answer: a security group.

- Which type of public IP address is retained even when the instance is stopped? Answer: An elastic IP address.

- You are trying to access a Cloudfront distribution but cannot see the web site. What is a possible cause? Answer: Make sure the default page is set to index.html.

- What service has built-in DoS proection: Route 53, Internet Gateway, Cloudfront or AWS Direct Connect? Answer: Cloudfront is a CDN which provides built-in DoS protection. Among these, AWS Direct Connect provides a way to connect on-prem machines to AWS and does not provide DoS protection.

- What types of Origin does Amazon CloudFront support? Answer: S3 bucket and EC2 instance

- In Amazon Route 53, what is the name for the configuration item that holds a collection of records belonging to a domain: DNS record, Alias or Routing Policy? Answer: Hosted zone

- Which of the following services combined allow for continuous integration: CodeCommit, CodeBuild, CodeDeploy? CodeCommit and CodeBuild

- RDS is an example of what type of database: Online transaction processing (OLTP), Online analytics processing (OLAP), NO-SQL, or Data warehouse? Answer: OLTP

- What AWS service provides OLAP (data warehousing)? Answer: Amazon Redshift

- Which AWS database service offers seamless horizontal scaling: Amazon RDS, Amazon RedShift, Amazon DynamoDB, or Database on Amazon EC2? Answer: Amazon DynamoDB

- How can fault tolerance (failover) be added to an Amazon RDS database: By using read replicas, using multi-AZ, using global replicas or using EBS snapshots? Answer: By using Multi-AZ

- If you want to implement serverless CRON jobs in AWS, would you use Lambda or S3? Answer: Lambda.

- How can an organization enable microsecond latency for a DynamoDB database: Use Amazon ElastiCache, Using DynamoDB Auto Scaling, using read replicas, or using DynamoDB Accelerator (DAX)? Answer: DAX

- What is Amazon Aurora? Answer: a MySQL and PostgreSQL-compatabile relational database.

- Which AWS database service is a relational data warehouse: Amazon RedShift, Amazon RDS Aurora, Amazon DynamoDB, or Amazon ElastiCache? Answer: Redshift

- Why might an organization decide to move an on-premises database to Amazon RDS? Answer: to reduce operational overhead.

- How do you increase the capacity of an Amazon RDS database? Answer: changing to a larger instance type

- Which service can be used to analyze data on Amazon S3 using serverless SQL queries? Amazon Athena, which is a serverless big data analysis tool.

- You have a lot of logs that you want to perform a query on. If you want to use AWS Athena, where should you put your logs? Answer: Put it in an S3 bucket.

- What is Amazon Kinesis? Answer: A service for analyzing video and data streams in real time.

- If you have lots of video data that you want to process in real-time, what would you use: Amazon Kinesis Streams or Firehose? Answer: It depends. Both are used to do the collection and processing of data in real-time. Streeams is the more customizable option and requires that scaling and provision is done manually. Firehose stores the data in other services such as S3, ElasticSearch, etc.

- A company *moves* data between Amazon S3, RDS, DynamoDB and EMR. Which service can help to process and move data between services? Answer: Amazon Data Pipeline

- A company needs an interactive dashboard for business intelligence. What should they use? Amazon QuickSight

- A company wants to improve their RDS performance and decrease latency by using an AWS DB caching service. Which of the following would you recommend they use? Answer: ElastiCache, which provides in-memory caching (it is essentially Redis)

- A company needs to run ETL jobs on data stored in S3. They are looking for a simple, scalable, and serverless data integration platform. Which service should they use? Answer: AWS Glue

- A company is looking for a fully managed graph database service on AWS. What do you recommend? Answer: Amazon Neptune

- AWS Organizations: You use AWS Organizations to manage multiple AWS accounts. You can group accounts into Organizational Units (OUs) like Development and Production. Each organization can be associated with Service Control Policies (SCPs). 

- Control Tower: A service to set up and govern a secure, compliant multi-account environment. It does this by simplifying the process of creating multi-account environments, setting up a landing zone for each user, and uses AWS Organizations to help with ongoing account account management and governance. Control Tower does not manage AWS resources such as EC2, S3, and RDS. That's the purpose of Systems Manager.

- Systems Manager is a service to gain operation insight and take action on AWS resources such as S3, EC2, etc. It features something called Session Manager, for example, to securely manage remote resources without using SSH. Systems Manager does not manage the Service Catalog.

- AWS Trusted advisor does NOT provide advice on which of the following: Cost optimization, Performance, TCO, Security, Fault Tolerance. Answer: TCO.

- A company wishes to determine if there will be any AWS maintenance that could affect their systems over the next few days. Which service should they check: AWS Service health dashboard, AWS personal health dashboard, AWS trusted advisor. Answer: Personal health dashboard. The personal health dashboard provides a personalized view of events that could affect your systems. Service health on the other hand is about services.

- A company wishes to restrict the applications users can launch to an approved list. Which service should they use: AWS Service Catalog, AWS Systems Manager, AWS OpsWorks. Answer: AWS service catalog. This service can be used to provide an approved catalog of services and applications that users can launch.

- Which of the following is true about AWS Organizations? Using AWS Organizations you can: Programmatically creating new AWS accounts. AWS Organzations manages user accounts and this provides APIs as well as CLI access.

- In regards to AWS Control Tower, the following is NOT true: AWS Control Tower does NOT manage  AWS resources such as EC2, S3, and RDS. Control Tower is for user accounts. AWS Systems Manager does.

- IAM authentication can be federated using either SAML (LDAP) or OIDC like Google. Another option is to use AWS SSO. Cognito can also be used to gain temporary access to AWS. In this case, users are defined in a user pool, and identity pools are used to obtain temporary, limited-privilege credentials. 

- Which tool can be used to find compliance information that relates to the AWS Cloud platform? Answer: AWS Artifact

- What is AWS' policy regarding penetration testing? Answer: You can now perform penetration testing against several services without approval

- Which service can assist with protecting against common web-based exploits? Answer: AWS WAF

- In case of account compromise, which of the following actions should you perform? Answer: Open a support case with AWS

- Which service uses a hardware security module to protect encryption keys in the cloud? Answer: AWS CloudHSM

- Which service can be used to find reports on Payment Card Industry (PCI) compliance of the AWS cloud? Answer: Artifact

- Which service can be used to record information about API activity in your AWS account? Answer: Cloudtrail

- Does Amazon CloudTrail permanently record all API activity in your account by default? Answer: no

- Which architectural benefit of the AWS Cloud assists with lowering operational cost? Answer: Higher-level managed services (like S3, RDS, Athena)

- Which of the following is NOT a limitation of scaling vertically? Answer: Requires a load balancer for distributing load

- Which services can scale horizontally? Answer: DynamoDB, EC2 Auto Scaling, S3

- Which pricing model is best suited for a batch computing workload that requires significant compute power and can be stopped at any time? Answer: Spot instances

- What is the most cost-effective storage tier for data that is not often accessed, will be retained for 7 years, and needs to be retrievable within 24 hours? Answer: Amazon S3 Glacier Deep Archive (need to memorize the different glaciers)

- Which storage classes are available for the Amazon Elastic File System? Answer: Standard, Infrequent Access Storage (I don't know what storage classes are available)

- What is the best tool for an organization to compare the cost of running on-premises to using the AWS Cloud? Answer: TCO Calculator (there is such a tool?)

- Which AWS support plan comes with a Technical Account Manager (TAM) Answer: Enterprise

- Which of the following needs to be considered in a Total Cost of Ownership (TCO) analysis? Answer: Data center operations costs

- With Amazon S3, which of the following are NOT chargeable items? Answer: Inbound data transfer

- Which AWS pricing feature can be used to take advantage of volume pricing discounts across multiple accounts? Answer: Consolidated billing

- Which tool can an IT manager use to forecast costs over the next 3 months? Answer: AWS Cost Explorer

- Which service can be used to migrate an on-premises database to Amazon RDS? Answer: AWS Database Migration Service

- Which service can be used to migrate exabytes of data into the AWS Cloud? Answer: AWS Snowmobile

- Which service can be used to quickly and affordably migrate 50TB of data to Amazon S3 for a company with a slow Internet connection? Answer: AWS Snowball

- How can a company migrate a database from Amazon EC2 to RDS without downtime? Answer: Migrate using AWS Database Migration Service

- A company needs to migrate several TB of data from an on-premises NAS device to Amazon FSx. Which service can the company use to migrate the data over a VPN connection? Answer: AWS DataSync

- A company recorded some support call conversations in mp4 files. How can the company extract the audio into a text document? Answer: AWS Transcribe

- An application is being built that needs to identify faces in images. Which service can be used? Answer: AWS Rekognition

- A company is looking to use machine learning to translate a few documents from English to Chinese. Having learned about machine learning services on AWS, which of the following services would you recommend the company to use? Answer: AWS Translate

- A company is planning on implementing a chatbot into their web application. Which AWS service would best meet their needs? Answer: Amazon Lex

- A company wants to develop their own machine learning application on AWS. Which of the following services would best fit their needs? Answer: Amazon SageMaker

- Which of the following services offers Text-to-Speech (TTS) services? Answer: Amazon Polly

- A company is collecting weather data to send to AWS for analytics. Each collection cycle the data doesn???t exceed 7 TB. Which AWS Snow Family product would you recommend? Answer: Amazon Snowcone

- True or false: If you want to deploy containers in AWS, usng serverless solutions like ECS with Fargate is always the best solution. Answer: Using fargate is a good solution if you don't want to manage things like security that's needed with EC2 instances. However, EC2 may be a better solution if you need certain faeatures that is only available with an instance such as machine learning. Also Fargate does not allow you to customize the networking layer.

- If you want to deploy containers but don't want to manage servers, and you need certain advanced features such as machine learning, is there any reason not to use EKS? Answer: Using EKS requires expert knowledge of Kubernetes. It can also cost a lot more.

- If you have files that you need to store in S3 and you need to have millisecond retrieval times, but don't expect to retrieve them often, what storage class should you use and why? Answer: Use S3 Standard-IA. It is cheaper than S3 Standard, provides millisecond access, and is stored in multiple availibility zones, so it is useful for storing backups. The minimum storage duration is 30 days.

- If you really want to save money storing backups in S3, and you can tolerate retrieval times ranging from minutes to hours (but not days), what storage class should you use? Answer: S3 Glacier.

- What is the difference between AWS Storage Gateway and AWS DataSync? Answer: Storage Gateway is meant for data integration. DataSync, on other hand, is meant for data migration. Storage Gateway uses your on-prem data and moves it to AWS. 

- What is Lightsail? Answer: It's an easy-to-use virtual private server (VPS) provider that makes it easy to build an application or website. 

- What is AWS Cost Explorer? It is a tool to visualize, understand, and manage your AWS costs and usage over time.