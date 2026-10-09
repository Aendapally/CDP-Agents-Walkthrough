# Signal Secure Messaging on AWS: Architecture Summary

**Requirements:** [requirements.json](requirements.json) · **Architecture as code:** [signal_secure_messaging_on_aws.yaml](signal_secure_messaging_on_aws.yaml) · **Diagram:** [signal_secure_messaging_on_aws.png](signal_secure_messaging_on_aws.png)

## Overview

A regulated broker-dealer wants a sanctioned secure messenger for its 25,000 employees, so that business conversations stop moving to consumer apps that compliance cannot see. This design self-hosts the open-source Signal stack on AWS as a closed, firm-only service. It keeps Signal's end-to-end encryption and adds what a regulated firm needs on top: identity tied to the corporate directory, capture of every business communication into a write-once archive, and a warm standby region.

The central tension is that end-to-end encryption and record-keeping pull in opposite directions. The servers never see plaintext, so the firm cannot capture messages on the server. The design resolves this at the endpoint and isolates everything that can decrypt the archive in a separate AWS account.

## Scope and assumptions

- 25,000 employees, up to three linked devices each; peak 20,000 concurrent connections; 2 million messages per day.
- Closed user group: only employees register, and the service does not federate with the public Signal network.
- Every device is enrolled in the firm's mobile device management.
- Consumer payments, donations and billing are out of scope. So is call recording, so calling is disabled for roles subject to call-taping rules.

## Architecture at a glance

- **Edge:** Route 53 failover records; an Application Load Balancer for WebSocket and gRPC traffic behind AWS WAF and Shield Advanced; CloudFront for attachment downloads; a UDP Network Load Balancer for group calls.
- **Services (Amazon EKS, private subnets, three AZs):** chat (Signal-Server), registration, storage, key transparency, the firm directory and the attachment upload (tus) service.
- **Calling:** the group-call SFU (Signal-Calling-Service) and TURN relays on EC2 in public subnets.
- **Data:** DynamoDB (34 tables), ElastiCache for Redis (five endpoints), FoundationDB on EC2 for message queues, and S3 for attachments, prekeys and dynamic configuration.
- **Compliance account:** capture API, SQS FIFO buffer, archive processor, S3 Object Lock archive and a CloudHSM-backed archive key.
- **Region B:** warm standby with DynamoDB global tables, FoundationDB DR, replicated S3 buckets and a scaled-down EKS cluster.

## Key decisions

| # | Decision | Satisfies | Grounded in | Alternatives considered |
|---|---|---|---|---|
| D1 | Run the open-source stack as a closed deployment on Amazon EKS across three AZs | FR-01, FR-04, NFR-AVL-01 | SIG-01; component inventory | ECS on Fargate is viable. EKS runs seven services and their autoscaling on one platform. |
| D2 | Capture at the endpoint: a firm-managed client journals each sent and received message, encrypted to the archive key, into an isolated compliance account. Records land in S3 Object Lock (compliance mode, 7 years). Only the archive processor's role can use the CloudHSM-backed key. | FR-05, FR-06, NFR-CMP-01 to 04, NFR-SEC-02 | CON-01; SEC Rule 17a-4 | Server-side capture is impossible with end-to-end encryption. A third-party archiving fork of Signal was rejected: in 2025 one such product was breached and its archive server held plaintext copies. |
| D3 | Replace SGX-based contact discovery with a firm directory service fed by SCIM, and disable PIN-based secure value recovery | FR-07, NFR-DAT-01 | SIG-08, CON-03 | Port the enclave services to AWS Nitro Enclaves. This takes significant effort; revisit if the user group opens up. |
| D4 | Port the storage service from Google Cloud Bigtable to DynamoDB | NFR-DAT-01 | SIG-09, CON-04 | Keep Bigtable over a cross-cloud link. That adds a second provider and a data-residency review. |
| D5 | DynamoDB on demand with point-in-time recovery for accounts, keys and profiles; global tables to Region B | NFR-DR-01, NFR-SCL-01 | SIG-02 | Aurora PostgreSQL would mean rewriting the data layer. |
| D6 | FoundationDB on EC2 i4i instances (local NVMe) across three AZs in triple-redundancy mode, with asynchronous DR replication to Region B | NFR-AVL-01, NFR-DR-01 | SIG-04 | The DynamoDB `messages` table that still appears in the config. The current server build requires FoundationDB. |
| D7 | ElastiCache for Redis OSS, Multi-AZ: four cluster-mode groups plus one non-clustered group for pub/sub, matching upstream | NFR-PRF-01, NFR-AVL-01 | SIG-03, SIG-05 | One shared cluster: fewer nodes, but a larger blast radius and noisy neighbours. |
| D8 | The tus upload service writes client-encrypted blobs to S3 (SSE-KMS). Downloads go through CloudFront signed URLs with origin access control. Buckets replicate to Region B. | FR-03, NFR-DR-01 | SIG-06 | Upstream's GCS and Cloudflare R2 backends would add a second provider. |
| D9 | Route 53 failover; an ALB for WebSocket and gRPC behind WAF and Shield Advanced; a UDP NLB for the SFU; TURN relays on EC2 with Elastic IPs | FR-02, NFR-AVL-01 | SIG-01, SIG-11 | Cloudflare TURN as upstream, which puts a third party in the media path. |
| D10 | Remove consumer monetisation: Stripe, Braintree, app-store billing, MobileCoin and donations are not deployed | NFR-DAT-01 | SIG-10 | None |
| D11 | Egress only through AWS Network Firewall with a domain allowlist (APNs, FCM, the IdP); VPC endpoints for AWS services | NFR-NET-01 | SIG-07 | Open NAT egress |
| D12 | Registration gated by corporate SSO (OIDC), with SCIM-driven deprovisioning within 15 minutes | FR-04, NFR-SEC-03 | Registration service in the component inventory | SMS-only verification as upstream |
| D13 | Warm standby in Region B with Route 53 failover | NFR-DR-01 | NFR-DR-01 targets | Active-active. FoundationDB queues and the Redis message cache make multi-writer complex. |

## How a message flows

1. A device resolves the service through Route 53 and holds a WebSocket open to the chat service through WAF and the ALB.
2. The sender's client encrypts the message with the Signal protocol and sends it. The chat service queues it for each recipient device in the Redis message cache, which persists to FoundationDB after one minute.
3. Online recipients get it immediately over their WebSocket via Redis pub/sub. Offline devices get an APNs or FCM wake-up through the egress firewall and fetch the message when they reconnect.
4. Attachments are encrypted on the device, uploaded through the tus service to S3 and downloaded through CloudFront.
5. The managed client also journals the message, encrypted to the archive key, to the capture API in the compliance account. SQS FIFO buffers it and the archive processor writes the original to the WORM archive. Inside the isolated account it then decrypts a copy and exports it to the approved surveillance platform. People can reach raw archive content only with two-person approval.

## Well-Architected review

- **Security:** end-to-end encryption is preserved. Customer-managed KMS keys everywhere; the archive key sits in a separate account with a CloudHSM key store. Least-privilege pod roles, no SSH, an egress allowlist, and WAF with Shield Advanced at the edge.
- **Reliability:** every tier spans three AZs, including the Redis message cache (messages persist only after a minute). Warm standby in Region B meets RPO 5 minutes and RTO 1 hour.
- **Performance efficiency:** Redis pub/sub fans messages out to open WebSockets, FoundationDB absorbs queue writes, and CloudFront serves attachments close to users.
- **Cost optimisation:** DynamoDB on demand, Graviton node groups, a scaled-down standby region, and no consumer billing or payment services.
- **Operational excellence:** OpenTelemetry to CloudWatch and X-Ray with log scrubbing tested in CI; everything defined as code; upstream releases tracked monthly.
- **Sustainability:** Graviton instances and demand-based scaling in the standby region.

## Policy check and exceptions requested

| ID | Request | Why | Compensating controls | Approvers |
|---|---|---|---|---|
| EXC-01 | Run a modified, firm-managed client that journals messages for the archive | End-to-end encryption prevents server-side capture (CON-01) | Separate compliance account; archive key usable only by the archive processor; two-person approval for human access; no plaintext stored outside the isolated account; annual penetration test of the client and archive | CISO, Chief Compliance Officer |
| EXC-02 | Operate self-managed FoundationDB | No managed equivalent on AWS, and the current server requires it | Triple redundancy across three AZs, continuous backup to S3, DR cluster, runbooks and on-call cover | Platform engineering lead, CCoE |
| EXC-03 | Maintain forks of AGPL-licensed components (client journaling, SSO registration, DynamoDB storage) | Needed for D2, D4 and D12 | Offer modified source to users as AGPL section 13 requires; merge upstream security fixes within 7 days | Legal, open-source program office |

## Risks and open items

- A compromised device exposes plaintext; this is inherent to end-to-end encryption. Device management and attestation reduce the risk but do not remove it.
- Fork maintenance: the client and three server components diverge from upstream, so security fixes must be merged quickly (EXC-03).
- FoundationDB and the Bigtable-to-DynamoDB port need specialist skills and are the largest engineering items.
- Open questions: the surveillance platform and export format (Q-01), roles that must have calling disabled (Q-02), and AGPL obligations (Q-03).
- An encryption export-control review is needed in each country of operation (NFR-CMP-05).

## Sources

- Signal-Server at commit `a15d5cdbb937`: [service/config/sample.yml](https://github.com/signalapp/Signal-Server/blob/main/service/config/sample.yml), [README](https://github.com/signalapp/Signal-Server)
- Companion services: [registration-service](https://github.com/signalapp/registration-service), [storage-service](https://github.com/signalapp/storage-service), [key-transparency-server](https://github.com/signalapp/key-transparency-server), [Signal-Calling-Service](https://github.com/signalapp/Signal-Calling-Service), [ContactDiscoveryService-Icelake](https://github.com/signalapp/ContactDiscoveryService-Icelake), [SecureValueRecovery2](https://github.com/signalapp/SecureValueRecovery2)
- [SEC Rule 17a-4](https://www.ecfr.gov/current/title-17/section-240.17a-4), [FINRA Rule 3110](https://www.finra.org/rules-guidance/rulebooks/finra-rules/3110), [FINRA Rule 4511](https://www.finra.org/rules-guidance/rulebooks/finra-rules/4511)
- [TechCrunch: TeleMessage, a modified Signal clone used by US government officials, has been hacked (May 2025)](https://techcrunch.com/2025/05/05/telemessage-a-modified-signal-clone-used-by-us-government-officials-has-been-hacked/)
