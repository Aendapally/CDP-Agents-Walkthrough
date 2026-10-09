# Dify AI Agent Platform on AWS: Architecture Summary

**Requirements:** [requirements.json](requirements.json) · **Architecture as code:** [dify_ai_agent_platform_on_aws.yaml](dify_ai_agent_platform_on_aws.yaml) · **Diagram:** [dify_ai_agent_platform_on_aws.png](dify_ai_agent_platform_on_aws.png)

## Overview

The firm wants one internal platform where teams build chat assistants, RAG apps, workflows and agents over internal documents. Every model call must go through Amazon Bedrock in-region, and every prompt, response and tool call is kept for audit. Dify provides the builder experience. AWS publishes a CDK deployment of Dify that is a good starting point, but it is sized for a small team and keeps convenience defaults that an enterprise cannot accept.

This design keeps the sample's shape (ECS on Fargate, Aurora with pgvector, Valkey, S3, Bedrock over VPC endpoints) and changes four things. It isolates the code sandbox from the API's credentials. It forces every outbound request through two layers of egress control. It adds a write-once audit trail. And it settles the licensing question that SSO and per-team workspaces raise.

## Scope and assumptions

- 5,000 employees, 800 peak concurrent sessions, about 200 published apps in 40 team workspaces.
- Knowledge bases total up to 2 million chunks.
- Access only from the corporate network; no public endpoint.
- Fine-tuning, self-hosted models and customer-facing apps are out of scope.

## Architecture at a glance

- **Private edge:** Direct Connect or VPN from the corporate network, AWS WAF, and an internal ALB with TLS.
- **Application (ECS on Fargate):** web (Next.js), API with websocket streaming, Celery workers and beat, and the plugin daemon.
- **Isolated subnet:** the DifySandbox service, with no IAM role and no direct egress.
- **Egress control:** the Squid SSRF proxy, then AWS Network Firewall with a domain allowlist, then NAT.
- **Models:** Amazon Bedrock (LLMs, embeddings, Guardrails) through VPC interface endpoints.
- **Data:** Aurora PostgreSQL for app and plugin metadata, a separate Aurora cluster with pgvector for knowledge bases, ElastiCache Valkey, and S3.
- **Governance:** an S3 audit bucket with Object Lock, Athena, CloudWatch, KMS and Secrets Manager, and SES.
- **Region B:** Aurora snapshot copies and an S3 replica.

## Key decisions

| # | Decision | Satisfies | Grounded in | Alternatives considered |
|---|---|---|---|---|
| D1 | Start from AWS's Dify CDK sample and harden it rather than design from scratch | NFR-AVL-01, NFR-SCL-01 | SIG-09 | EKS: nothing here is stateful enough to justify running Kubernetes |
| D2 | Move the sandbox out of the API task into its own Fargate service in an isolated subnet: no task role, and a security group that only reaches the SSRF proxy. Keep seccomp and the 15-second limit. | FR-04, NFR-SEC-03 | SIG-05, SIG-10, CON-02 | Keep the sample's sidecar layout, where user code shares the API's network namespace and its S3 and Bedrock permissions |
| D3 | Run the plugin daemon as its own service with a narrow role, installing plugins only from an internal, reviewed catalogue | FR-05 | SIG-07 | Open marketplace installs |
| D4 | Two layers of egress control: the SSRF proxy blocks internal ranges and the metadata endpoint, then Network Firewall enforces a domain allowlist | NFR-NET-01 | SIG-06 | Either layer alone |
| D5 | Bedrock is the only model provider, reached through VPC endpoints. An SCP allows only approved model ARNs. Guardrails apply to every production app, through the provider settings where supported or a mandatory guardrail step in workflows. | NFR-SEC-01, NFR-SEC-04 | SIG-09 | External model APIs through the egress proxy (rejected by policy) |
| D6 | Provisioned Aurora PostgreSQL (Multi-AZ, Graviton) for app and plugin metadata, plus a separate Aurora cluster with pgvector for knowledge bases | NFR-AVL-01, NFR-SCL-01 | SIG-04, SIG-09 | The sample's Aurora Serverless v2 capped at 2 ACU (too small). Amazon OpenSearch for vectors; revisit beyond about 10 million chunks or if hybrid search is needed. |
| D7 | ElastiCache Valkey, Multi-AZ with TLS and AUTH, sized up from the sample's `cache.t4g.micro` | NFR-AVL-01 | SIG-02, SIG-09 | None |
| D8 | On-demand Fargate for web and API; Fargate Spot only for Celery workers | NFR-AVL-01, NFR-CST-01 | SIG-09 (the sample defaults to Spot) | Spot everywhere, as in the sample |
| D9 | License Dify Enterprise for SAML/OIDC SSO and per-team workspaces | NFR-SEC-02, FR-03 | SIG-11, SIG-12, CON-01 | Community edition behind ALB OIDC authentication: one shared workspace, no per-team isolation, and a licence breach if teams get separate workspaces |
| D10 | Audit trail: Bedrock model invocation logging plus a scheduled export of conversations and tool calls into an S3 bucket with Object Lock (1 year), queried with Athena | NFR-CMP-01 | Scenario | CloudWatch Logs only, which gives no write-once guarantee |
| D11 | Private access only: an internal ALB with WAF, reached over Direct Connect or VPN | NFR-SEC-02 | The sample supports an internal ALB | CloudFront with public access, the sample's default |
| D12 | Each workspace's Bedrock provider uses its own application inference profile, tagged for cost allocation, with AWS Budgets alerts at 80% | NFR-CST-01 | Scenario | Account-level spend only |
| D13 | Keep the agent backend's shell sandbox off at launch | NFR-SEC-03 | SIG-08, CON-03 | Enable it with the default local sandbox |
| D14 | Aurora point-in-time recovery with cross-region snapshot copies, S3 replication, and redeployment from CDK in Region B | NFR-DR-01 | NFR-DR-01 targets | A warm standby, which an RTO of 4 hours does not need |

## How a request flows

1. An employee opens the console or a published app over the corporate network. WAF and the internal ALB send UI traffic to the web service and API calls to the API service; sign-in goes through the corporate IdP.
2. For a chat request, the API retrieves matching chunks from pgvector, calls the model in Bedrock through the VPC endpoint, and streams the answer back.
3. HTTP tool calls leave through the SSRF proxy and Network Firewall, and only to allowlisted APIs. Code nodes run in the isolated sandbox service, which has no credentials and can reach only the proxy.
4. Uploaded documents land in S3. Celery workers chunk them, create embeddings with Bedrock and index them in pgvector.
5. Bedrock invocation logs and the scheduled conversation export land in the write-once audit bucket, where Athena supports investigations.

## Well-Architected review

- **Security:** user code is isolated from credentials. Egress has two layers of control and models are reached only privately. Guardrails screen prompts and responses, and every store uses customer-managed keys. There is no public endpoint.
- **Reliability:** every service runs in three AZs; Aurora and Valkey are Multi-AZ; backups are copied to Region B.
- **Performance efficiency:** responses stream over websockets, and vector search stays in a separate Aurora cluster so indexing does not slow app metadata queries.
- **Cost optimisation:** Spot for workers, Graviton for Aurora, and per-workspace model spend with budgets.
- **Operational excellence:** CloudWatch alarms on Celery queue depth and task failures; the stack is CDK code derived from AWS's sample.
- **Sustainability:** serverless containers sized to demand.

## Policy check and exceptions requested

| ID | Request | Why | Compensating controls | Approvers |
|---|---|---|---|---|
| EXC-01 | Commercial Dify Enterprise licence | SSO and per-team workspaces (CON-01) | Vendor risk assessment; contract covers security patches and support response times; software runs in the firm's account, so no data goes to the vendor | Procurement, CISO |
| EXC-02 | Execute user-supplied code on the platform | Code nodes are central to workflows (FR-04, CON-02) | A separate task with no IAM role, an isolated subnet, egress only through the proxy, seccomp, a 15-second limit, and code nodes restricted to approved builders | CISO |

## Risks and open items

- Prompt injection through documents and tool outputs. Guardrails reduce the risk but do not remove it, so actions with side effects should need human approval.
- Knowledge bases are separated per workspace, but per-document access control that mirrors the source systems is not designed yet (Q-01).
- Dify releases frequently, and its licence lets the producer change terms. Keep upgrades current and keep Legal watching the licence.
- The tool egress allowlist still needs an owner and a review process (Q-02).

## Sources

- langgenius/dify at commit `6d14cd586132`: [docker/docker-compose.yaml](https://github.com/langgenius/dify/blob/main/docker/docker-compose.yaml), [docker/.env.example](https://github.com/langgenius/dify/blob/main/docker/.env.example), [LICENSE](https://github.com/langgenius/dify/blob/main/LICENSE)
- AWS reference: [aws-samples/dify-self-hosted-on-aws](https://github.com/aws-samples/dify-self-hosted-on-aws) at commit `a589e3e5e83b`
- [Dify Enterprise: SSO authentication](https://enterprise-docs.dify.ai/en/3.13.x/administer/sso/introduction)
- Amazon Bedrock: [model invocation logging](https://docs.aws.amazon.com/bedrock/latest/userguide/model-invocation-logging.html), [inference profiles](https://docs.aws.amazon.com/bedrock/latest/userguide/inference-profiles.html)
