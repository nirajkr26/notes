# Cloud Computing — Interview & Exam Notes

> 📌 **GitHub:** [nirajkr26](https://github.com/nirajkr26) &nbsp;|&nbsp; **LinkedIn:** [nirajkr26](https://www.linkedin.com/in/nirajkr26)

---

## 1. Introduction / Overview

**Cloud Computing** is the on-demand delivery of computing resources (servers, storage, databases, networking, software, analytics, intelligence) over the Internet ("the cloud") with pay-as-you-go pricing.

**Key Characteristics (NIST Definition):**

| Characteristic             | Description                                                               |
|----------------------------|---------------------------------------------------------------------------|
| **On-demand self-service** | Provision resources automatically without human interaction               |
| **Broad network access**   | Available over the network via standard devices                           |
| **Resource pooling**       | Provider resources shared among many tenants (multi-tenancy)              |
| **Rapid elasticity**       | Scale up/down quickly based on demand                                     |
| **Measured service**       | Usage is metered, monitored, and billed accordingly                       |

**Benefits:**
- No upfront capital expenditure (CapEx → OpEx)
- Global reach and low latency via geographically distributed data centers
- High availability and disaster recovery built-in
- Automatic software updates and maintenance
- Faster time to market

**Challenges:**
- Security and compliance concerns
- Vendor lock-in
- Internet dependency / latency
- Data privacy and sovereignty issues
- Cost management (cloud sprawl)

---

## 2. Service Models (IaaS, PaaS, SaaS, FaaS)

### 2.1 The Stack

```
┌──────────────────────────────────────────────────────────┐
│  SaaS — Applications (Gmail, Salesforce, Dropbox)        │
├──────────────────────────────────────────────────────────┤
│  PaaS — Platform (AWS Elastic Beanstalk, Heroku, GKE)    │
├──────────────────────────────────────────────────────────┤
│  IaaS — Infrastructure (AWS EC2, Azure VMs, GCP Compute) │
├──────────────────────────────────────────────────────────┤
│  Physical Hardware (Data Centers)                         │
└──────────────────────────────────────────────────────────┘
```

### 2.2 Responsibility Comparison

| Layer              | On-Premises | IaaS       | PaaS       | SaaS       |
|--------------------|-------------|------------|------------|------------|
| Applications       | You         | You        | You        | Provider   |
| Data               | You         | You        | You        | Provider   |
| Runtime            | You         | You        | Provider   | Provider   |
| Middleware         | You         | You        | Provider   | Provider   |
| OS                 | You         | You        | Provider   | Provider   |
| Virtualization     | You         | Provider   | Provider   | Provider   |
| Servers            | You         | Provider   | Provider   | Provider   |
| Storage            | You         | Provider   | Provider   | Provider   |
| Networking         | You         | Provider   | Provider   | Provider   |

### 2.3 Service Model Details

**IaaS (Infrastructure as a Service):**
- Provides virtualized computing infrastructure (VMs, storage, networking)
- User manages OS upward; provider manages hardware/hypervisor
- Examples: AWS EC2, Azure VMs, Google Compute Engine, DigitalOcean Droplets
- Use case: Full control over OS, custom configurations, lift-and-shift migrations

**PaaS (Platform as a Service):**
- Provides a platform to build, deploy, and manage applications without managing infrastructure
- User manages applications and data; provider manages runtime, OS, infrastructure
- Examples: AWS Elastic Beanstalk, Google App Engine, Heroku, Azure App Service, OpenShift
- Use case: Developers focused on code, not infrastructure

**SaaS (Software as a Service):**
- Complete software application delivered over the internet; user just uses the software
- Provider manages everything; user only configures application settings
- Examples: Gmail, Microsoft 365, Salesforce, Dropbox, Zoom, Slack
- Use case: End-user business applications

**FaaS / Serverless (Function as a Service):**
- Run individual functions in response to events; no server management
- Auto-scales to zero; billed per invocation and execution time
- Examples: AWS Lambda, Azure Functions, Google Cloud Functions, Cloudflare Workers
- Use case: Event-driven workloads, microservices, background jobs

**CaaS (Container as a Service):**
- Container orchestration platform managed by provider
- Examples: AWS ECS/EKS, Azure AKS, Google GKE
- Use case: Containerized microservices

---

## 3. Deployment Models

| Model              | Description                                                         | Managed By       | Use Case                                   |
|--------------------|---------------------------------------------------------------------|------------------|--------------------------------------------|
| **Public Cloud**   | Resources shared among multiple organizations over the internet     | Cloud Provider   | Startups, variable workloads, dev/test      |
| **Private Cloud**  | Dedicated cloud infrastructure for a single organization            | Organization/MSP | Financial, healthcare, high compliance needs |
| **Hybrid Cloud**   | Combination of public + private cloud with data/application portability | Both          | Burst to public, keep sensitive data private |
| **Multi-Cloud**    | Using services from multiple cloud providers                        | Organization     | Avoid vendor lock-in, best-of-breed services |
| **Community Cloud**| Shared infrastructure for specific community (e.g., government)    | Community/Provider| Government, research, specific industry   |

**Hybrid Cloud Use Cases:**
- **Cloud bursting**: Run baseline on private cloud, burst to public for peak loads
- **Data sovereignty**: Keep sensitive data on-prem while using public cloud for compute
- **Gradual migration**: Migrate workloads incrementally

---

## 4. Major Cloud Providers

### 4.1 AWS (Amazon Web Services)

| Category           | Key Services                                                       |
|--------------------|--------------------------------------------------------------------|
| **Compute**        | EC2, Lambda, ECS, EKS, Fargate, Elastic Beanstalk, Batch          |
| **Storage**        | S3, EBS, EFS, Glacier, Storage Gateway                            |
| **Database**       | RDS, DynamoDB, Aurora, ElastiCache, Redshift, DocumentDB          |
| **Networking**     | VPC, Route 53, CloudFront, ELB, API Gateway, Direct Connect       |
| **Security**       | IAM, KMS, Cognito, WAF, Shield, GuardDuty, Secrets Manager        |
| **Monitoring**     | CloudWatch, CloudTrail, X-Ray                                     |
| **CI/CD**          | CodePipeline, CodeBuild, CodeDeploy, CodeCommit                   |
| **Messaging**      | SQS, SNS, EventBridge, Kinesis, MQ                                |
| **ML/AI**          | SageMaker, Rekognition, Comprehend, Textract, Bedrock             |
| **IaC**            | CloudFormation, CDK                                               |

### 4.2 Azure (Microsoft Azure)

| Category           | Key Services                                                       |
|--------------------|--------------------------------------------------------------------|
| **Compute**        | Virtual Machines, AKS, App Service, Functions, Container Instances |
| **Storage**        | Blob Storage, Disk Storage, Files, Data Lake Storage              |
| **Database**       | SQL Database, Cosmos DB, Azure Cache for Redis, Synapse Analytics |
| **Networking**     | Virtual Network, DNS, CDN, Front Door, Application Gateway        |
| **Security**       | Azure AD, Key Vault, Sentinel, Defender for Cloud                 |
| **Monitoring**     | Monitor, Application Insights, Log Analytics                      |
| **Messaging**      | Service Bus, Event Hubs, Event Grid                               |
| **IaC**            | ARM Templates, Bicep, Terraform                                   |

### 4.3 GCP (Google Cloud Platform)

| Category           | Key Services                                                       |
|--------------------|--------------------------------------------------------------------|
| **Compute**        | Compute Engine, GKE, Cloud Run, Cloud Functions, App Engine       |
| **Storage**        | Cloud Storage, Persistent Disk, Filestore                         |
| **Database**       | Cloud SQL, Spanner, Firestore, Bigtable, BigQuery, Memorystore     |
| **Networking**     | VPC, Cloud DNS, Cloud CDN, Load Balancing, Cloud Interconnect     |
| **Security**       | IAM, Cloud KMS, Secret Manager, Security Command Center           |
| **Monitoring**     | Cloud Monitoring, Cloud Logging, Cloud Trace                      |
| **Messaging**      | Pub/Sub, Eventarc                                                 |
| **ML/AI**          | Vertex AI, AutoML, Gemini, Vision AI                              |
| **IaC**            | Cloud Deployment Manager, Terraform                               |

### 4.4 AWS vs Azure vs GCP Comparison

| Feature              | AWS                     | Azure                    | GCP                      |
|----------------------|-------------------------|--------------------------|--------------------------|
| Market share         | ~31% (leader)           | ~25% (2nd)               | ~11% (3rd)               |
| Compute unit         | EC2 Instance            | Virtual Machine          | Compute Engine VM        |
| Object storage       | S3                      | Blob Storage             | Cloud Storage            |
| Managed Kubernetes   | EKS                     | AKS                      | GKE (most mature)        |
| Serverless           | Lambda                  | Functions                | Cloud Functions/Run      |
| NoSQL database       | DynamoDB                | Cosmos DB                | Firestore / Bigtable     |
| DNS                  | Route 53                | Azure DNS                | Cloud DNS                |
| CDN                  | CloudFront              | Azure CDN / Front Door   | Cloud CDN                |
| IAM                  | AWS IAM                 | Azure AD / RBAC          | Cloud IAM                |
| Data warehouse       | Redshift                | Synapse Analytics        | BigQuery                 |
| Strength             | Widest service portfolio| Enterprise/hybrid (Windows) | Data/ML, GKE          |

---

## 5. Compute Services

### 5.1 Virtual Machines

- Virtualized hardware using hypervisors (Type 1: VMware ESXi, Hyper-V, KVM; Type 2: VirtualBox, VMware Workstation)
- Full OS isolation; billed per hour/second
- AWS EC2 instance types: General Purpose (t3, m6i), Compute Optimized (c6i), Memory Optimized (r6i), Storage Optimized (i3), GPU (p3, g4)

### 5.2 Containers vs VMs vs Serverless

| Feature           | VMs                    | Containers              | Serverless              |
|-------------------|------------------------|-------------------------|-------------------------|
| Isolation         | Full OS                | Process-level           | Function-level          |
| Startup time      | Minutes                | Seconds                 | Milliseconds            |
| Resource usage    | High (full OS)         | Low                     | Minimal                 |
| Scaling           | Manual / auto-scaling  | Container orchestration | Automatic (to zero)     |
| Management        | High                   | Medium                  | Low                     |
| Cost model        | Per hour               | Per hour                | Per invocation          |
| State             | Stateful               | Can be stateful         | Stateless               |
| Cold start        | N/A                    | Very fast               | Can be an issue         |

### 5.3 Serverless / FaaS Deep Dive

**How it works:**
1. Function code uploaded to cloud
2. Triggered by events (HTTP, queue message, file upload, timer)
3. Cloud provisions resources, runs function, returns result
4. Resources released after execution

**Pricing:** Billed per request + execution time (GB-seconds)

**Cold Start Problem:**
- When a function hasn't been invoked recently, container is spun up from scratch → latency spike
- Mitigation: Provisioned concurrency (AWS Lambda), keep-alive pings, optimize package size

**Use cases:** REST APIs, event-driven processing, scheduled jobs, data transformation, webhooks

**Limitations:**
- Max execution time (AWS Lambda: 15 min, Azure Functions: 10 min default)
- Max memory (AWS Lambda: 10 GB)
- No persistent state between invocations
- Cold start latency

---

## 6. Storage Services

### 6.1 Storage Types

| Type                    | Description                                           | AWS Service          | Use Case                          |
|-------------------------|-------------------------------------------------------|----------------------|-----------------------------------|
| **Object Storage**      | Store files as objects with metadata and unique ID    | S3                   | Images, backups, static website   |
| **Block Storage**       | Low-level storage volumes attached to VMs             | EBS                  | OS disks, databases               |
| **File Storage (NFS)**  | Shared file system mounted by multiple instances      | EFS                  | Shared storage, CMS               |
| **Archive Storage**     | Infrequent access, very cheap long-term storage       | S3 Glacier           | Compliance archives               |
| **In-Memory Storage**   | Sub-millisecond key-value store (RAM-based)           | ElastiCache (Redis/Memcached) | Sessions, caching        |
| **Data Warehouse**      | Columnar store for large-scale analytics              | Redshift / BigQuery  | BI, analytics, reporting          |

### 6.2 S3 (AWS Simple Storage Service)

**Key Concepts:**
- Buckets contain objects; objects have key (name), value (data), metadata, version ID
- Globally unique bucket names; region-specific
- Maximum object size: 5 TB (multipart upload required for > 5 GB)
- 99.999999999% (11 nines) durability

**S3 Storage Classes:**

| Class                      | Use Case                             | Retrieval   | Cost       |
|----------------------------|--------------------------------------|-------------|------------|
| S3 Standard                | Frequently accessed data             | Immediate   | Highest    |
| S3 Intelligent-Tiering     | Unknown access patterns              | Immediate   | Varies     |
| S3 Standard-IA             | Infrequent access, quick retrieval   | Immediate   | Lower      |
| S3 One Zone-IA             | Infrequent, single AZ                | Immediate   | Lower      |
| S3 Glacier Instant         | Archive, millisecond retrieval       | Immediate   | Very low   |
| S3 Glacier Flexible        | Archive, minutes-to-hours retrieval  | Minutes–hours | Very low |
| S3 Glacier Deep Archive    | Long-term archive, ≤12 hrs retrieval | Hours       | Lowest     |

**S3 Features:**
- **Versioning**: Keep multiple versions of objects
- **Lifecycle policies**: Automatically transition or expire objects
- **Cross-Region Replication (CRR)**: Replicate objects to another region
- **S3 Event Notifications**: Trigger Lambda/SQS/SNS on object events
- **Pre-signed URLs**: Temporary access to private objects
- **Server-Side Encryption**: SSE-S3, SSE-KMS, SSE-C
- **Static Website Hosting**: Serve static HTML/CSS/JS

---

## 7. Networking in the Cloud

### 7.1 VPC (Virtual Private Cloud)

A **VPC** is an isolated virtual network within the cloud where you launch your resources.

**Key VPC Components:**

| Component             | Description                                                          |
|-----------------------|----------------------------------------------------------------------|
| **CIDR Block**        | IP address range for the VPC (e.g., 10.0.0.0/16)                    |
| **Subnets**           | Subdivisions of VPC (public = internet-accessible, private = not)   |
| **Internet Gateway**  | Enables communication between VPC and internet                       |
| **NAT Gateway**       | Allows private subnet instances to initiate outbound internet traffic |
| **Route Table**       | Rules that determine where network traffic is directed               |
| **Security Group**    | Stateful firewall at instance level (allow rules only)               |
| **NACL**              | Stateless firewall at subnet level (allow and deny rules)            |
| **VPC Peering**       | Connect two VPCs privately (non-transitive)                          |
| **VPN Gateway**       | IPSec VPN connection between VPC and on-premises network             |
| **Direct Connect**    | Dedicated private network connection to AWS (AWS Direct Connect)     |
| **Transit Gateway**   | Hub-and-spoke model to connect many VPCs and on-premises networks    |
| **Endpoint**          | Private connection to AWS services without internet (Gateway/Interface) |

### 7.2 Security Group vs NACL

| Feature         | Security Group                        | NACL (Network ACL)                     |
|-----------------|---------------------------------------|----------------------------------------|
| Level           | Instance/ENI level                    | Subnet level                           |
| Stateful?       | Yes (return traffic auto-allowed)     | No (must explicitly allow both ways)   |
| Rules           | Allow only                            | Allow and Deny                         |
| Rule evaluation | All rules evaluated                   | Rules evaluated in order (numbered)    |
| Default         | Deny all inbound, allow all outbound  | Allow all inbound and outbound         |
| Association     | Multiple instances                    | One per subnet (many subnets per NACL) |

### 7.3 DNS and CDN

**DNS (Domain Name System):**
- Translates domain names to IP addresses
- AWS Route 53: Authoritative DNS + domain registrar
- Routing policies: Simple, Weighted, Latency, Failover, Geolocation, Geoproximity, Multivalue

**CDN (Content Delivery Network):**
- Distributes content to geographically distributed edge servers (PoPs)
- Reduces latency by serving from nearest edge location
- Caches static content; can also cache dynamic content
- AWS CloudFront, Azure CDN, Google Cloud CDN, Cloudflare, Akamai
- Origin: S3 bucket, EC2, ALB, on-premises server

### 7.4 Load Balancing

**Types of Load Balancers (AWS):**

| Type                              | OSI Layer | Use Case                                           |
|-----------------------------------|-----------|----------------------------------------------------|
| Application Load Balancer (ALB)   | Layer 7   | HTTP/HTTPS routing by path, headers, host          |
| Network Load Balancer (NLB)       | Layer 4   | TCP/UDP, ultra-high performance, static IP         |
| Gateway Load Balancer (GWLB)      | Layer 3   | Deploy/manage third-party virtual appliances       |
| Classic Load Balancer (CLB)       | 4 & 7     | Legacy; EC2-Classic only                           |

**Load Balancing Algorithms:**
- Round Robin, Weighted Round Robin, Least Connections, IP Hash, Random

---

## 8. Databases in the Cloud

### 8.1 Cloud Database Categories

| Type              | Description                                  | AWS Example           | Use Case                        |
|-------------------|----------------------------------------------|-----------------------|---------------------------------|
| **Relational**    | SQL, ACID, structured schema                 | RDS, Aurora           | OLTP, financial systems         |
| **Key-Value**     | Simple GET/SET by key                        | DynamoDB, ElastiCache | Sessions, shopping carts        |
| **Document**      | JSON/BSON documents, flexible schema         | DocumentDB, DynamoDB  | Catalogs, user profiles         |
| **Wide-Column**   | Rows with many columns (column families)     | Keyspaces (Cassandra) | IoT, time-series, analytics     |
| **Graph**         | Nodes and edges for relationship-heavy data  | Neptune               | Social networks, fraud detection|
| **Time-Series**   | Optimized for timestamped data               | Timestream            | IoT metrics, monitoring         |
| **Ledger**        | Immutable, cryptographically verifiable log  | QLDB                  | Financial records, supply chain |
| **Search**        | Full-text search and analytics               | OpenSearch Service    | Log analytics, search features  |
| **Data Warehouse**| Columnar, OLAP, large-scale analytics        | Redshift, BigQuery    | BI dashboards, reporting        |

### 8.2 RDS vs Aurora vs DynamoDB

| Feature          | RDS                               | Aurora                                | DynamoDB                        |
|------------------|-----------------------------------|---------------------------------------|---------------------------------|
| Type             | Relational (SQL)                  | Relational (MySQL/PostgreSQL compat.) | NoSQL key-value/document        |
| Scaling          | Vertical + read replicas          | Auto-scaling storage, up to 15 replicas | Horizontal, auto-scaling     |
| Durability       | Multi-AZ                          | 6 copies across 3 AZs                 | Multi-region replication        |
| Performance      | Standard                          | 5x MySQL, 3x PostgreSQL               | Single-digit ms at any scale    |
| Serverless option| No                                | Aurora Serverless v2                  | On-demand (serverless)          |
| Managed          | Yes                               | Yes (more managed than RDS)           | Fully managed                   |

### 8.3 CAP Theorem

> A distributed system can guarantee at most **two** of the three: **C**onsistency, **A**vailability, **P**artition Tolerance.

- **CP** (Consistency + Partition Tolerance): HBase, Zookeeper, etcd — prefers correctness over availability
- **AP** (Availability + Partition Tolerance): Cassandra, DynamoDB (eventually consistent), CouchDB — prefers availability
- **CA** (Consistency + Availability): Traditional RDBMS (MySQL, PostgreSQL) — only works without network partitions (single node)

Since network partitions are unavoidable in distributed systems, real systems choose between **CP** and **AP**.

---

## 9. Identity & Access Management (IAM)

### 9.1 AWS IAM Concepts

| Concept             | Description                                                              |
|---------------------|--------------------------------------------------------------------------|
| **User**            | Individual identity with long-term credentials (username/password, access keys) |
| **Group**           | Collection of users; attach policies to group                           |
| **Role**            | Identity with temporary credentials; assumed by services, users, or federated identities |
| **Policy**          | JSON document defining permissions (Allow/Deny actions on resources)    |
| **Permission Boundary** | Maximum permissions an identity can have                            |
| **SCP (Service Control Policy)** | Organization-level guardrails (AWS Organizations)         |

### 9.2 IAM Policy Structure

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowS3ReadAccess",
      "Effect": "Allow",
      "Principal": { "AWS": "arn:aws:iam::123456789:user/alice" },
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::mybucket",
        "arn:aws:s3:::mybucket/*"
      ],
      "Condition": {
        "StringEquals": {
          "aws:RequestedRegion": "us-east-1"
        }
      }
    }
  ]
}
```

### 9.3 IAM Best Practices

- **Principle of Least Privilege**: Grant only the permissions needed
- **Enable MFA**: For root account and privileged users
- **No root access keys**: Never create/use root account access keys
- **Use roles for EC2**: Attach IAM roles to instances instead of embedding credentials
- **Rotate credentials**: Regularly rotate access keys and passwords
- **Use IAM conditions**: Restrict access by IP, MFA, time, region
- **Use AWS Organizations + SCPs**: Set guardrails across accounts
- **Audit with CloudTrail**: Log all API calls for auditing

### 9.4 Authentication & Authorization

**Authentication methods:**
- Username + password (IAM Users)
- Access Key ID + Secret Access Key (programmatic access)
- IAM Roles + STS (temporary credentials)
- SSO / SAML Federation (enterprise identity providers)
- OIDC (GitHub Actions, Kubernetes workloads)

**Authorization flow (AWS):**
1. Request received (who, what action, what resource, conditions)
2. All applicable policies evaluated
3. Explicit Deny → Deny (overrides any Allow)
4. Explicit Allow → Allow
5. Default → Implicit Deny

---

## 10. Cloud Security

### 10.1 Shared Responsibility Model

| Aspect                    | Cloud Provider Responsibility          | Customer Responsibility                |
|---------------------------|----------------------------------------|----------------------------------------|
| Physical security         | Data center, hardware                  | N/A                                    |
| Virtualization/Hypervisor | Hypervisor patching                    | N/A                                    |
| Network infrastructure    | Global network backbone                | VPC config, security groups, NACLs     |
| Storage encryption        | Physical media encryption              | Enable encryption, manage keys         |
| OS patching               | IaaS: Provider handles host OS only   | Patch guest OS (IaaS), App layer       |
| Application security      | N/A                                    | Customer fully responsible             |
| Identity & access         | IAM service infrastructure             | Configure users, roles, policies       |
| Data                      | Durability guarantees                  | Backup strategy, data classification   |

### 10.2 Encryption

**Encryption at Rest:**
- AWS KMS (Key Management Service): Create, rotate, manage encryption keys
- SSE (Server-Side Encryption) for S3: SSE-S3, SSE-KMS, SSE-C
- EBS volume encryption
- RDS encryption with KMS keys

**Encryption in Transit:**
- TLS/SSL for all data in transit
- HTTPS endpoints, VPN tunnels, TLS-enabled database connections
- AWS ACM (Certificate Manager) for free TLS certificates

**Key Management:**

| Option          | Description                                      |
|-----------------|--------------------------------------------------|
| SSE-S3          | AWS-managed keys, fully automatic                |
| SSE-KMS         | Customer managed keys via KMS; audit trail in CloudTrail |
| SSE-C           | Customer-provided keys; you manage key material  |
| Client-side     | Encrypt before uploading; full control           |

### 10.3 Compliance & Governance

| Framework / Standard | Description                                          |
|----------------------|------------------------------------------------------|
| **SOC 2**            | Security, availability, processing integrity, confidentiality, privacy |
| **ISO 27001**        | Information security management system (ISMS)        |
| **PCI DSS**          | Payment card industry data security standard         |
| **HIPAA**            | US healthcare data privacy standard                  |
| **GDPR**             | EU data protection regulation                        |
| **FedRAMP**          | US federal government cloud security standard        |
| **CIS Benchmarks**   | Security configuration best practices for cloud      |

**AWS Compliance Tools:**
- **AWS Config**: Track resource configuration changes and compliance
- **AWS Security Hub**: Unified security view, aggregates findings
- **AWS GuardDuty**: Threat detection using ML on CloudTrail/VPC Flow/DNS logs
- **AWS Inspector**: Automated security assessment for EC2/Lambda/ECR
- **AWS Macie**: Discover and protect sensitive data (PII) in S3

---

## 11. Scalability & High Availability

### 11.1 Scalability Concepts

| Type                    | Description                                              | Example                        |
|-------------------------|----------------------------------------------------------|--------------------------------|
| **Vertical scaling (Scale Up)** | Increase resources of a single instance (more CPU/RAM) | t3.micro → m5.4xlarge  |
| **Horizontal scaling (Scale Out)** | Add more instances of the same type             | 1 EC2 → 10 EC2 behind ALB  |
| **Auto Scaling**        | Automatically adjust capacity based on demand            | AWS Auto Scaling Groups        |
| **Elasticity**          | Scale in and out dynamically; release unused resources   | Lambda auto-scales to zero     |

### 11.2 High Availability (HA) Concepts

| Concept                 | Description                                                |
|-------------------------|------------------------------------------------------------|
| **Availability Zone (AZ)** | Isolated data center(s) within a region; physically separate |
| **Region**              | Geographic area containing multiple AZs                    |
| **Multi-AZ deployment** | Run resources in multiple AZs for fault tolerance          |
| **Multi-Region**        | Deploy across multiple regions for disaster recovery       |
| **RPO (Recovery Point Objective)** | Maximum acceptable data loss (time)             |
| **RTO (Recovery Time Objective)** | Maximum acceptable downtime after failure        |
| **SLA (Service Level Agreement)** | Agreed availability target (e.g., 99.99% = 52.6 min downtime/year) |

**Availability SLA Numbers:**

| Uptime    | Downtime per year |
|-----------|-------------------|
| 99%       | 3.65 days         |
| 99.9%     | 8.77 hours        |
| 99.99%    | 52.6 minutes      |
| 99.999%   | 5.26 minutes      |

### 11.3 Disaster Recovery Strategies

| Strategy                  | RPO          | RTO          | Cost     | Description                              |
|---------------------------|--------------|--------------|----------|------------------------------------------|
| **Backup & Restore**      | Hours        | Hours        | Lowest   | Periodic backups; restore when needed    |
| **Pilot Light**           | Minutes      | 10s of min   | Low      | Minimal standby with critical components running |
| **Warm Standby**          | Seconds      | Minutes      | Medium   | Scaled-down but fully functional copy    |
| **Multi-Site Active/Active** | Near zero | Near zero    | Highest  | Full capacity in multiple regions simultaneously |

---

## 12. Microservices & Cloud-Native Architecture

### 12.1 Monolith vs Microservices

| Feature            | Monolith                            | Microservices                           |
|--------------------|-------------------------------------|-----------------------------------------|
| Deployment         | Single deployable unit              | Each service deployed independently    |
| Scalability        | Scale whole application             | Scale individual services              |
| Technology stack   | Single stack                        | Each service can use different stack   |
| Development speed  | Slows as app grows                  | Teams work independently               |
| Failure isolation  | One bug can crash entire app        | Failures isolated to a service         |
| Data management    | Single shared database              | Each service owns its data             |
| Complexity         | Simple initially                    | Complex operations, networking, tracing|
| Testing            | Easier (single process)             | Integration testing is harder          |

### 12.2 12-Factor App Methodology

| Factor             | Principle                                                      |
|--------------------|----------------------------------------------------------------|
| Codebase           | One codebase tracked in version control, many deploys          |
| Dependencies       | Explicitly declare and isolate dependencies                    |
| Config             | Store config in environment variables (not in code)            |
| Backing Services   | Treat databases, queues, etc. as attached resources            |
| Build, Release, Run| Strictly separate build and run stages                        |
| Processes          | Execute app as stateless processes                             |
| Port Binding       | Export services via port binding                               |
| Concurrency        | Scale out via the process model                                |
| Disposability      | Fast startup and graceful shutdown                             |
| Dev/Prod Parity    | Keep development, staging, production as similar as possible   |
| Logs               | Treat logs as event streams                                    |
| Admin Processes    | Run admin tasks as one-off processes                           |

### 12.3 Service Mesh

A **service mesh** handles service-to-service communication in microservices:
- **Sidecar proxy pattern**: Envoy/Linkerd proxy injected alongside each service
- Features: Service discovery, load balancing, retries, circuit breaking, mTLS, observability
- Examples: Istio, Linkerd, Consul Connect, AWS App Mesh

---

## 13. Messaging & Event-Driven Architecture

### 13.1 Message Queue vs Pub/Sub

| Feature           | Message Queue                        | Pub/Sub (Topic)                         |
|-------------------|--------------------------------------|-----------------------------------------|
| Consumers         | Single consumer per message          | Multiple subscribers receive same message |
| Model             | Point-to-point                       | Fan-out / broadcast                     |
| Example (AWS)     | SQS                                  | SNS                                     |
| Use case          | Task queue, work distribution        | Notifications, event broadcasting       |

### 13.2 Key AWS Messaging Services

| Service        | Type           | Use Case                                               |
|----------------|----------------|--------------------------------------------------------|
| **SQS**        | Queue          | Decouple services, buffer requests, task processing    |
| **SNS**        | Pub/Sub        | Fan-out notifications, send to SQS/Lambda/email/HTTP   |
| **EventBridge**| Event Bus      | Event-driven integration between AWS services and SaaS |
| **Kinesis**    | Streaming      | Real-time data streaming, log aggregation              |
| **MSK (Kafka)**| Streaming      | Managed Apache Kafka for high-throughput streaming     |
| **MQ**         | Message Broker | Managed ActiveMQ/RabbitMQ for migration from on-prem   |

### 13.3 SQS Key Concepts

| Concept              | Description                                                      |
|----------------------|------------------------------------------------------------------|
| **Standard Queue**   | Best-effort ordering, at-least-once delivery, high throughput    |
| **FIFO Queue**       | Strict ordering, exactly-once processing, 3,000 TPS              |
| **Visibility Timeout** | Time message is hidden from other consumers after being received |
| **Dead Letter Queue (DLQ)** | Captures messages that fail processing (after max retries) |
| **Long Polling**     | Reduces empty responses; waits up to 20s for messages            |
| **Message Retention**| 4 days default (1 min to 14 days configurable)                  |

---

## 14. Monitoring & Observability

### 14.1 Pillars of Observability

| Pillar     | Description                                          | AWS Tool               |
|------------|------------------------------------------------------|------------------------|
| **Metrics**| Numerical measurements over time (CPU, requests/sec) | CloudWatch Metrics     |
| **Logs**   | Timestamped records of events                        | CloudWatch Logs        |
| **Traces** | End-to-end request path across distributed services  | AWS X-Ray              |
| **Events** | State changes and notifications                      | CloudWatch Events / EventBridge |

### 14.2 Key Monitoring Concepts

| Concept              | Description                                                   |
|----------------------|---------------------------------------------------------------|
| **Alarm**            | Trigger action when metric crosses threshold                  |
| **Dashboard**        | Visual display of metrics                                     |
| **Log Group/Stream** | Container for log events (CloudWatch Logs)                    |
| **Metric Filter**    | Extract metrics from log patterns                             |
| **Distributed Tracing** | Trace request path across services with correlation IDs   |
| **SLI**              | Service Level Indicator — specific metric (latency, error rate) |
| **SLO**              | Service Level Objective — target value for SLI               |
| **SLA**              | Service Level Agreement — contractual commitment             |
| **Error Budget**     | Allowed amount of downtime before SLO is breached            |

### 14.3 Common Monitoring Tools

| Tool            | Category    | Description                                          |
|-----------------|-------------|------------------------------------------------------|
| CloudWatch      | AWS Native  | Metrics, logs, alarms, dashboards                    |
| Prometheus      | Open Source | Metrics collection with pull model                   |
| Grafana         | Open Source | Visualization and dashboards for Prometheus, CloudWatch, etc. |
| Datadog         | SaaS        | Full-stack monitoring, APM, logs, security           |
| New Relic       | SaaS        | APM, infrastructure, full-stack observability        |
| Splunk          | SaaS/On-prem | Log management and SIEM                            |
| ELK Stack       | Open Source | Elasticsearch + Logstash + Kibana for log analytics  |
| Jaeger / Zipkin | Open Source | Distributed tracing                                  |

---

## 15. Infrastructure as Code (IaC)

### 15.1 IaC Concepts

**Infrastructure as Code** defines cloud resources in config files, enabling:
- Version control for infrastructure
- Reproducible environments
- Automated provisioning and change management
- Reduced human error

### 15.2 IaC Tools Comparison

| Tool              | Provider    | Language         | State Management | Key Feature                     |
|-------------------|-------------|------------------|------------------|---------------------------------|
| **Terraform**     | HashiCorp   | HCL              | State file (S3/remote) | Multi-cloud, provider-agnostic |
| **AWS CloudFormation** | AWS   | JSON / YAML      | Stacks           | Native AWS; free                |
| **AWS CDK**       | AWS         | Python/TS/Java   | CloudFormation   | Code-first, type-safe           |
| **Pulumi**        | Pulumi      | Python/TS/Go/C#  | State file       | Real programming languages      |
| **Ansible**       | Red Hat     | YAML (Playbooks) | Agentless        | Config management + provisioning |
| **Bicep**         | Microsoft   | Bicep DSL        | ARM states       | Azure-native IaC                |

### 15.3 Terraform Basics

```hcl
# Configure AWS provider
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
  backend "s3" {
    bucket = "my-tfstate"
    key    = "prod/terraform.tfstate"
    region = "us-east-1"
  }
}

provider "aws" {
  region = var.aws_region
}

# Create VPC
resource "aws_vpc" "main" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true

  tags = {
    Name        = "main-vpc"
    Environment = var.environment
  }
}

# Reference another resource
resource "aws_subnet" "public" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.0.1.0/24"
}

# Data source
data "aws_ami" "ubuntu" {
  most_recent = true
  owners      = ["099720109477"]

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-*-22.04-amd64-server-*"]
  }
}

# Output
output "vpc_id" {
  value = aws_vpc.main.id
}
```

**Terraform Workflow:**
```bash
terraform init      # Initialize providers and backend
terraform plan      # Preview changes
terraform apply     # Apply changes
terraform destroy   # Destroy all resources
terraform validate  # Validate configuration
terraform fmt       # Format code
terraform state ls  # List state resources
```

---

## 16. Cloud Cost Optimization

### 16.1 Cost Optimization Strategies

| Strategy                    | Description                                               |
|-----------------------------|-----------------------------------------------------------|
| **Right-sizing**            | Match instance size to actual workload needs              |
| **Reserved Instances**      | Commit to 1 or 3 years for up to 72% discount (AWS)      |
| **Spot Instances**          | Use spare capacity at up to 90% discount (interruptions possible) |
| **Auto Scaling**            | Scale down during low-demand periods                      |
| **Storage tiering**         | Move infrequent data to cheaper storage classes           |
| **Delete idle resources**   | Remove unused EIPs, snapshots, load balancers, volumes    |
| **Use managed services**    | Avoid running self-managed services that cost more        |
| **Savings Plans**           | Flexible pricing model; commit to usage amount           |
| **Tag resources**           | Track spending by team, project, environment              |
| **AWS Compute Optimizer**   | ML-based recommendations for EC2, Lambda, EBS sizing      |

### 16.2 EC2 Pricing Models

| Model                   | Discount vs On-Demand | Commitment   | Interruption Risk |
|-------------------------|-----------------------|--------------|-------------------|
| On-Demand               | Baseline (0%)         | None         | None              |
| Reserved (1-year)       | Up to 40%             | 1 year       | None              |
| Reserved (3-year)       | Up to 72%             | 3 years      | None              |
| Savings Plans (Compute) | Up to 66%             | 1–3 years    | None              |
| Spot Instances          | Up to 90%             | None         | Can be interrupted |
| Dedicated Hosts         | Varies                | On-demand/reserved | None        |

---

## 17. Common Interview Questions

### Q1. What is the difference between IaaS, PaaS, and SaaS?
**A:**
- **IaaS**: Provides virtualized hardware (VMs, storage, networking). You manage OS and above. E.g., AWS EC2.
- **PaaS**: Provides a platform to deploy applications. You manage only the application and data. E.g., Heroku, AWS Elastic Beanstalk.
- **SaaS**: Complete software delivered over internet. You just use it. E.g., Gmail, Salesforce.

### Q2. What is the Shared Responsibility Model?
**A:** A framework defining which security tasks are the cloud provider's responsibility (physical security, hypervisor, network infrastructure) and which are the customer's (OS patching for IaaS, application security, data encryption, IAM configuration). "Provider secures *of* the cloud; customer secures *in* the cloud."

### Q3. What is a VPC and why do we use it?
**A:** A Virtual Private Cloud is a logically isolated virtual network. It lets you control IP ranges, subnets (public/private), route tables, and security. Resources in a VPC are isolated from other customers' resources. Internet Gateway + public subnets provide internet access; NAT Gateway allows private resources to reach the internet without being reachable from it.

### Q4. Explain CAP Theorem.
**A:** In distributed systems, you can only guarantee two of three: Consistency (all nodes see the same data), Availability (every request gets a response), Partition Tolerance (system continues despite network partitions). Since partitions are unavoidable, systems trade off between CP (consistent but may be unavailable) and AP (available but may return stale data).

### Q5. What is the difference between horizontal and vertical scaling?
**A:** **Vertical scaling** (scale up): add more CPU/RAM/disk to existing instance. Has limits and typically requires downtime. **Horizontal scaling** (scale out): add more instances and distribute load via a load balancer. Preferred for cloud-native apps because it's more resilient and has no theoretical upper limit.

### Q6. What is serverless and when should you use it?
**A:** Serverless means you deploy code (functions) without managing servers. The cloud auto-provisions, auto-scales, and bills per execution. Use it for event-driven workloads, APIs with variable traffic, scheduled jobs, and background processing. Avoid it for long-running processes (>15 min), latency-sensitive hot paths (cold starts), or heavy stateful workloads.

### Q7. What is the difference between SQL and NoSQL databases?
**A:**
- **SQL (Relational)**: Fixed schema, ACID transactions, vertical scaling, great for complex queries and joins. E.g., PostgreSQL, RDS.
- **NoSQL**: Flexible/schemaless, BASE (eventually consistent), horizontal scaling, optimized for specific data models (document, key-value, graph, wide-column). E.g., DynamoDB, MongoDB.

Choose SQL for complex transactions, reporting. Choose NoSQL for high-throughput reads/writes, flexible schemas, or massive scale.

### Q8. What is the difference between Security Groups and NACLs in AWS?
**A:** **Security Groups** are stateful (return traffic auto-allowed), applied at the instance/ENI level, and support only allow rules. **NACLs** are stateless (must explicitly allow inbound and outbound), applied at subnet level, support both allow and deny rules, and evaluate rules in numbered order.

### Q9. What is a CDN and how does it work?
**A:** A Content Delivery Network is a globally distributed network of edge servers (PoPs). When a user requests content, it's served from the nearest edge location, reducing latency. Edge servers cache responses from the origin. On cache miss, they fetch from origin and cache for subsequent requests. Great for static assets, streaming, and geographic distribution.

### Q10. Explain the difference between RPO and RTO.
**A:** **RPO (Recovery Point Objective)** is the maximum amount of data loss that is acceptable, measured in time. **RTO (Recovery Time Objective)** is the maximum acceptable downtime after a failure. Lower values require more investment in backup/replication. Example: RPO=1hr means you can lose at most 1 hour of data; RTO=4hrs means the system must be restored within 4 hours.

### Q11. What is Infrastructure as Code (IaC) and why is it important?
**A:** IaC means defining and provisioning infrastructure through config files instead of manual UI/CLI actions. Benefits: version control, reproducibility, consistency across environments, automated change management, code review for infrastructure, and faster provisioning. Tools: Terraform (multi-cloud), CloudFormation (AWS-native), Pulumi (general-purpose languages).

### Q12. What is auto scaling and how does it work?
**A:** Auto scaling automatically adjusts compute capacity based on demand. AWS Auto Scaling Groups monitor metrics (CPU, requests/sec) via CloudWatch alarms or schedules. When threshold is breached, it launches or terminates instances, registers/deregisters from load balancer. Requires launch templates, min/max/desired capacity configuration.

---

## 18. Quick Revision Checklist

- [ ] Cloud service models: IaaS, PaaS, SaaS, FaaS — with examples
- [ ] Deployment models: Public, Private, Hybrid, Multi-Cloud
- [ ] NIST cloud characteristics (on-demand, broad access, pooling, elasticity, metered)
- [ ] AWS core services: EC2, S3, RDS, Lambda, VPC, IAM, CloudWatch, Route 53
- [ ] Shared Responsibility Model
- [ ] VPC components: subnets, IGW, NAT, security groups, NACLs, route tables
- [ ] Security Group (stateful) vs NACL (stateless)
- [ ] S3 storage classes and pricing tiers
- [ ] IAM: users, groups, roles, policies, Least Privilege principle
- [ ] Auto Scaling: scale-out vs scale-up
- [ ] Load Balancer types: ALB (L7), NLB (L4)
- [ ] CAP Theorem: CP vs AP
- [ ] Database types: RDS, DynamoDB, ElastiCache, Redshift
- [ ] RPO vs RTO and DR strategies
- [ ] Serverless: Lambda cold start, billing, limitations
- [ ] Monitoring: Metrics, Logs, Traces (CloudWatch, X-Ray)
- [ ] IaC: Terraform workflow (init, plan, apply, destroy)
- [ ] Messaging: SQS (queue) vs SNS (pub/sub)
- [ ] Cost optimization: Reserved, Spot, Savings Plans, right-sizing
- [ ] Encryption: at rest (KMS/SSE) vs in transit (TLS)
- [ ] Microservices vs Monolith trade-offs
- [ ] Multi-AZ vs Multi-Region deployments
