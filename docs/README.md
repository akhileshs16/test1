# AWS

## Services

---

### EC2 - Elastic Compute Cloud

---

Amazon Elastic Compute Cloud (Amazon EC2) provides on-demand, scalable computing capacity in the Amazon Web Services (AWS) Cloud.  
It provides scalable computing power, memory, network, storage.

SSH into EC2 instance from terminal using `ssh -i <authentication-key>.pem ec2-user@<instance public IP>`  
and use `chmod 0400 <authentication-key>.pem` if unprotected keyfile warning occurs.

#### Types of Instances

- General Purpose (M,T series) - great for web servers, code repos etc, balance between compute, memory, networking.
- Compute Optimised (C series) - High performance for machine learning, scientific modelling, gaming servers etc.
- Memory Optimised (R,U,X,z series) - for fast performance and to process large data sets.
- Storage Optimised () - storage intensive tasks such as SQL and NoSQL.  
  [Refer AWS docs](https://docs.aws.amazon.com/ec2/latest/instancetypes/instance-types.html)  
  [Instances Comparison](https://instances.vantage.sh/)

#### EC2 Pricing Models

- On-Demand Instances - pay for compute capacity per hour or per second (only for Linux Instances) and no upfront payments are needed. You can increase or decrease your compute capacity to meet the demands of your application and only pay for the instance you use.
- Spot Instances - Spot Instances are available at up to a 80% discount compared to On-Demand prices. The Spot price of Amazon EC2 spot Instances fluctuates periodically based on supply and demand.
- Reserved Instances - The Reserved Instances are used to reserve instances for an agreed period. The options are for 1-year or 3-years. It can provide as much as 70% in savings.
- Dedicated Hosts - Dedicated Hosts provide you with physical EC2 servers, which are entirely dedicated to your workloads.

#### Security Groups

Security groups are virtual firewalls that control network traffic for EC2 instances. They have two sets of rules that define what traffic can enter and leave the instance:

- Inbound rules: Control the traffic that can come into the instance
- Outbound rules: Control the traffic that can leave the instance

#### EC2 Placment Groups

- Cluster – Packs instances close together inside an Availability Zone. This strategy enables workloads to achieve the low-latency network performance necessary for tightly-coupled node-to-node communication that is typical of high-performance computing (HPC) applications.
- Partition – Spreads your instances across logical partitions such that groups of instances in one partition do not share the underlying hardware with groups of instances in different partitions. This strategy is typically used by large distributed and replicated workloads, such as Hadoop, Cassandra, and Kafka.
- Spread – Strictly places a small group of instances across distinct underlying hardware to reduce correlated failures.

---

Elastic IP - Assigns a fixed public IP to the instance.  
EC2 Hibernate - where the RAM is stored in EBS, and instance is shutdown. And when the instance is up, ram is restored from the EBS thereby reducing boot time.  
Amazon Machine Image (AMI) - is an image that provides the software that is required to set up and boot an Amazon EC2 instance.

### EBS - Elastic Block Store Volume

---

An Amazon EBS volume is a block-level storage device that can be attached to an Amazon EC2 instance and used like a physical hard drive. It allows instances to persist data, even after termination.  
[Formatting and mounting EBS volume to an instance](https://docs.aws.amazon.com/ebs/latest/userguide/ebs-using-volumes.html)

EBS volumes are only available within an AZ and can be transferred between instances within that AZ.
EBS volumes can be transferred from one AZ to another using snapshots.  
EBS volumes can be encrypted.
EBS multi-Attach - Attach same volumes to multiple EC2 instances.  
Instance Store - It provides temporary storage for EC2 instance. The data in an instance store persists during the lifetime of its instance. If an instance reboots, data in the instance store will persist. When the instance hibernates or terminates, you lose any data in the instance store.

### EFS - Elastic File System

---

EFS is a file system that can be accessed by multiple EC2 instances and works with instances in multi-AZ.  
Only compatible with linux based AMI's.  
3x expensive than EBS.

#### EFS Storage Tiers

- Standard - high-speed, low-latency option for regularly accessed or modified data workloads.
- Infrequent Access - low-cost option for infrequently accessed data workloads.
- Archive - Long-term archival data, such as regulatory compliance data or legal documents.

Both Regional(multi-AZ) and One Zone availability possible.  
Implement lifecycle policies to move files between storage tiers.

### ELB - Elastic Load Balancer

---

Elastic Load Balancer automatically distributes your incoming application traffic across all the EC2 instances that you are running. It acts as a single point of contact for all incoming web traffic to EC2 instances. Elastic Load Balancing helps to manage incoming requests by optimally routing traffic so that no one instance is overwhelmed.  
A **Target Group** contains EC2 instances to which a load balancer distributes workload.

#### Health Checks

Load Balancer periodically sends requests to its registered instances to test their status. These tests are called health checks. The status of the instances that are healthy at the time of the health check is InService. The status of any instances that are unhealthy at the time of the health check is OutOfService. The load balancer performs health checks on all registered instances, whether the instance is in a healthy state or an unhealthy state.

#### Types of ELB

- Classic Load Balancer - Not Used.
- Application Load Balancer - Layer 7 load balancer. Routes and load balances at the application layer (HTTP/HTTPS), and supports path-based routing.
- Network Load Balancer - Layer 4 load balancer. NLB can handle millions of queries per second, while retaining extremely low latencies.
- Gateway Load Balancer - Layer 3 load balancer. Distributes traffic to a fleet of appliance instances. Provides scale, availability, and simplicity for third-party virtual appliances, such as firewalls, intrusion detection and prevention systems, and other appliances.

---

**Sticky Sessions** - Sticky sessions are used to ensure that all requests from a client are routed to the same instance.(target groups -> edit attributes)

- application based cookies
- duration based cookies

**Cross-zone load balancer** - Each load balancer node for your Elastic Load Balancer distributes requests evenly across the registered instances in all enabled Availability Zones. If cross-zone load balancing is disabled, each load balancer node distributes requests evenly across the registered instances in its Availability Zone only.(ALB - no charge, NLB,GWLB - charges)(Load Balancer -> Attributes)

Server Name Indication(SNI) - If you use HTTPS (SSL or TLS) for your front-end listener, you must deploy an SSL/TLS certificate on your load balancer. The load balancer uses the certificate to terminate the connection and then decrypt requests from clients before sending them to the instances. When you create a certificate for use with your load balancer, you must specify a domain name. The domain name on the certificate must match the custom domain name record. If they do not match, the traffic will not be encrypted as the TLS connection cannot be verified.

Connection draining(deregistraion delay) - process that ensures that existing, in-progress requests are given time to complete while the instance is deregistering or unhealthy and prevents new requests to de-registering instance.

#### ASG - Auto Scaling Group

Auto Scaling is a feature of AWS that allows you to scale your resources up or down automatically based on the traffic. Auto Scaling is used to ensure that your application is always available and scalable. It can set parameters such as minimum and maximum number of instances and also desired capacity. It can recreate unhealthy instances and also register the instaances to load balancer.  
ASG is free(pay for underlying EC2 instance).  
Use **Launch Template** to set ASG attributes.

### S3 - Simple Storage Service

---

S3 is a highly scalable, high-speed, web-based cloud storage service. It allows people to store files in "Buckets"(directories) and these buckets must have globally unique name. Buckets are only available within a region.  
**Objects**(files) have a **Key** which is the full path to the file and **value** is the content inside the file.  
**Versioning** - allows you to keep multiple versions of an object in the same bucket.  
**Replication** - Replicate buckets within same region or across multiple regions for redundancy,change ownerships etc.

- CRR - Cross Regian Replication - Lower latency access across regions.
- SRR - Same Region Replication - Replication between production and test.

Works only if versioning is enabled.  
Requester Pays - THe requester pays the cost of the request and data download from bucket instead of the bucket owner.

S3 Event Notifications - Use the Amazon S3 Event Notifications feature to receive notifications when certain events happen in the S3 bucket. Set the Event notifications in the S3 bucket properties.
Amazon S3 can send event notification messages to the following destinations. You specify the Amazon Resource Name (ARN) value of these destinations in the notification configuration.

- Amazon Simple Notification Service (Amazon SNS) topics
- Amazon Simple Queue Service (Amazon SQS) queues
- AWS Lambda function
- Amazon EventBridge

**Multi-part upload** - Divides large files to smaller parts and parallelize uploads, thus increasing upload speeds.  
**S3 Transfer Acceleration** - Speeds up data transfer from client to S3 by routing data through neraby edge location.  
**Byte-Range Fetches** - Parallelize GETs by requesting specific byte ranges, thus speeding uploads. Better resilience to failures.  
**S3 Inventory** - It allows to manage the storage.  
**S3 Batch Operations** - Use S3 Batch Operations to perform large-scale batch operations on Amazon S3 objects. S3 Batch Operations can perform a single operation on lists of Amazon S3 objects that you specify. Use S3 inventory and select features to specify a list of objects.  
**S3 Storage Lens** - Use S3 Storage Lens metrics to generate summary insights, such as finding out how much storage you have across your entire organization or which are the fastest-growing buckets and prefixes. You can also use S3 Storage Lens metrics to identify cost-optimization opportunities, implement data-protection and security best practices, and improve the performance of application workloads.

### VPC - Virtual Private Network

---

VPC is a virtual network dedicated to your AWS account. It is logically isolated from other VPC. 5 IP addresses are reserved in VPC. By creating your own VPC, you gain full control over the networking environment, including the ability to define IP address ranges, subnets, routing tables, and connectivity options.  
**Internet Gateway** - Connects your VPC to the internet. The instances in a public subnet can access the internet because the subnet route table contains a route that sends traffic bound for the internet to the internet gateway.  
**Route Tables** - Contains a set of rules, called routes, that determine where network traffic from your subnet or gateway is directed.  
**Bastion Hosts** - A bastion host is a secure, isolated EC2 instance that is used to access a private network.  
**Subnets** - A subnet is a range of IP addresses in your VPC. You can create multiple subnets in a VPC as public and private subnets.  
NAT translates private IP addresses in an internal network to a public IP address before packets are sent to an external network.  
**NAT Gateway** - Enables instances in a private subnet to send outbound traffic to the internet, but prevents resources on the internet from connecting to the instances.  
**NACL** (network access control list) allows or denies specific inbound or outbound traffic at the subnet level.
NACL is stateless(Return traffic must be explicitly allowed by rules) and is used at subnet level whereas Security Groups are stateful (Return traffic is automatically allowed, regardless of any rules) and is used at instance level.  
Ephemeral Ports - Ephemeral ports are a range of ports that are automatically assigned to a connection by the operating system and unallocated when connection closes.  
**VPC Peering** - It is a networking connection between two VPCs that enables you to route traffic between them. Instances in either VPC can communicate with each other as if they are within the same network. You can create a VPC peering connection between your own VPCs, or with a VPC in another AWS account.(VPCs must have non overlapping IP's)  
**VPC Endpoints** - It is a private network connection between your VPC and an amazon service outside your private subnet and these do no trequire public IP to communicate.

- Interface Endpoints - It is supported by many services but is paid.
- Gateway Endpoints - It is supported by S3 and DynamoDB and is free.

**VPC Flow Logs** - VPC Flow Logs captures information about the IP traffic going to and from network interfaces in your VPC.  
**VPC Site-to-Site VPN** - Enable access to your remote network from your VPC by creating an AWS Site-to-Site VPN (Site-to-Site VPN) connection, and configuring routing to pass traffic through the connection. It allows all customer networks connected to VPN to communicate with each other.

- Virtual Private Gateway(VGW) - A virtual private gateway is the VPN endpoint on the Amazon side of your Site-to-Site VPN connection that can be attached to a single VPC.
- Customer Gateway(CGW) - A customer gateway is the VPN endpoint on your side of the VPN.

**Direct Connect(DX)** - Direct Connect is a network service that makes it possible to establish a dedicated network connection from your premises to the VPC. It is unencrypted but private.(new connection takes more than a month)  
Direct Connect Gateway - Direct Connect Gateway is a virtual network interface that enables you to setup s Direct Connect to one or more VPC.  
**Transit Gateway** - Transit Gateway enables customers to connect thousands of VPCs. You can attach all your hybrid connectivity (VPC, VPN and Direct Connect connections) to a single gateway, consolidating and controlling your organization's entire AWS routing configuration in one place.  
![Transit Gateway image](https://osamaoracle.com/wp-content/uploads/2021/08/image-5.png?w=676)

### ECS - Elastic Container Service

---

ECS is a container orchestration service that allows you to run, stop, and manage containers on AWS.

- EC2 Launch Type - You musts provision EC2 instances with ECS agent to run in ECS cluster. AWS will manage starting/stopping of containers.
- Fargate Launch Type - You don't need to provision EC2 instances. Its serverless. You create task definitions and to scale, just increase the number of tasks.

EC2 Instance Profile - is IAM role used by ECS agent in launch type to interact with ECS, ECR, Cloudwatch logs.  
ECS Task Role - Allows each task to have a specific role and is defined in task definition.

ECS can be integrated with ELBs.  
EFS with ECS - can be used with both launch types adn task running in any AZ will share the same data in EFS

#### ECS Service Auto Scaling

ECS Service Auto Scaling is a feature that allows you to automatically scale your ECS services based on metrics such as:

- CPU utilization
- Memory utilization(based on RAM)
- ALB request count per target.

Types of Auto Scaling:

- Step Scaling - Scale based on a specific Cloudwatch Alram.
- Target Tracking - Scale based on target value of specific CloudWatch metric.
- Scheduled Scaling - Scale based on a specified date/time(predictable changes).

Fargate Autoscaling is easier to setup.

**EC2 Launch Type - Auto Scaling**

- Auto Scaling Group - Add EC2 instances overtime.
- Cluster Capacty Provider(Preferred) - Automatically scale for the ECS tasks. Adds EC2 instances when you're missing capacity(CPU,RAM)

**ECR - Elastice Container Repository** - Used to store and manage socker images on AWS.

#### AWS Lambda

AWS Lambda is a serverless compute service that allows you to run code without provisioning or managing servers.
Run on demand, scaling is automated.  
Generous free tier.

Containers on lambda must implement Lambda Runtime API

## Monitoring and Logging

---

### CloudWatch

---

#### Cloud Watch Metrics

Metric is a variable to monitor CPU usage, NetworkIn etc.

- Namespace - AWS service that the metric is from.
- Dimensions - Key-value pairs that uniquely identify the metric.

Can create cloudwatch dashboard for metrics and also custom metrics(eg RAM).  
Can stream cloudwatch metric to destination(Kinesis Data Firehose then to destinations(S3,redshift,opensearch),3rd party services) in real-time.

#### CloudWatch Logs

Store application logs in AWS.

- Log Group - Collection of log streams, usually representing an application with a name.
- Log Stream - Collection of log events, usually representing a specific instance of an application.

Can define expiration policies.  
Can be streamed to different services.  
Logs are encrypted by default.

CloudWatch Logs Insights - It helps query the logs for a specific timeframe.  
CloudWatch Logs Subscriptions - Get Real time logs for processing and analysis.  
**Live Tail**(Free: 1hr per day) - Live logs are displayed according to filters.  
CloudWatch Agents(By default, EC2 Insance do not go to cloudwatch) - Run on EC2 instances to push log files on the instance to cloudwatch.

- CloudWatch Logs Agent(Old) - Push logs from EC2 instance to cloudwatch.
- CloudWatch Unified Agent(New) - collects logs and system level metrics(RAM,processes,etc)

#### CloudWatch Alarms

Alarms are used to trigger notification when any metric goes beyond a given threshold.
**Composite Alarms** - are used combine and monitor states of multiple alarms.Use AND and OR conditions.

#### EventBridge

EventBridge is a serverless event bus that makes it easy to respond to state changes in your AWS applications. There are different event buses such as:

- Default Event Bus - The default event bus is used for all events.
- Partner Event Bus - Respond to events on partnered services such as Datadog,etc.
- Custom Event Bus - Create a custom event bus for your application.

Archive events and also has ability to replay archived events, used for debugging purposes.

#### CloudWatch Insights

- Logs Insights - Query logs for a specific timeframe.
- Container Insights
- Lambda Insights
- Contributor Insights
- Application Insights

### CloudTrail

---

CloudTrail is a service that provides a record of all history/API calls made within your AWS account. This
includes calls made by users, roles, and AWS services.

- CloudTrail Event - An event is a single API call made within your AWS account.
  - Management Events - Operations performed on resources such as configuring security.
  - Data Events - Operations performed on resources such as reading data from S3.
- CloudTrail Insights - Used to detect unusual activity in the account.
- CloudTrail Events Retention - Events are stored for 90 days, to keep beyond this period log them to S3.

### AWS Config

---

AWS Config is a service that provides a detailed inventory of the resources in your AWS account, as well as a history of all changes made to these resources. It helps recod configs and changes over time.

#### AWS Config Rules

Evaluate resources against rules to ensure compliance.  
Can use AWS managed rules and custom rules.  
These rules does not prevent action from happening.  
No free tier.
