# Infrastructure Policy Rules

## Org policy

Organization-mandated rules distilled from the organization's policy document. They take precedence over everything else.

| ID | Policy |
|---|---|
| ASSUMP-01 | When a paved path exists and a customer is not compliant, the customer must remediate within an appropriate and agreed-upon timeline. |
| BB_AWS0100 | Password reuse prevention is suppressed (BB_AWS0100); no corresponding POLY<GO> requirement exists. |
| BB_AWS0101 | Password expiry within 90 days is suppressed (BB_AWS0101). |
| BB_AWS0120 | Inbound VNC (ports 5800/5900/5500) must be blocked. |
| BB_AWS0123 | Inbound Telnet (port 23) must be blocked. |
| BB_AWS0125 | ELB cross-zone load balancing must be enabled. |
| BB_AWS0128 | SQS policies must not have global wildcard ('.') access. |
| BB_AWS0136 | CloudFront WAF protection is suppressed (BB_AWS0136); POLY<GO> does not mandate WAF usage. |
| BB_AWS0137 | Inbound SSH (port 22) must be blocked. |
| BB_AWS0139 | Inbound RDP (port 3389) must be blocked. |
| BB_AWS01471 | S3 lifecycle policies must be enabled. |
| BB_AWS0148 | ELB must not use TCP all (unrestricted TCP listener). |
| BB_AWS0155 | S3 bucket policies must block ALL actions for all principals. |
| BB_AWS0156 | S3 bucket policies must block LIST actions for all principals. |
| BB_AWS0157 | Inbound Elasticsearch (ports 9200/9300) must be blocked. |
| BB_AWS0159 | Inbound etcd (port 2379) must be blocked. |
| BB_AWS0160 | Inbound MongoDB (ports 27017/27018) must be blocked. |
| BB_AWS0167 | Inbound LDAP (port 389) must be blocked. |
| BB_AWS0173 | Inbound NetBIOS (ports 137-139) must be blocked. |
| BB_AWS0176 | Inbound MSSQL (ports 1433/1434/135) must be blocked. |
| BB_AWS0183 | Inbound SQL Server Analysis (port 2383) must be blocked. |
| BB_AWS0184 | Inbound Oracle DB SSL (port 2484) must be blocked. |
| BB_AWS0187 | Inbound MySQL (port 3306) must be blocked. |
| BB_AWS0190 | Inbound PostgreSQL (port 5432) must be blocked. |
| BB_AWS0193 | Inbound Cassandra (port 7001) must be blocked. |
| BB_AWS0204 | RDS deletion protection must be enabled. |
| BB_AWS0205 | All ports/protocols from any source must be blocked. |
| BB_AWS0206 | Only ports 80/443 may be allowed from the internet. |
| BB_AWS0211 | S3 bucket policies must block DELETE actions from all principals. |
| BB_AWS0213 | S3 bucket policies must block GET actions from all principals. |
| ORG-01 | CSP compute resources must only be deployed using PCE-hardened (golden) images. |
| ORG-02 | Workloads must leverage PCE-approved deployment pipelines and automation. |
| ORG-03 | Workloads must leverage CTO-approved public cloud patterns where suitable patterns exist. |
| ORG-04 | Development activity must occur exclusively in PCE-managed development environments. |
| ORG-05 | Production workloads must operate exclusively in PCE-managed production environments. |
| ORG-06 | Production workloads must comply with CTO-approved disaster recovery patterns, including multi-region where appropriate. |
| ORG-07 | CSP environments must be scanned for vulnerabilities and remediated in accordance with the Vulnerability Management Standard. |
| CSPLOG-01 | CSP environments must integrate with CSP-native logging solutions (e.g., AWS Config, CloudTrail), unless explicitly approved by CTO. |
| CSPLOG-02 | Approved CSP Services must log activity, including configuration changes. |
| CSPLOG-03 | Logging must comply with the Logging Procedure. |
| DPE-01 | Restricted data stored in CSPs must be encrypted at rest using CTO-approved patterns. |
| DPE-02 | CTO-approved key management solutions must be used. |
| DPE-03 | All data in transit to, from, and within CSP environments must be encrypted. |
| DPE-04 | Production data must not be accessible from non-production CSP environments. |
| DPE-05 | CSP data stores containing Internal, Limited, or Restricted data must not be accessible over the public internet without authentication and authorization. |
| DPE-06 | Only CGC-approved regions may store data. |
| IAM-01 | CSP console access must leverage Org Single Sign On (BSSO) and PVF <GO> and/or PVFX <GO>. |
| IAM-02 | Administrative access to CSP environments must be limited to PCE-authorized team members. |
| IAM-03 | MFA is required for administrative access and for direct human access to the CSP control plane and related assets. |
| IAM-04 | Static identities are prohibited for interactive use. |
| IAM-05 | Templated roles are required to restrict access to approved environments and services. |
| IAM-06 | CSP credentials must be rotated at a maximum cadence of 90 days. |
| IAM-07 | CSP Services must integrate with CSP-native IAM and key management solutions. |
| IAM-08 | Programmatic access keys must be encrypted at rest and rotated at a maximum cadence of 90 days. |
| INIT-VM-01 | Virtual machine images must be rotated every 90 days (enforced by INIT; not explicitly required by POLY<GO>). |
| LOGP-01 | Logs must be forwarded to the organization's centralized log management solution. |
| LOGP-02 | Only the logging subsystem may write or modify log files. |
| LOGP-03 | Only authorized administrators may access logs for troubleshooting. |
| LOGP-04 | All other users must not have direct access to log files. |
| LOGP-05 | Log data at rest must be protected from alteration. |
| LOGP-06 | Systems and applications must log security-related events including successful and unsuccessful user registration, authentication, authorization, resource access, and privileged operations; network connection initiation, acceptance, or denial; creation, modification, and deletion of user accounts, group accounts, and objects; changes to security policy, access control configurations, and audit policy configurations; changes in system state; application/process failures and abnormal terminations; all actions taken by root or administrative users; all administrative access and activity; access attempts made to logs; initialization or clearing of audit logs; password change events; login and logout events; application errors (e.g., HTTP 403, 500); modification of security configuration; and starting and stopping of critical system services including the logging subsystem. |
| LOGP-07 | Logs must include sufficient information to accurately describe the event, including where available: date and time (including time zone if not UTC), type of action performed, identifiers of the subject, identifiers of the object acted upon, whether the action was allowed or denied, subsystem performing the action, reason codes for denied actions, before/after values when updating data (unless sensitive), and category or severity label. |
| LOGP-08 | Logs must be formatted in a structured way (e.g., JSON, KVP, XML) to support parsing and integrations. |
| LOGP-09 | Logs must not contain unsanitized user input, passwords, encryption or decryption keys, API keys or other credentials, bank or credit card information, Personal Data, personal health information, Restricted Client Data, Restricted Internal Data, Restricted Contributor Data, source code, IB or MSG content, or any information illegal to collect. |
| LOGP-10 | If user input must be logged, it must be sanitized first. |
| LOGP-11 | Client-side logs must not be relied upon for security decisions. |
| LOGP-12 | Client-side logs must be encrypted when possible. |
| LOGP-13 | Client-side logs must be separated from server logs when uploaded. |
| MON-01 | Operational monitoring of CSP environments must be performed by PCE. |
| MON-02 | Operational monitoring of workloads must be performed by Workload Teams. |
| MON-03 | Security monitoring must be performed by CSOC. |
| MON-04 | Security events must be monitored, triaged, escalated, and reported. |
| REG-01 | CSP Services may only be used in CGC-approved regions. |
| REG-02 | Use of non-approved regions must be reported immediately to CGC. |
| REG-03 | Only CTO-approved data center extension and network edge patterns may be used. |
| REG-04 | CSP organization and account structures must restrict access to approved regions and services, enforce environment separation, and implement preventative and detective controls mandated by CTO. |
| REG-05 | CSP account ownership must be registered and maintained in an authoritative directory. |
| TAG-01 | Required attributes (e.g., data classification, RTO) must be maintained. |
| TAG-02 | Tags must be applied to all workloads to support access control, automation, and cost allocation. |
| WLOG-01 | Logging must be enabled for access activity, configuration changes, and cost management activity. |

## Approved regions

Regions approved for running workloads and storing data.

| ID | Region |
|---|---|
| us-east-1 | N. Virginia |
| us-east-2 | Ohio |
| us-west-1 | N. California |
| us-west-2 | Oregon |
| eu-central-1 | Frankfurt |
| eu-west-1 | Ireland |
| eu-west-2 | London |
| eu-south-1 | Milan |
| eu-west-3 | Paris |
| eu-south-2 | Spain |
| eu-north-1 | Stockholm |
| eu-central-2 | Zurich |
| ap-southeast-2 | Sydney |
| ap-southeast-4 | Melbourne |
| ap-northeast-1 | Tokyo |
| ap-northeast-2 | Seoul |
| ap-northeast-3 | Osaka |
| ca-central-1 | Central |
| ca-west-1 | Calgary |

## Approved services

The approved AWS service catalog. Organization rules above override it.

| ID | Service |
|---|---|
| `svc:analytics:amazon-athena` | Amazon Athena |
| `svc:analytics:amazon-cloudsearch` | Amazon CloudSearch |
| `svc:analytics:amazon-data-firehose` | Amazon Data Firehose |
| `svc:analytics:amazon-datazone` | Amazon DataZone |
| `svc:analytics:amazon-emr` | Amazon EMR |
| `svc:analytics:amazon-finspace` | Amazon FinSpace |
| `svc:analytics:amazon-kinesis` | Amazon Kinesis |
| `svc:analytics:amazon-kinesis-data-streams` | Amazon Kinesis Data Streams |
| `svc:analytics:amazon-kinesis-video-streams` | Amazon Kinesis Video Streams |
| `svc:analytics:amazon-managed-service-for-apache-flink` | Amazon Managed Service for Apache Flink |
| `svc:analytics:amazon-managed-streaming-for-apache-kafka-amazon-msk` | Amazon Managed Streaming for Apache Kafka (Amazon MSK) |
| `svc:analytics:amazon-opensearch-serverless` | Amazon OpenSearch Serverless |
| `svc:analytics:amazon-opensearch-service` | Amazon OpenSearch Service |
| `svc:analytics:amazon-redshift` | Amazon Redshift |
| `svc:analytics:amazon-redshift-serverless` | Amazon Redshift Serverless |
| `svc:analytics:aws-clean-rooms` | AWS Clean Rooms |
| `svc:analytics:aws-data-exchange` | AWS Data Exchange |
| `svc:analytics:aws-data-pipeline` | AWS Data Pipeline |
| `svc:analytics:aws-entity-resolution` | AWS Entity Resolution |
| `svc:analytics:aws-glue` | AWS Glue |
| `svc:analytics:aws-lake-formation` | AWS Lake Formation |
| `svc:analytics:quick` | Quick |
| `svc:application-integration:amazon-appflow` | Amazon AppFlow |
| `svc:application-integration:amazon-eventbridge` | Amazon EventBridge |
| `svc:application-integration:amazon-managed-workflows-for-apache-airflow-mwaa` | Amazon Managed Workflows for Apache Airflow (MWAA) |
| `svc:application-integration:amazon-mq` | Amazon MQ |
| `svc:application-integration:amazon-simple-notification-service` | Amazon Simple Notification Service |
| `svc:application-integration:amazon-simple-queue-service` | Amazon Simple Queue Service |
| `svc:application-integration:amazon-simple-workflow-service` | Amazon Simple Workflow Service |
| `svc:application-integration:aws-b2b-data-interchange` | AWS B2B Data Interchange |
| `svc:application-integration:aws-step-functions` | AWS Step Functions |
| `svc:cloud-financial-management:aws-billing-conductor` | AWS Billing Conductor |
| `svc:cloud-financial-management:aws-budgets` | AWS Budgets |
| `svc:cloud-financial-management:aws-cost-and-usage-report` | AWS Cost and Usage Report |
| `svc:cloud-financial-management:aws-cost-explorer` | AWS Cost Explorer |
| `svc:cloud-financial-management:reserved-instance-ri-reporting` | Reserved Instance (RI) reporting |
| `svc:cloud-financial-management:savings-plans` | Savings Plans |
| `svc:compute:amazon-ec2` | Amazon EC2 |
| `svc:compute:amazon-ec2-auto-scaling` | Amazon EC2 Auto Scaling |
| `svc:compute:amazon-ec2-image-builder` | Amazon EC2 Image Builder |
| `svc:compute:amazon-lightsail` | Amazon Lightsail |
| `svc:compute:amazon-linux-2023` | Amazon Linux 2023 |
| `svc:compute:aws-app-runner` | AWS App Runner |
| `svc:compute:aws-batch` | AWS Batch |
| `svc:compute:aws-elastic-beanstalk` | AWS Elastic Beanstalk |
| `svc:compute:aws-fargate` | AWS Fargate |
| `svc:compute:aws-lambda` | AWS Lambda |
| `svc:compute:aws-outposts` | AWS Outposts |
| `svc:compute:aws-serverless-application-repository` | AWS Serverless Application Repository |
| `svc:compute:aws-wavelength` | AWS Wavelength |
| `svc:compute:vmware-cloud-on-aws` | VMware Cloud on AWS |
| `svc:containers:amazon-elastic-container-registry` | Amazon Elastic Container Registry |
| `svc:containers:amazon-elastic-container-service` | Amazon Elastic Container Service |
| `svc:containers:amazon-elastic-kubernetes-service` | Amazon Elastic Kubernetes Service |
| `svc:containers:aws-app2container` | AWS App2Container |
| `svc:containers:red-hat-openshift-service-on-aws` | Red Hat OpenShift Service on AWS |
| `svc:databases:amazon-aurora` | Amazon Aurora |
| `svc:databases:amazon-documentdb-with-mongodb-compatibility` | Amazon DocumentDB (with MongoDB compatibility) |
| `svc:databases:amazon-dynamodb` | Amazon DynamoDB |
| `svc:databases:amazon-elasticache` | Amazon ElastiCache |
| `svc:databases:amazon-keyspaces-for-apache-cassandra` | Amazon Keyspaces (for Apache Cassandra) |
| `svc:databases:amazon-lightsail-managed-databases` | Amazon Lightsail managed databases |
| `svc:databases:amazon-memorydb` | Amazon MemoryDB |
| `svc:databases:amazon-neptune` | Amazon Neptune |
| `svc:databases:amazon-rds-for-db2` | Amazon RDS for Db2 |
| `svc:databases:amazon-rds-on-vmware` | Amazon RDS on VMware |
| `svc:databases:amazon-relational-database-service` | Amazon Relational Database Service |
| `svc:databases:amazon-timestream` | Amazon Timestream |
| `svc:migration-and-transfer:aws-application-discovery-service` | AWS Application Discovery Service |
| `svc:migration-and-transfer:aws-database-migration-service` | AWS Database Migration Service |
| `svc:migration-and-transfer:aws-datasync` | AWS DataSync |
| `svc:migration-and-transfer:aws-mainframe-modernization-service` | AWS Mainframe Modernization Service |
| `svc:migration-and-transfer:aws-migration-hub` | AWS Migration Hub |
| `svc:migration-and-transfer:aws-snow-family` | AWS Snow Family |
| `svc:migration-and-transfer:aws-transfer-family` | AWS Transfer Family |
| `svc:migration-and-transfer:aws-transform-mgn` | AWS Transform MGN |
| `svc:ml-and-ai:amazon-augmented-ai` | Amazon Augmented AI |
| `svc:ml-and-ai:amazon-bedrock` | Amazon Bedrock |
| `svc:ml-and-ai:amazon-codeguru` | Amazon CodeGuru |
| `svc:ml-and-ai:amazon-comprehend` | Amazon Comprehend |
| `svc:ml-and-ai:amazon-comprehend-medical` | Amazon Comprehend Medical |
| `svc:ml-and-ai:amazon-devops-guru` | Amazon DevOps Guru |
| `svc:ml-and-ai:amazon-forecast` | Amazon Forecast |
| `svc:ml-and-ai:amazon-fraud-detector` | Amazon Fraud Detector |
| `svc:ml-and-ai:amazon-kendra` | Amazon Kendra |
| `svc:ml-and-ai:amazon-lex` | Amazon Lex |
| `svc:ml-and-ai:amazon-lookout-for-equipment` | Amazon Lookout for Equipment |
| `svc:ml-and-ai:amazon-lookout-for-metrics` | Amazon Lookout for Metrics |
| `svc:ml-and-ai:amazon-lookout-for-vision` | Amazon Lookout for Vision |
| `svc:ml-and-ai:amazon-monitron` | Amazon Monitron |
| `svc:ml-and-ai:amazon-partyrock` | Amazon PartyRock |
| `svc:ml-and-ai:amazon-personalize` | Amazon Personalize |
| `svc:ml-and-ai:amazon-polly` | Amazon Polly |
| `svc:ml-and-ai:amazon-q` | Amazon Q |
| `svc:ml-and-ai:amazon-rekognition` | Amazon Rekognition |
| `svc:ml-and-ai:amazon-sagemaker-ai` | Amazon SageMaker AI |
| `svc:ml-and-ai:amazon-textract` | Amazon Textract |
| `svc:ml-and-ai:amazon-transcribe` | Amazon Transcribe |
| `svc:ml-and-ai:amazon-translate` | Amazon Translate |
| `svc:ml-and-ai:aws-deepcomposer` | AWS DeepComposer |
| `svc:ml-and-ai:aws-deepracer` | AWS DeepRacer |
| `svc:ml-and-ai:aws-healthlake` | AWS HealthLake |
| `svc:ml-and-ai:aws-healthscribe` | AWS HealthScribe |
| `svc:ml-and-ai:aws-panorama` | AWS Panorama |
| `svc:ml-and-ai:kiro` | Kiro |
| `svc:networking-and-content-delivery:amazon-api-gateway` | Amazon API Gateway |
| `svc:networking-and-content-delivery:amazon-cloudfront` | Amazon CloudFront |
| `svc:networking-and-content-delivery:amazon-route-53` | Amazon Route 53 |
| `svc:networking-and-content-delivery:amazon-vpc` | Amazon VPC |
| `svc:networking-and-content-delivery:amazon-vpc-lattice` | Amazon VPC Lattice |
| `svc:networking-and-content-delivery:aws-app-mesh` | AWS App Mesh |
| `svc:networking-and-content-delivery:aws-cloud-map` | AWS Cloud Map |
| `svc:networking-and-content-delivery:aws-global-accelerator` | AWS Global Accelerator |
| `svc:networking-and-content-delivery:aws-private-5g` | AWS Private 5G |
| `svc:networking-and-content-delivery:aws-privatelink` | AWS PrivateLink |
| `svc:networking-and-content-delivery:aws-transit-gateway` | AWS Transit Gateway |
| `svc:networking-and-content-delivery:aws-verified-access` | AWS Verified Access |
| `svc:networking-and-content-delivery:direct-connect` | Direct Connect |
| `svc:networking-and-content-delivery:elastic-load-balancing` | Elastic Load Balancing |
| `svc:networking-and-content-delivery:integrated-private-wireless-on-aws` | Integrated Private Wireless on AWS |
| `svc:networking-and-content-delivery:site-to-site-vpn` | Site-to-Site VPN |
| `svc:security-identity-and-compliance:amazon-cognito` | Amazon Cognito |
| `svc:security-identity-and-compliance:amazon-detective` | Amazon Detective |
| `svc:security-identity-and-compliance:amazon-guardduty` | Amazon GuardDuty |
| `svc:security-identity-and-compliance:amazon-inspector` | Amazon Inspector |
| `svc:security-identity-and-compliance:amazon-macie` | Amazon Macie |
| `svc:security-identity-and-compliance:amazon-security-lake` | Amazon Security Lake |
| `svc:security-identity-and-compliance:amazon-verified-permissions` | Amazon Verified Permissions |
| `svc:security-identity-and-compliance:aws-artifact` | AWS Artifact |
| `svc:security-identity-and-compliance:aws-audit-manager` | AWS Audit Manager |
| `svc:security-identity-and-compliance:aws-certificate-manager` | AWS Certificate Manager |
| `svc:security-identity-and-compliance:aws-cloudhsm` | AWS CloudHSM |
| `svc:security-identity-and-compliance:aws-directory-service` | AWS Directory Service |
| `svc:security-identity-and-compliance:aws-firewall-manager` | AWS Firewall Manager |
| `svc:security-identity-and-compliance:aws-iam-identity-center` | AWS IAM Identity Center |
| `svc:security-identity-and-compliance:aws-identity-and-access-management` | AWS Identity and Access Management |
| `svc:security-identity-and-compliance:aws-key-management-service` | AWS Key Management Service |
| `svc:security-identity-and-compliance:aws-network-firewall` | AWS Network Firewall |
| `svc:security-identity-and-compliance:aws-resource-access-manager` | AWS Resource Access Manager |
| `svc:security-identity-and-compliance:aws-secrets-manager` | AWS Secrets Manager |
| `svc:security-identity-and-compliance:aws-security-hub-cspm` | AWS Security Hub CSPM |
| `svc:security-identity-and-compliance:aws-shield` | AWS Shield |
| `svc:security-identity-and-compliance:aws-waf` | AWS WAF |
| `svc:security-identity-and-compliance:aws-waf-captcha` | AWS WAF Captcha |
| `svc:storage:amazon-elastic-block-store` | Amazon Elastic Block Store |
| `svc:storage:amazon-elastic-file-system` | Amazon Elastic File System |
| `svc:storage:amazon-file-cache` | Amazon File Cache |
| `svc:storage:amazon-fsx-for-lustre` | Amazon FSx for Lustre |
| `svc:storage:amazon-fsx-for-netapp-ontap` | Amazon FSx for NetApp ONTAP |
| `svc:storage:amazon-fsx-for-openzfs` | Amazon FSx for OpenZFS |
| `svc:storage:amazon-fsx-for-windows-file-server` | Amazon FSx for Windows File Server |
| `svc:storage:amazon-simple-storage-service` | Amazon Simple Storage Service |
| `svc:storage:aws-backup` | AWS Backup |
| `svc:storage:aws-elastic-disaster-recovery` | AWS Elastic Disaster Recovery |
| `svc:storage:aws-storage-gateway` | AWS Storage Gateway |

## Selection guidance

AWS decision-guide criteria for choosing between approved services.

| ID | Topic | Guidance |
|---|---|---|
| `guidance:analytics:apache-kafka-processing-of-streaming-data` | Apache Kafka processing of streaming data | Optimized for using Apache Kafka data-plane operations and running open source versions of Apache Kafka. |
| `guidance:analytics:big-data-processing` | Big data processing | Optimized for processing, moving, and transforming large amounts of data. |
| `guidance:analytics:building-apache-flink-applications` | Building Apache Flink applications | Optimized for using Java, Scala, Python, or SQL to process and analyze streaming data. |
| `guidance:analytics:dashboards-and-visualizations` | Dashboards and visualizations | Optimized for visually representing complex datasets, and providing natural language query of your data. |
| `guidance:analytics:data-catalog` | Data catalog | Optimized for providing detailed information about the available data, its structure, characteristics, and relationships. |
| `guidance:analytics:data-warehousing` | Data warehousing | Optimized for centrally storing, organizing, and retrieving large volumes of structured and sometimes semi-structured data from various sources within an organization. |
| `guidance:analytics:interactive-analytics` | Interactive analytics | Optimized for performing real-time data analysis and exploration, which allows users to interactively query and visualize data. |
| `guidance:analytics:managed-opensearch-clusters` | Managed OpenSearch clusters | Optimized for log analytics, real-time application monitoring, and clickstream analysis. |
| `guidance:analytics:managing-data-access` | Managing data access | Optimized for setting up the proper management, availability, usability, integrity, and security of data throughout its lifecycle. |
| `guidance:analytics:object-storage-for-data-lakes` | Object storage for data lakes | Optimized for providing a data lake foundation with virtually unlimited scalability and high durability. |
| `guidance:analytics:real-time-processing` | Real-time processing | Optimized for rapid and continuous data intake and aggregation, including IT infrastructure log data, application logs, social media, market data feeds, and web clickstream data. |
| `guidance:analytics:real-time-streaming-data-delivery` | Real-time streaming data delivery | Optimized for delivering real-time streaming data to destinations such as Amazon S3, Amazon Redshift, OpenSearch Service, Splunk, Apache Iceberg Tables, and any custom HTTP endpoint or HTTP endpoints owned by supported third-party service providers. |
| `guidance:analytics:secure-data-clean-rooms` | Secure data clean rooms | Optimized for collaborating with other organizations without sharing raw underlying data. |
| `guidance:analytics:unified-access-to-data-lakes-and-data-warehouses` | Unified access to data lakes and data warehouses | Built on a lakehouse architecture to optimize for unifying data access across Amazon S3 data lakes, Amazon Redshift data warehouses, operational databases, and third-party and federated data sources. |
| `guidance:analytics:unified-analytics-and-ai` | Unified analytics and AI | Optimized for using a single development environment, Amazon SageMaker Unified Studio, to access data, analytics, and AI capabilities. |
| `guidance:analytics:workflow-orchestration` | Workflow orchestration | Optimized for creating, scheduling, and monitoring data workflows using Apache Airflow to coordinate analytics processes and ETL jobs. |
| `guidance:application-integration:capacity` | Capacity | Use when you need to decouple publishers and subscribers and send events to multiple subscribers simultaneously. |
| `guidance:application-integration:messaging` | Messaging | Use when you need either pub/sub messaging to broadcast messages to multiple recipients simultaneously, or point-to-point messaging when you need reliable and asynchronous communication between components. |
| `guidance:application-integration:scheduling` | Scheduling | Use scheduling when you need to automate routine tasks such as data processing, backups, or system health checks. Tasks often need to be run at specific times or intervals, such as every night, hour, or minute. |
| `guidance:application-integration:streaming` | Streaming | Use streaming services such as Amazon Kinesis Data Streams and Amazon Managed Streaming for Apache Kafka (MSK) in scenarios that involve handling and processing real-time streaming data. |
| `guidance:application-integration:workflows` | Workflows | Use when you need to design, coordinate, and manage workflows or sequences of tasks in an organized and scalable manner. |
| `guidance:cloud-financial-management:manage-and-control-aws-billing-console` | Manage and control - AWS Billing Console | Provides a dashboard to track overall spend and view cost breakdown by service and account. |
| `guidance:cloud-financial-management:manage-and-control-aws-budget-actions` | Manage and control - AWS Budget Actions | Provides functionality to run an action when a budget exceeds a certain cost or usage threshold, either automatically or after manual approval. |
| `guidance:cloud-financial-management:manage-and-control-aws-consolidated-billing` | Manage and control - AWS Consolidated Billing | Consolidates billing and payment for multiple AWS accounts, allowing shared volume discounts across all accounts. |
| `guidance:cloud-financial-management:manage-and-control-aws-cost-anomaly-detection` | Manage and control - AWS Cost Anomaly Detection | Helps you use machine learning models to detect and alert on anomalous spend patterns in your deployed AWS services. |
| `guidance:cloud-financial-management:manage-and-control-aws-data-exports` | Manage and control - AWS Data Exports | Enables you to create billing and cost management data exports using basic SQL, and visualize data by integrating with Amazon Quick. |
| `guidance:cloud-financial-management:manage-and-control-aws-purchase-order-management` | Manage and control - AWS Purchase Order Management | Helps you to configure multiple purchase orders (POs), define how POs are mapped to their invoices, and access invoices generated against those POs. |
| `guidance:cloud-financial-management:optimize-and-save-amazon-ec2-reserved-instances` | Optimize and save - Amazon EC2 Reserved Instances | Offers a billing discount that provides savings on Amazon EC2 costs compared to on-demand instance pricing. |
| `guidance:cloud-financial-management:optimize-and-save-amazon-ec2-spot-instances` | Optimize and save - Amazon EC2 Spot Instances | Provides Amazon EC2 instances that use spare Amazon EC2 capacity that is available for less than the on-demand price. |
| `guidance:cloud-financial-management:optimize-and-save-amazon-s3-intelligent-tiering` | Optimize and save - Amazon S3 Intelligent-Tiering | Optimizes storage costs by automatically moving data to the most cost-effective access tier when access patterns change. |
| `guidance:cloud-financial-management:optimize-and-save-aws-compute-optimizer` | Optimize and save - AWS Compute Optimizer | Recommends optimal AWS compute resources for your workloads, helping you reduce costs and improve performance. |
| `guidance:cloud-financial-management:optimize-and-save-aws-cost-optimization-hub` | Optimize and save - AWS Cost Optimization Hub | Provides you with a comprehensive view of your cost optimization recommendations across your AWS Regions and AWS accounts within your organization. |
| `guidance:cloud-financial-management:optimize-and-save-savings-plans` | Optimize and save - Savings Plans | Provides a flexible pricing model that can help reduce your bill by up to 72% over on-demand prices. |
| `guidance:cloud-financial-management:plan-and-evaluate-aws-budgets` | Plan and evaluate - AWS Budgets | Provides improved planning and cost control with flexible budgeting and forecasting. |
| `guidance:cloud-financial-management:plan-and-evaluate-aws-organizations` | Plan and evaluate - AWS Organizations | Helps you centrally govern your AWS environment as you grow and scale on AWS. |
| `guidance:cloud-financial-management:plan-and-evaluate-aws-pricing-calculator` | Plan and evaluate - AWS Pricing Calculator | Provides a free web-based planning tool for creating cost estimates for using AWS services. |
| `guidance:cloud-financial-management:plan-and-evaluate-migration-evaluator` | Plan and evaluate - Migration Evaluator | Helps you create a directional business case for AWS Cloud planning and migration. |
| `guidance:cloud-financial-management:track-and-allocate-aws-billing-conductor` | Track and allocate - AWS Billing Conductor | Supports the showback and chargeback workflows of AWS Solution Providers and Enterprise customers. |
| `guidance:cloud-financial-management:track-and-allocate-aws-cost-and-usage-report` | Track and allocate - AWS Cost and Usage Report | Delivers cost and usage data to an S3 bucket where it can be integrated into other AWS tools or ERP for further analysis. |
| `guidance:cloud-financial-management:track-and-allocate-aws-cost-categories` | Track and allocate - AWS Cost Categories | Group accounts, tags, services, and charge types into meaningful categories with custom rules. |
| `guidance:cloud-financial-management:track-and-allocate-aws-cost-explorer` | Track and allocate - AWS Cost Explorer | Provides visualization of cost and utilization with default reports, and creates specific views with filters and grouping. |
| `guidance:compute:amazon-ec2` | Amazon EC2 | Launch and manage virtual servers. Design your ideal computing environment with virtual servers that you can configure to your exact specifications. Scale resources up or down based on demand, choose from hundreds of pre-configured options, and only pay for what you use. |
| `guidance:compute:container-services` | Container services | Run and manage containers. Deploy, manage, and scale containerized applications without worrying about the complexity of the underlying infrastructure. Container orchestration services handle tasks like scheduling, cluster management, and service discovery, allowing you to focus on application development rather than operational overhead. |
| `guidance:compute:cost-and-savings-optimization` | Cost and savings optimization | Helping you reduce your costs for your workloads. |
| `guidance:compute:deploy-and-manage-web-applications` | Deploy and manage web applications | Deploy and manage web applications with minimal management of the underlying infrastructure. Deploy and scale full-stack applications while minimizing infrastructure management tasks. This approach automates application operations and infrastructure provisioning, allowing development teams to concentrate on writing code and implementing business logic. |
| `guidance:compute:elastic-load-balancing` | Elastic Load Balancing | Increasing the availability and fault tolerance of your applications. |
| `guidance:compute:on-premises-and-edge-compute` | On-premises and edge compute | Allowing you to run familiar AWS interfaces to your premises and the edge, providing lower latency and local data processing needs. |
| `guidance:compute:process-large-volumes-of-data-in-parallel` | Process large volumes of data in parallel | Process large volumes of data in parallel. Run batch computing workloads efficiently without managing the underlying infrastructure. Computing resources are automatically provisioned based on volume and specific requirements of your jobs, with scheduling and resource allocation handled dynamically while you focus on analyzing results. |
| `guidance:compute:run-containers-on-aws-managed-compute` | Run containers on AWS managed compute | Deploy containerized applications without provisioning or managing the underlying servers. The compute resources automatically scale to match your application's needs, eliminating capacity planning and infrastructure management while you focus on container configuration and application code. |
| `guidance:compute:serverless-compute` | Serverless compute | Run code without having to provision or manage servers. Run your code in response to events without managing any infrastructure. Your code runs only when needed, and scales automatically from a few requests per day to thousands per second, with you paying only for the compute time consumed. |
| `guidance:containers:capacity` | Capacity | Use when you want to run your containers on self-managed or AWS managed compute. |
| `guidance:containers:on-premises` | On-premises | Use these services for flexibility in where you run your container-based applications. |
| `guidance:containers:orchestration` | Orchestration | Use when you need the capacity to deploy and manage up to thousands of containers. |
| `guidance:containers:tools-and-services-with-container-support` | Tools and services with container support | Use for supporting your container operations. |
| `guidance:containers:vertical-solutions` | Vertical solutions | Use when you or your team don't have a lot of experience with containers or infrastructure. |
| `guidance:databases:document` | Document | Use when you want to store JSON-like documents with rich querying abilities across the fields of the documents. |
| `guidance:databases:graph` | Graph | Use when you have to model complex networks of objects, such as social networks, fraud detection, and recommendation engine use cases. |
| `guidance:databases:in-memory` | In-memory | Use Amazon ElastiCache when you need a caching layer to improve read performance. Use Amazon MemoryDB when you need full data persistence, but still need sub-millisecond read latencies. |
| `guidance:databases:key-value` | Key-value | Use for workloads such as session stores or shopping carts. Key-value databases can scale to large amounts of data and extremely high throughput of requests, while servicing millions of simultaneous users through distributed processing and storage. |
| `guidance:databases:relational` | Relational | Use when you're migrating or modernizing an on-premises relational workload, or if your workload has less predictable query patterns. |
| `guidance:databases:time-series` | Time series | Use when you have a large amount of time series data, potentially from a number of sources, such as Internet of Things (IoT) data, application metrics, and asset tracking. |
| `guidance:databases:wide-column` | Wide-column | Use when you need to migrate your on-premises Apache Cassandra workloads, or when you need to process data at high speeds for applications that require single-digit millisecond latency. |
| `guidance:frontend-web-and-mobile:deliver` | Deliver | Deliver your app using a git-based workflow. Connect private and public repositories from GitHub, BitBucket, GitLab, and AWS CodeCommit. |
| `guidance:frontend-web-and-mobile:develop` | Develop | Develop your Android, iOS, or web app with features such as authentication, on-device and cloud storage, analytics, AI/ML, IoT, and notifications. Natively integrate with other AWS services. |
| `guidance:frontend-web-and-mobile:develop-graphql-apis` | Develop - GraphQL APIs | Use GraphQL APIs, and choose from supported frameworks and protocols. |
| `guidance:frontend-web-and-mobile:develop-rest-apis` | Develop - REST APIs | Use REST APIs, and choose from supported frameworks and protocols. |
| `guidance:frontend-web-and-mobile:engage-location-and-geospatial` | Engage - Location and geospatial | Use maps, points of interest, geocoding, and tracking to target your audience. |
| `guidance:frontend-web-and-mobile:engage-user-journeys-and-analytics` | Engage - User journeys and analytics | Engage with customers by designing user journeys and analyzing user behavior. |
| `guidance:frontend-web-and-mobile:monitor` | Monitor | Monitor app health with traces, metrics, logs, alarms, and other resources. |
| `guidance:frontend-web-and-mobile:test` | Test | Test your app by using automated testing or remote access. |
| `guidance:migration-and-transfer:assess-and-mobilize` | Assess and mobilize | These services are optimized to accelerate decision-making and discover on-premises assets to plan your AWS migration. |
| `guidance:migration-and-transfer:hybrid-cloud-storage` | Hybrid cloud storage | These services are optimized for on-premises applications that require low-latency data access or rapid data transfer to the cloud. |
| `guidance:migration-and-transfer:migrate-your-applications` | Migrate your applications | These services are optimized to simplify and expedite the migration on on-premises servers and databases to AWS. |
| `guidance:migration-and-transfer:online-data-transfer` | Online data transfer | These services are optimized to make it simple and easy to transfer your data into and out of AWS via online methods. |
| `guidance:ml-and-ai:infrastructure` | Infrastructure | Use when you want to achieve the lowest cost for training models and need to run inference in the cloud. |
| `guidance:ml-and-ai:ml-services` | ML services | Use these services when when you need more customized machine learning models or workflows that go beyond the pre-built functionalities offered by the core AI services. |
| `guidance:ml-and-ai:specific-use-cases` | Specific use cases | Use the AI services provided by AWS when you require specific, pre-built functionalities to be integrated into your applications, without the need for extensive customizations or machine learning expertise. These services are designed to be easy to use and do not require much coding or configuration. |
| `guidance:ml-and-ai:tools-and-associated-services` | Tools and associated services | These services and tools are designed to help you accelerate deep learning in the cloud, providing Amazon machine images, docker images and entity resolution. |
| `guidance:networking-and-content-delivery:application-networking` | Application networking | Optimized to ensure that your workloads are highly available, adapt to demand, and can communicate with each other. |
| `guidance:networking-and-content-delivery:edge-networking-and-content-delivery` | Edge networking and content delivery | Optimized for low latency, reliable traffic routing to and from your workloads. |
| `guidance:networking-and-content-delivery:global-and-hybrid-connectivity` | Global and hybrid connectivity | Optimized to ensure private, secure, and global network connectivity. |
| `guidance:networking-and-content-delivery:network-foundations` | Network foundations | Optimized for getting started with AWS networking services and connecting your VPCs securely. |
| `guidance:networking-and-content-delivery:network-security-and-remote-access` | Network security and remote access | Optimized to protect your workloads against malware, DDoS, SQL injection, and cross-site scripting attacks. |
| `guidance:security-identity-and-compliance:data-protection-cryptographic-keys` | Data Protection - Cryptographic Keys | Use these services to help you achieve and maintain the confidentiality, integrity, and availability of sensitive data stored and processed within AWS environments. |
| `guidance:security-identity-and-compliance:data-protection-hardware-security-modules` | Data Protection - Hardware Security Modules | Use these services to help you achieve and maintain the confidentiality, integrity, and availability of sensitive data stored and processed within AWS environments. |
| `guidance:security-identity-and-compliance:data-protection-payment-cryptography` | Data Protection - Payment Cryptography | Use these services to help you achieve and maintain the confidentiality, integrity, and availability of sensitive data stored and processed within AWS environments. |
| `guidance:security-identity-and-compliance:data-protection-private-certificate-authority` | Data Protection - Private Certificate Authority | Use these services to help you achieve and maintain the confidentiality, integrity, and availability of sensitive data stored and processed within AWS environments. |
| `guidance:security-identity-and-compliance:data-protection-secrets-management` | Data Protection - Secrets Management | Use these services to help you achieve and maintain the confidentiality, integrity, and availability of sensitive data stored and processed within AWS environments. |
| `guidance:security-identity-and-compliance:data-protection-sensitive-data-discovery` | Data Protection - Sensitive Data Discovery | Use these services to help you achieve and maintain the confidentiality, integrity, and availability of sensitive data stored and processed within AWS environments. |
| `guidance:security-identity-and-compliance:data-protection-ssl-tls-certificates` | Data Protection - SSL/TLS Certificates | Use these services to help you achieve and maintain the confidentiality, integrity, and availability of sensitive data stored and processed within AWS environments. |
| `guidance:security-identity-and-compliance:detection-and-response-audit-trail-logging` | Detection and Response - Audit Trail Logging | Use these services to help you detect and respond to security risks across your accounts, so you can protect your workloads at scale. |
| `guidance:security-identity-and-compliance:detection-and-response-incident-response` | Detection and Response - Incident Response | Use these services to help you detect and respond to security risks across your accounts, so you can protect your workloads at scale. |
| `guidance:security-identity-and-compliance:detection-and-response-resource-configuration` | Detection and Response - Resource Configuration | Use these services to help you detect and respond to security risks across your accounts, so you can protect your workloads at scale. |
| `guidance:security-identity-and-compliance:detection-and-response-security-alerts-centralization` | Detection and Response - Security Alerts Centralization | Use these services to help you detect and respond to security risks across your accounts, so you can protect your workloads at scale. |
| `guidance:security-identity-and-compliance:detection-and-response-security-data-centralization` | Detection and Response - Security Data Centralization | Use these services to help you detect and respond to security risks across your accounts, so you can protect your workloads at scale. |
| `guidance:security-identity-and-compliance:detection-and-response-security-investigation` | Detection and Response - Security Investigation | Use these services to help you detect and respond to security risks across your accounts, so you can protect your workloads at scale. |
| `guidance:security-identity-and-compliance:detection-and-response-threat-detection` | Detection and Response - Threat Detection | Use these services to help you detect and respond to security risks across your accounts, so you can protect your workloads at scale. |
| `guidance:security-identity-and-compliance:detection-and-response-vulnerability-management` | Detection and Response - Vulnerability Management | Use these services to help you detect and respond to security risks across your accounts, so you can protect your workloads at scale. |
| `guidance:security-identity-and-compliance:governance-and-compliance-audit-management` | Governance and Compliance - Audit Management | Use these services to help you implement best practices and meet industry standards when using AWS. |
| `guidance:security-identity-and-compliance:governance-and-compliance-compliance-documentation` | Governance and Compliance - Compliance Documentation | Use these services to help you implement best practices and meet industry standards when using AWS. |
| `guidance:security-identity-and-compliance:governance-and-compliance-multi-account-environment-setup` | Governance and Compliance - Multi-Account Environment Setup | Use these services to help you implement best practices and meet industry standards when using AWS. |
| `guidance:security-identity-and-compliance:governance-and-compliance-multi-account-management` | Governance and Compliance - Multi-Account Management | Use these services to help you implement best practices and meet industry standards when using AWS. |
| `guidance:security-identity-and-compliance:identity-and-access-management` | Identity and Access Management | Use these services to help you securely manage and govern access for your customers, workforce, and workloads. |
| `guidance:security-identity-and-compliance:identity-and-access-management-fine-grained-access-control` | Identity and Access Management - Fine-Grained Access Control | Use these services to help you securely manage and govern access for your customers, workforce, and workloads. |
| `guidance:security-identity-and-compliance:identity-and-access-management-resource-sharing` | Identity and Access Management - Resource Sharing | Use these services to help you securely manage and govern access for your customers, workforce, and workloads. |
| `guidance:security-identity-and-compliance:identity-and-access-management-web-and-mobile-apps` | Identity and Access Management - Web and Mobile Apps | Use these services to help you securely manage and govern access for your customers, workforce, and workloads. |
| `guidance:security-identity-and-compliance:network-and-application-protection-ddos-protection` | Network and Application Protection - DDoS Protection | Use these services to help you enforce detailed security policies at every network control point. |
| `guidance:security-identity-and-compliance:network-and-application-protection-firewall-management` | Network and Application Protection - Firewall Management | Use these services to help you enforce detailed security policies at every network control point. |
| `guidance:security-identity-and-compliance:network-and-application-protection-network-firewall` | Network and Application Protection - Network Firewall | Use these services to help you enforce detailed security policies at every network control point. |
| `guidance:security-identity-and-compliance:network-and-application-protection-web-application-firewall` | Network and Application Protection - Web Application Firewall | Use these services to help you enforce detailed security policies at every network control point. |
| `guidance:storage:block` | Block | Applications requiring low-latency, high-performance durable storage attached to single Amazon EC2 instances or containers, such as databases and general-purpose local instance storage. |
| `guidance:storage:cache` | Cache | Fully managed, scalable, and high-speed cache on AWS for processing file data stored in disparate locations—including on premises NFS file systems, and/or in cloud file systems (Amazon FSx for OpenZFS, Amazon FSx for NetApp ONTAP), and Amazon S3. |
| `guidance:storage:file-system` | File system | Applications and workloads requiring shared read and write access across multiple Amazon EC2 instances or containers, or from multiple on-prem servers, such as team file shares, highly-available enterprise applications, analytics workloads, and ML training. |
| `guidance:storage:hybrid-edge` | Hybrid/Edge | Deliver low-latency data to on-premises applications and providing on-premises applications access to cloud-backed storage. |
| `guidance:storage:object` | Object | Read-heavy workloads such as content distribution, web hosting, big data analytics, and ML workflows. Well-suited for scenarios where data needs to be stored, accessed, and distributed globally over the internet. |
| `guidance:storage:offline-migration` | Offline migration | Suitable for one-time or periodic uploads - and when data can be static in transit. This choice makes sense when you need to use only the minimum available bandwidth - and you prefer the predictability of physical moves. |
| `guidance:storage:online-migration` | Online migration | Online is optimized for frequent updates to data. Use it for time-critical or ongoing workloads. Consider scheduling your transfer during off hours when you have sufficient bandwidth. |
