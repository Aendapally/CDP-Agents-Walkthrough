# Sentry Observability Platform on AWS: Architecture Summary

**Requirements:** [requirements.json](requirements.json) · **Architecture as code:** [sentry_observability_platform_on_aws.yaml](sentry_observability_platform_on_aws.yaml) · **Diagram:** [sentry_observability_platform_on_aws.png](sentry_observability_platform_on_aws.png)

## Overview

An engineering organisation with 3,000 engineers and 2,500 projects wants Sentry for errors, tracing, replays and profiling. The telemetry carries customer data and source context, so it must stay in the firm's own AWS accounts and in EU regions. Sentry's self-hosted package is a single-host Docker Compose stack of 53 services, and upstream describes it as suited to "low-volume deployments and proofs-of-concept". This design turns that topology into a production platform that absorbs 25,000 events per second during incident storms without losing accepted events.

The design follows the stack's own data path: Relay authenticates and scrubs events at the edge, Kafka buffers everything, and consumers fan the data out to ClickHouse for search, S3 for raw payloads and files, and PostgreSQL for issue metadata.

## Scope and assumptions

- 5,000 events per second sustained, 25,000 per second for up to 30 minutes, and about 2 TB per month of attachments, replays and profiles.
- One EU region in production; backups are copied to a second EU region.
- Single-organisation install (`SENTRY_SINGLE_ORGANIZATION`), with teams mapped from the corporate IdP.
- SaaS-only Sentry features and multi-region active-active operation are out of scope.

## Architecture at a glance

- **Edge:** a public ALB behind AWS WAF for browser and mobile SDKs; a PrivateLink endpoint service for workloads in other AWS accounts; an internal ALB for the UI, API and CI uploads.
- **Ingest (EKS):** the Relay fleet authenticates DSNs, enforces quotas and rate limits in Redis, scrubs PII and writes to Kafka.
- **Streaming:** Amazon MSK across three AZs (replication factor 3) carries ingest, Snuba and task topics.
- **Processing (EKS):** ingest consumers, 18 Snuba consumers, post-process forwarders, the taskbroker and task workers, Symbolicator, and the uptime and cron monitors. All of them scale on Kafka lag.
- **Web and query (EKS):** the Sentry web app and API, the Snuba query API and Vroom for profiles.
- **ClickHouse (EKS):** shards with two replicas each, three ClickHouse Keeper nodes, and S3 for cold parts and backups.
- **Data:** Aurora PostgreSQL behind RDS Proxy, ElastiCache for Redis and Memcached, and S3 for the nodestore and files.
- **Operations:** KMS and Secrets Manager, CloudWatch with consumer-lag alarms, AWS Backup, Amazon SES, and egress through Network Firewall to Slack, PagerDuty and Jira.

## Key decisions

| # | Decision | Satisfies | Grounded in | Alternatives considered |
|---|---|---|---|---|
| D1 | Run the services on Amazon EKS, with Karpenter for nodes and KEDA scaling consumers on Kafka lag | NFR-SCL-01, NFR-OPS-01 | SIG-01 | ECS on Fargate handles the stateless services well, but ClickHouse needs StatefulSets and the operator. One platform is simpler to run. |
| D2 | Two ingest paths: a public ALB with AWS WAF for browser and mobile SDKs, and a PrivateLink endpoint service for internal producers | FR-06, NFR-NET-01 | CON-02 | Route all traffic over the internet (rejected by policy) |
| D3 | Relay as the only writer to Kafka, enforcing DSN auth, quotas and PII scrubbing before data is stored | NFR-SEC-02, NFR-SCL-01 | Relay component; SIG-07 | Scrubbing after storage would mean PII lands on disk first |
| D4 | Amazon MSK provisioned across three AZs: replication factor 3, min in-sync replicas 2, producer acks=all, message size raised to match Sentry's 50 MB setting | NFR-DUR-01, NFR-AVL-01 | SIG-02, CON-03 | Self-managed Kafka on EKS (more to operate, and firm policy prefers managed) |
| D5 | Self-managed ClickHouse on EKS with the Altinity operator: shards with two replicas across AZs, three Keeper nodes, data older than 7 days moved to S3, TTL-based retention | NFR-PRF-02, NFR-RET-01, NFR-CST-01 | SIG-03, CON-01 | Hosted ClickHouse service (telemetry would leave the firm's accounts); EC2 without an operator (more manual work) |
| D6 | Aurora PostgreSQL (Multi-AZ, Graviton) behind RDS Proxy instead of PgBouncer | NFR-AVL-01, NFR-DR-01 | SIG-04 | Self-managed PostgreSQL on EKS |
| D7 | Raw event payloads (nodestore) and all files (attachments, replays, profiles, debug files) in S3, with lifecycle expiry at 90 and 30 days | NFR-RET-01, NFR-CST-01 | SIG-05, SIG-06, SIG-09 | Nodestore in PostgreSQL, which upstream moved away from because it did not scale |
| D8 | ElastiCache for Redis OSS (Multi-AZ) for quotas, rate limits, buffers and digests; ElastiCache for Memcached for the Django cache | NFR-PRF-01, NFR-AVL-01 | SIG-07, SIG-08 | A single Redis for everything, which mixes cache evictions with quota state |
| D9 | The Kafka-backed taskbroker and task workers handle background work, with no separate message broker | NFR-SCL-01 | SIG-10 | Celery with RabbitMQ (no longer the upstream path) |
| D10 | SAML SSO with the corporate IdP; email through Amazon SES; Slack, PagerDuty and Jira reached only through the Network Firewall allowlist | FR-02, FR-05, NFR-SEC-03 | SIG-12, SIG-14 | Local accounts and open egress |
| D11 | All stores encrypted with customer-managed KMS keys; one production region; backups copied only to another EU region | NFR-SEC-01, NFR-DR-01 | Scenario | Cross-region replicas of live data (more cost, with no matching RTO need) |
| D12 | Platform self-monitoring: CloudWatch Container Insights, MSK consumer-lag alarms, StatsD metrics through the CloudWatch agent | NFR-OPS-01 | SIG-13 | Monitoring Sentry with Sentry (it fails at the same moment) |

## How an event flows

1. An SDK sends an event to Relay, either over the public endpoint behind WAF or privately over PrivateLink.
2. Relay checks the DSN and project quotas in Redis, scrubs PII, and writes the event to an `ingest-*` topic on MSK.
3. Ingest consumers process the event, call Symbolicator when stack traces need symbols, store the raw payload in the S3 nodestore, and update issues and groups in Aurora.
4. Snuba consumers batch the processed events into ClickHouse. Post-process forwarders trigger alert rules, and task workers send email through SES and notifications through the egress firewall.
5. Engineers search and build dashboards through the web app. Its queries go through the Snuba API to ClickHouse, and attachments, replays and profiles come from S3.

## Well-Architected review

- **Security:** PII is scrubbed at the edge, before storage. Internal producers use PrivateLink. WAF rate limits protect the public endpoint, every store uses customer-managed keys, sign-in is SSO-only, and egress goes through an allowlist.
- **Reliability:** Kafka absorbs storms (replication factor 3, acks=all). Every tier spans three AZs, consumers catch up from Kafka after failures, and backups are copied to a second EU region.
- **Performance efficiency:** consumers scale on lag; ClickHouse serves search and dashboards; Relay rejects over-quota traffic before it costs anything downstream.
- **Cost optimisation:** S3 tiering for ClickHouse, lifecycle expiry on every bucket, Graviton nodes, and Karpenter consolidation after storms.
- **Operational excellence:** lag-based SLOs and alarms, everything defined as code, and a monthly upgrade cadence that follows self-hosted releases.
- **Sustainability:** scale-to-demand consumers and cold-tier storage.

## Policy check and exceptions requested

| ID | Request | Why | Compensating controls | Approvers |
|---|---|---|---|---|
| EXC-01 | Operate self-managed ClickHouse | No managed option inside the firm's accounts (CON-01) | Altinity operator, replicas across AZs, nightly backups to S3 with cross-region copy, restore tested quarterly, on-call runbooks | Platform engineering lead, CCoE |
| EXC-02 | Internet-facing ingest endpoint for browser and mobile SDKs | Client-side telemetry cannot use PrivateLink (CON-02) | Only Relay is exposed; WAF rate limits and bot control; per-project DSN keys with quotas; PII scrubbed before storage | CISO |

The FSL-1.1 licence permits internal use. Legal sign-off is tracked as NFR-CMP-01 rather than as an exception.

## Risks and open items

- Upstream ships monthly, and self-hosted upgrades include database migrations. Rehearse each upgrade in a staging environment with production-like data volumes.
- ClickHouse operations need specialist skills (EXC-01).
- Brokers must accept 50 MB messages. Load-test the largest replays and attachments before go-live (CON-03).
- Open questions: replay masking (Q-01) and centrally enforced sample rates (Q-02).

## Sources

- getsentry/self-hosted at commit `d09ff0cd592f`: [docker-compose.yml](https://github.com/getsentry/self-hosted/blob/master/docker-compose.yml), [sentry.conf.example.py](https://github.com/getsentry/self-hosted/blob/master/sentry/sentry.conf.example.py), [config.example.yml](https://github.com/getsentry/self-hosted/blob/master/sentry/config.example.yml), [LICENSE.md](https://github.com/getsentry/self-hosted/blob/master/LICENSE.md)
- Components: [relay](https://github.com/getsentry/relay), [sentry](https://github.com/getsentry/sentry), [snuba](https://github.com/getsentry/snuba), [symbolicator](https://github.com/getsentry/symbolicator), [vroom](https://github.com/getsentry/vroom), [taskbroker](https://github.com/getsentry/taskbroker), [uptime-checker](https://github.com/getsentry/uptime-checker)
- [Sentry developer docs: nodestore](https://develop.sentry.dev/backend/application-domains/nodestore/)
