---
title: Principles
layout: null
tab: true
order: 3
tags: secure-by-design
---

## Secure-By-Design Principles & Recommendations

The Secure-by-Design Principles & Recommendations provide detailed, design-time guidance for building security directly into system architecture. This content focuses exclusively on decisions made during the design phase—well before coding or testing begins.

It does **not** cover secure coding standards (such as OWASP ASVS), automated scanning, or vulnerability triage. Instead, it presents architecture-level controls that eliminate entire classes of flaws—for example, applying least privilege, enforcing strong isolation, ensuring idempotency, maintaining disciplined schema management, and using mutual TLS (mTLS).

These principles form the foundation for the Secure-by-Design review process and are applied across domains such as architecture, data protection, resilience, access control, and monitoring.

**Audience:** Product architects, senior engineers, and AppSec reviewers.

**How to Use:**

- Start at the domain relevant to your current design decision and record decisions as ADRs and on architecture/data-flow diagrams.
- For each recommendation: review the Rationale, apply the Control, consider the Implementation Notes, and capture Evidence.
- Use the SbD Review Checklist (detailed in Checklist tab) to summarize what you’ve applied (Yes/No/N-A \+ justification) for internal review and to communicate with AppSec when needed.
- For concrete, end-to-end examples that implement these controls, see catalug tab.

The guidance is organized into six domains as follows. You can also [download the principals as a CSV file](https://github.com/OWASP/www-project-secure-by-design-framework/tree/main/resources/artifacts/OWASP-Secure-by-Design-Principals.csv?raw=true) for easy offline use and integration into your team’s workflows.

### Domain 1 \- Core Secure Design Principles

- **Least Privilege** — Minimize permissions for users, services, pipelines, and tools; deny-by-default; narrowly scope tokens/roles; time-bound access.
- **Defense in Depth** — Layer network isolation, authN/Z, input validation, encryption, rate-limiting, monitoring, and alerting so one failure isn’t fatal.
- **Secure Defaults** — Enable TLS, private networking, strict security headers, safe cipher suites, and hardened baselines by default.
- **Zero-Trust & Explicit Boundaries** — Treat all networks and callers as untrusted; authenticate and authorize every call; make trust zones explicit on diagrams.
- **Fail Secure / Graceful Degradation** — Prefer safe failure (deny) to insecure success; avoid leaking internals in errors; design partial modes.
- **Simplicity & Minimized Attack Surface** — Reduce components, open ports, conditional paths; favor small, cohesive interfaces over sprawling ones.
- **Observability by Design** — Emit structured logs, traces, and security events with correlation/trace IDs; standardize logging libraries.
- **Contract-First & Versioned Interfaces** — Define OpenAPI/AsyncAPI/Avro/Protobuf up front; validate at edges; version breaking changes.
- **Automation & Repeatability** — Use IaC and policy-as-code; template security configurations; enforce in CI (linting, drift checks).

---

### Domain 2 \- Architecture & Service Design

#### Service Design & Boundaries

- Align services to domain boundaries (DDD); each owns its data and business logic.
- Eliminate circular dependencies; refactor to reduce coupling; prefer pub/sub over chatty RPC.
- Keep services right-sized (cohesive, independently deployable); avoid nano-services and accidental monoliths.
- Document dependencies, SLOs, and data ownership in READMEs/runbooks.
- Centralize shared business logic where appropriate (avoid duplication; prevent inconsistent enforcement).

#### Trust Zones / Network Isolation (“Spaces”)

- Place workloads in isolated trust zones; allow direct access only within a zone.
- Enforce cross-zone access through Integration Services (gateway/mesh/bus).
- Use a unified addressing/DNS scheme for external references; allow shortcuts only for intra-zone calls.
- Keep addressing and data-access methods consistent across environments.

#### Cross-Zone Integration Services

- Route HTTP/gRPC via API gateway / Smart Gateway; enforce authN/Z, rate-limits, schema validation, and observability.
- Use a message bus (Kafka/AMQP/Redis-streams class) for async cross-zone traffic; centralize policies and auditing.
- Treat Integration Services as shared, governed assets with strict access controls.

#### Inter-Service Communication

- Prefer internal mesh with mTLS for service-to-service identity and encryption.
- Standardize client libraries for retries, timeouts, circuit-breakers, and idempotency.
- Avoid synchronous chains across many services; break with events or queues.

#### Messaging Infrastructure

- Choose log-based (Kafka-class) vs message-based (AMQP/Redis) intentionally; document rationale.
- Configure durability (persistence, replication, backups) and topic/queue retention per data class.
- Monitor throughput, lag, latency, resource usage; plan capacity and partitioning.
- Define DLQ behavior to route messages that cannot be processed after retries; and monitor and triage workflow; alert on DLQ growth.

#### API & Event Design / Schema Management

- Separate internal vs global schemas/topics; establish enterprise naming (e.g., `OrderCreated_v1`).
- Define required/optional fields and constraints; standardize on JSON/Avro/Protobuf.
- Plan backward/forward compatibility; version breaking changes; publish deprecation timelines.
- Validate and sanitize all inbound inputs at the first hop; reject unknown fields; use approved libraries.
- Design events to be multi-consumer safe (self-contained context, idempotent consumption).
- Notify impacted consumers on breaking changes via an agreed process (owners list, subscriptions, or catalogs).

#### Legacy Systems Integration

- Use an anti-corruption layer to translate models/protocols and contain legacy risk.
- Replace legacy components incrementally (strangler pattern); avoid leaking legacy assumptions.

#### Scalability & Startup Resilience

- Design for horizontal scale; set autoscaling triggers and minimum replicas.
- Ensure safe startup when dependencies are absent (timeouts, backoff, feature flags).
- Precompute or warm caches where cold-start latency matters.

#### Service Discovery

- Centralize discovery (DNS/mesh registry) and pin service identities (SPIFFE/SVID-class in meshes).
- Keep service contracts discoverable (OpenAPI/AsyncAPI catalogs).

---

### Domain 3 \- Data Management & Protection

#### Data Ownership & Domain Modeling

- Each microservice owns its data; no shared write stores; expose data via APIs/events.
- Maintain data dictionaries and owners; review when schemas evolve.

#### Idempotency

- Make handlers idempotent; tolerate repeated deliveries and retries without side-effects.
- Use stable identifiers and store processed IDs/checksums to suppress duplicates.
- Provide exactly-once effects through idempotent operations \+ dedupe, not broker magic.

#### Transaction Management

- Prefer intra-service transactional (ACID) operations where possible to prevent partial updates before cross-service workflows.
- For multi-service workflows or long-running transactions, use Sagas (orchestration/choreography).
- Avoid distributed transactions such as 2-phase commit (2PC) due to operational complexity and fragility.
- Define compensating actions for rollback; record them beside the happy path.

#### Data Consistency

- Embrace eventual consistency; document staleness windows and reconciliation.
- Provide UI/UX strategies for lag/conflicts (status, retries, manual resolve).
- Consider event sourcing when reconstructable state and audit are required.

#### Data Classification & Protection

- Classify data (public/internal/confidential/restricted) and assign owners; review classifications periodically.
- Apply appropriate security controls based on data classification (e.g., encryption for sensitive, RBAC for restricted).
- Encrypt in transit and at rest using approved algorithms; centralize key management and rotation; separate duties.
- Minimize data collection to business necessity; document rationale and lawful basis where applicable.

#### Data Retention & Deletion Policies

- Define retention periods and secure deletion/archival for each data class; meet regulatory and contractual obligations.
- Periodically purge unnecessary data; verify deletion in backups/replicas.

#### Environment-Consistent Data Access Methods

- Standardize connection methods and secrets retrieval across environments.
- Avoid environment-specific quirks that break portability and security.

#### Auditability & Data Lineage

- Capture who/what/when for sensitive data changes; maintain lineage from source to sink.
- Log schema changes and approvals; catalog datasets.

---

### Domain 4 \- Reliability & Resilience

### Error Handling

- Use exponential backoff \+ jitter; cap attempts; avoid retry storms.
- Provide default and safe errors that don’t leak internals; standardize error models.

#### Asynchronous Communication Semantics

- Decide on ordering; use keys/partitions or total ordering where required.
- Select delivery semantics (at-least-once vs exactly-once trade-offs); document choices.
- Define ack/retry/visibility timeouts per queue/topic; implement DLQ policies.
- Design Integration Services for high availability and cross-zone error handling so shared layers don’t propagate failures.
- Explicitly accept and design for timing delays that may affect outcomes (latency budgets, timeout windows).

#### Isolation Patterns

- Apply circuit breakers to failing dependencies to prevent cascading failures when a service is down; bulkheads to isolate pools/threads.
- Provide fallbacks or degrade non-critical features instead of cascading failure.

#### Race Conditions & Concurrency Controls

- Use distributed locks (e.g., Redis-class) or atomic ops for shared-state updates.
- Require Idempotency-Key for all mutating endpoints; on replay with same key+payload return stored response; same key+different payload → 409 Conflict.
- Back with rate-limits and DB uniqueness/optimistic concurrency to handle concurrent modifications safely within a service..

#### Timeouts, Health Checks & Service Availability

- Set sensible timeouts for all calls; include liveness/readiness probes; auto-restart.
- Design degraded modes for dependency loss; prefer partial functionality.
- Define explicit failover/redundancy strategies (active-passive, multi-AZ/region) for critical services.

#### Scalability, Resource Quotas & Rate Limiting

- Define quotas (CPU/mem/IO) and autoscaling policies; protect neighbors.
- Enforce rate limits at gateway/mesh (per endpoint, per client) with burst controls.

#### Performance Optimization & Caching

- Use caching (client, service, CDN) for hot paths; tune eviction/invalidation.
- Apply back-pressure and queue sizing for spikes; shed load gracefully. The minimum quotas definition ensures the service performs correctly, as platform configurations may limit resource usage to prevent overconsumption by a single service.

#### Dependency & Third-Party Risk Management

- Evaluate external services/OSS (license, support, vulnerability posture); follow onboarding policy.
- Track components; monitor for CVEs; pin versions; plan exit strategies.

#### Deployment & Release Management

- Prefer canary/blue-green; measure guardrails; test rollback paths regularly.
- Keep feature flags for progressive delivery and kill switches.

#### Decommissioning & Disposal

- Retire DNS, CI/CD, storage, credentials; wipe data securely; verify no residual access.

#### Supply Chain Integrity & Artifact Provenance

- Use signed, scanned artifacts from trusted registries; enforce provenance checks in CI.
- Generate/retain SBOMs (e.g., CycloneDX); gate builds on policy compliance.

---

### Domain 5 \- Access Control & Secure Communication

#### Secure Communication & Service Identity

- Enforce TLS/mTLS for all service communications; automate cert issuance/rotation (mesh SDS-class).
- Pin service identities (SPIFFE/SVID-class) for workload-to-workload trust.

#### Authentication

- Centralize user auth with OIDC/OAuth2 IdP; enforce MFA for privileged paths.
- Use short-lived tokens; avoid long-lived static secrets; prefer workload identities.

#### Authorization & Permissions

- Implement RBAC/ABAC; clearly define who/what can call which APIs or publish/consume events.
- Centralize authZ at gateway/mesh where possible; use policy-as-code for consistency.
- Govern shared Integration Services with strict access lists and approvals.
- Externalize business rules (configs/policies) rather than hard-coding.
- Periodically review and right-size permissions (users, services, pipelines, tools).

#### Configuration Security & Secret/Key Management

- Store secrets in a dedicated secret manager; rotate keys/certs regularly; audit access.
- Use dynamic/sealed secrets where possible; avoid secrets in env vars/logs.

#### Compliance & Regulatory Requirements

- Map controls to GDPR/PCI DSS/…; document compensating controls where needed.
- Maintain records and evidence (policies, DPA, DPIA) as part of design artifacts.

#### Identity & Access Management

- Define roles and separation of duties; log/admin path protection; least privilege for admin tools.

#### Least Privilege for Tooling & Pipelines

- Restrict CI/CD, VCS apps, chat-ops, and integrations to minimal scopes; use approval workflows for sensitive operations.
- Protect signing keys and runners; isolate build environments.

---

### Domain 6 \- Monitoring & Incident Readiness

#### Logging & Observability

- Emit structured logs with correlation/trace IDs; centralize ingestion and retention.
- Protect logs from tampering; restrict access; include admin/operational access logging.

#### Metrics, Dashboards & SLOs

- Instrument latency, error rate, saturation, and business KPIs; create health dashboards.
- Monitor event flows (queue sizes, lag, DLQ volume); alert on anomalies.

#### Security Testing & Verification

- Plan ASVS-aligned verification; include negative tests from threat modeling.
- Validate SbD controls (isolation tests, mTLS handshake checks, authorization denials, rate-limit behavior).

#### Documentation & Knowledge Sharing

- Publish OpenAPI/AsyncAPI and event schemas; maintain catalogs.
- Keep runbooks and ADRs up to date; document data ownership and dependencies.

#### Incident Response & Recovery

- Maintain a formal IR plan (roles, comms, evidence handling); rehearse with tabletops.
- Detect suspicious patterns: repeated failed logins, unusual 403 rates, anomalous access to sensitive endpoints, geo-improbable logins, unexpected config/file changes.
- Feed lessons learned back into patterns, ADRs, and the checklist.

#### Audit & Retention

- Retain audit-relevant logs per policy (e.g., ≥12 months with a searchable window).
- Ensure tamper-evidence and chain-of-custody for security logs.

### ◽ Links

You can also [download the principals as a CSV file](https://github.com/OWASP/www-project-secure-by-design-framework/tree/main/resources/artifacts/OWASP-Secure-by-Design-Principals.csv?raw=true) for easy offline use and integration into your team’s workflows.
