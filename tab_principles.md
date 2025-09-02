---
title: Principles
layout: null
tab: true
order: 3
tags: secure-by-design
---

## SbD Principles & Recommendations

The Secure-by-Design Principles & Recommendations provide detailed, design-time guidance for building security directly into system architecture. This content focuses exclusively on decisions made during the design phase—well before coding or testing begins.

It does **not** cover secure coding standards (such as OWASP ASVS), automated scanning, or vulnerability triage. Instead, it presents architecture-level controls that eliminate entire classes of flaws—for example, applying least privilege, enforcing strong isolation, ensuring idempotency, maintaining disciplined schema management, and using mutual TLS (mTLS).

These principles form the foundation for the Secure-by-Design review process and are applied across domains such as architecture, data protection, resilience, access control, and monitoring.

**How to Use:**

- Start at the domain relevant to your current design decision and record decisions as ADRs and on architecture/data-flow diagrams.
- For each recommendation: review the Rationale, apply the Control, consider the Implementation Notes, and capture Evidence.
- Use the SbD Review Checklist (detailed in Checklist tab) to summarize what you’ve applied (Yes/No/N-A \+ justification) for internal review and to communicate with AppSec when needed.
- For concrete, end-to-end examples that implement these controls, see catalug tab.

The guidance is organized into six domains as follows.

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

- Align services to domain boundaries (DDD); each owns its data and business logic—others use its API/events. Avoid shared write databases.
- Eliminate circular dependencies; refactor to reduce coupling; prefer pub/sub over chatty RPC to reduce tight coupling.
- Keep services right-sized (cohesive, independently deployable); avoid nano-services and accidental monoliths.
- Document dependencies, SLOs, and data ownership in READMEs/runbooks.
- Centralize shared business logic where appropriate (avoid duplication; prevent inconsistent enforcement).
- Document dependencies, SLOs, and data ownership in READMEs/runbooks so on-call knows what breaks what.

#### Trust Zones / Network Isolation

- Place workloads in isolated trust zones; allow direct access only within a zone.
- Force cross-zone access via Integration Services (API gateway/mesh/bus) where policies are enforced and traffic is observable.
- Use a unified addressing/DNS scheme for external references; intra-zone shortcuts are OK but don’t leak outside.
- Keep addressing and data-access methods consistent across environments to reduce “works in dev only” issues.

#### Cross-Zone Integration Services

- Route HTTP/gRPC through an API gateway: enforce authN/Z, rate-limits, schema validation, and request logging.
- Use a message bus (Kafka/AMQP/Redis streams) for async cross-zone communication; centralize policies, ACLs, and auditing.
- Treat Integration Services as shared, governed assets with strict access controls and owner approval.

#### Inter-Service Communication

- Use a service mesh with mTLS for identity + encryption between services; it removes the need to manage certs in each app.
- Standardize client libraries for retries, timeouts, circuit-breakers, and idempotency.
- Avoid long synchronous chains across many services (N services in a line). Break chains with queues/events to absorb spikes and failures.

#### Messaging Infrastructure

- Choose log-based (e.g., Kafka-class) vs message-based (e.g., AMQP/Redis) deliberately; document the trade-off (replay vs simplicity).
- Configure durability (persistence, replication, backups) and topic/queue retention by data sensitivity.
- Monitor throughput, lag, latency, and resource usage; plan capacity/partitioning ahead of load.
- Define DLQ behavior to route messages that cannot be processed after retries; and monitor and alert on growth; have a triage/replay playbook workflow.

#### API & Event Design / Schema Management

- Separate internal vs global schemas/topics; establish enterprise naming (e.g., OrderCreated_v1).
- Specify required/optional fields and constraints; standardize on JSON/Avro/Protobuf so tooling works everywhere.
- Plan backward/forward compatibility; version breaking changes; publish deprecation timelines.
- Validate and sanitize all inbound inputs at the first hop; reject unknown fields; use approved libraries.
- Design events to be multi-consumer safe (self-contained context, idempotent consumption).
- Notify impacted consumers on breaking changes via an agreed process (owners list, subscriptions, or catalogs).

#### Legacy Systems Integration

- Add an anti-corruption layer to translate old models/protocols; keep legacy assumptions from leaking into new services.
- Replace legacy components incrementally (strangler pattern) with measurable milestones.

#### Scalability & Startup Resilience

- Design for horizontal scale; set autoscaling triggers and a minimum replica count (so you don’t scale to zero by mistake).
- Start safely when dependencies aren’t ready: timeouts, retries with backoff, and feature flags.
- Warm caches or precompute hot data to reduce cold-start latency.

#### Service Discovery

- Centralize discovery (DNS/mesh registry) and pin service identities (SPIFFE/SVID-class in meshes) to know who is who.
- Publish service contracts in catalogs (OpenAPI/AsyncAPI) so teams can find and integrate safely.

---

### Domain 3 \- Data Management & Protection

#### Data Ownership & Domain Modeling

- Each service must own its data store (write access). Others read via API or subscribed events. Prevents tight coupling and “stealth joins”.
- Maintain data dictionaries and owners; update when schemas evolve.

#### Idempotency

- Make handlers idempotent; tolerate retries and duplicates (repeated deliveries) without side-effects.
- Use stable identifiers and store processed IDs/checksums to suppress duplicates.
- Provide exactly-once effects through idempotent operations \+ dedupe, not broker magic.

#### Transaction Management

- Prefer local ACID transactions within a service where possible to avoid partial updates before cross-service workflows.
- For multi-service workflows or long-running transactions, use Sagas (orchestration/choreography).
- Avoid distributed transactions such as 2-phase commit (2PC)—it’s complex and brittle at scale.
- Define compensating actions (for rollback) next to the happy path.

#### Data Consistency

- Embrace eventual consistency; document staleness windows and reconciliation jobs.
- Design UI/UX to show “processing”/“pending” states and offer retry or manual resolve.
- Consider event sourcing when you need full history/audit or state reconstruction.

#### Data Classification & Protection

- Classify data (public/internal/confidential/restricted) with a named owner; review periodically.
- Apply appropriate security controls based on data classification (e.g., encryption for sensitive, RBAC for confidential/restricted).
- Encrypt in transit and at rest using approved algorithms; centralize key management/rotation; separate duties.
- Minimize data collection to business necessity; document rationale/lawful basis where applicable.

#### Data Retention & Deletion Policies

- Set retention periods \+ secure deletion/archival for data class; meet regulatory/contractual obligations.
- Periodically purge unnecessary data; verify deletion propagates to backups/replicas.

#### Environment-Consistent Data Access Methods

- Standardize connection methods and secrets retrieval across environments.
- Avoid environment-specific quirks that break portability and security.

#### Auditability & Data Lineage

- Record who/what/when for sensitive data changes; Track lineage from source to sink.
- Log schema changes and approvals; catalog datasets and owners.

---

### Domain 4 \- Reliability & Resilience

### Error Handling

- Use retries with exponential backoff \+ jitter; cap attempts; avoid retry storms.
- Return safe, standardized errors (no stack traces or secrets). Document error models.

#### Asynchronous Communication Semantics

- Decide ordering requirements; use keys/partitions or total ordering where needed.
- Select delivery semantics (at-least-once vs exactly-once trade-offs); document choices.
- Set ack/retry/visibility timeouts per queue/topic; define DLQ policies and a replay process.
- Design Integration Services for high availability and cross-zone error handling so shared layers don’t propagate failures.
- Explicitly accept and design for timing delays that may affect outcomes (latency/timeout windows).

#### Isolation Patterns

- Apply circuit breakers to failing dependencies to prevent cascading failures when a dependent service is down; bulkheads to isolate pools/threads so one spike doesn’t sink the ship.
- Provide fallbacks (cached data, default responses) or degrade non-critical features instead of cascading failure.

#### Race Conditions & Concurrency Controls

- Use distributed locks (e.g., Redis-class) or atomic ops for shared-state updates.
- Require Idempotency-Key for all mutating endpoints; on same key+payload → return stored response; same key+different payload → 409 Conflict.
- Combine with rate-limits and DB uniqueness/optimistic concurrency to handle concurrent modifications safely within a service..

#### Timeouts, Health Checks & Service Availability

- Set timeouts for all calls; include liveness/readiness probes; enable auto-restart.
- Design degraded modes when dependencies are down; prefer partial functionality (queue-and-replay, read-only, feature flags).
- Define explicit failover/redundancy (active-passive, multi-AZ/region) strategies for critical paths.

#### Scalability, Resource Quotas & Rate Limiting

- Define CPU/mem/IO quotas and autoscaling policies; prevent noisy-neighbor issues.
- Enforce gateway/mesh rate limits (per endpoint, per client) with bursts and sensible defaults.

#### Performance Optimization & Caching

- Cache on client/service/CDN for hot paths; define eviction and invalidation rules.
- Apply back-pressure and queue sizing for spikes; shed load gracefully. Set minimum quotas so the service can operate even under platform caps (in case, platform configurations may limit resource usage to prevent overconsumption by a single service).

#### Dependency & Third-Party Risk Management

- Evaluate external services/OSS (license, support, vulnerability posture); follow onboarding policy.
- Track components; monitor for CVEs; pin versions; plan exit strategies.

#### Deployment & Release Management

- Prefer canary/blue-green; set guardrail metrics; test rollback paths regularly.
- Use feature flags for progressive delivery and kill switches for fast disable.

#### Decommissioning & Disposal

- Retire DNS, CI/CD, storage, credentials; wipe data securely; verify no residual access.

#### Supply Chain Integrity & Artifact Provenance

- Use signed, scanned artifacts from trusted registries; enforce provenance checks in CI.
- Generate/retain SBOMs (e.g., CycloneDX); gate builds on policy compliance.

---

### Domain 5 \- Access Control & Secure Communication

#### Secure Communication & Service Identity

- Enforce TLS/mTLS for all service communications; automate cert issuance/rotation (mesh SDS-style).
- Pin service identities (SPIFFE/SVID-class) for workload-to-workload trust (to prove who is calling whom).

#### Authentication

- Centralize user auth with OIDC/OAuth2 IdP; enforce MFA for privileged paths.
- Prefer short-lived tokens and workload identities over long-lived static secrets.

#### Authorization & Permissions

- Implement RBAC/ABAC; document "who/what can call which APIs or publish/consume events".
- Centralize authorization policies (gateway/mesh where possible) using policy-as-code for consistency.
- Govern shared Integration Services with strict access lists (ACLs) and approvals.
- Externalize business rules (configs/policies) instead of hard-coding.
- Periodically review and right-size permissions for users, services, pipelines, tools.

#### Configuration Security & Secret/Key Management

- Store secrets in a secret manager; rotate keys/certs regularly; audit access. Avoid secrets in env vars/logs.
- Use dynamic/ephemeral credentials when available.

#### Compliance & Regulatory Requirements

- Map controls to GDPR/PCI DSS/etc; document compensating controls where needed.
- Maintain evidence (policies, DPA, DPIA) as part of design artifacts.

#### Identity & Access Management

- Define roles and separation of duties; protect log/admin path; least privilege everywhere.

#### Least Privilege for Tooling & Pipelines

- Restrict CI/CD, VCS apps, chat-ops, and integrations to minimal scopes; require approvals for sensitive actions.
- Protect signing keys/runners; isolate build environments.

---

### Domain 6 \- Monitoring & Incident Readiness

#### Logging & Observability

- Emit structured logs with correlation/trace IDs; centralize ingestion and retention.
- Log administrative and operational access; restrict access; protect logs from tampering and over-exposure.

#### Metrics, Dashboards & SLOs

- Track latency, error rate, saturation, and key business KPIs. Build health dashboards.
- Monitor event flows (queue sizes, lag, DLQ volume); alert on anomalies.

#### Security Testing & Verification

- Plan ASVS-aligned verification; include negative tests from threat modeling.
- Validate SbD controls (isolation tests, mTLS handshake checks, authorization denials, rate-limit behavior).

#### Documentation & Knowledge Sharing

- Publish OpenAPI/AsyncAPI and event schemas; maintain catalogs.
- Keep runbooks and ADRs up to date; document data ownership and dependencies.

#### Incident Response & Recovery

- Maintain a formal Incident Response plan (roles, comms, evidence handling); rehearse with tabletops.
- Detect suspicious patterns: repeated failed logins, unusual 403 rates, anomalous access to sensitive endpoints, geo-improbable logins, unexpected config/file changes.
- Feed lessons learned back into security requirements, patterns, ADRs, and the checklist.

#### Audit & Retention

- Retain audit logs per policy (e.g., ≥12 months with a searchable window).
- Ensure tamper-evidence and chain-of-custody for security logs.

### ◽ Links

You can also [download the principals as a CSV file](https://github.com/OWASP/www-project-secure-by-design-framework/tree/main/resources/artifacts/OWASP-Secure-by-Design-Principals.csv?raw=true) for easy offline use and integration into your team’s workflows.
