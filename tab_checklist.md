---
layout: col-sidebar
title: Checklist
level: 2
type: documentation
order: 4
tags: Secure-by-design-review, Secure-by-design-review-checklist, Secure-by-designchecklist, Design-review, Design-review-checklist, Design-checklist
# title: Checklist
# layout: null
# tab: true
# order: 4
# tags: Secure-by-design-review, Secure-by-design-review-checklist, Secure-by-designchecklist, Design-review, Design-review-checklist, Design-checklist
---

## Secure-By-Design Review Checklist

The Secure-by-Design checklist set is a practical tool for embedding security requirements into the design phase of the software development lifecycle and for ensuring those requirements are verified before implementation. It serves as a clear, concise mechanism for product teams to self‑assess their designs, capture evidence, and internally document their security posture—without introducing unnecessary delays. These checklists are intended primarily for internal team self‑review and as a shared communication tool with the security team in high‑risk projects.

Checklists translate the framework’s principles and recommendations (tab Principals) into actionable, reviewable controls. They ensure that security‑critical aspects are consistently considered, documented, and validated across all projects, regardless of the team’s size, experience, or technology stack. When used systematically, they reduce rework, strengthen the security posture, and enable efficient, scalable collaboration within the team and, where appropriate, with AppSec.

SbD Review Checklist defines the structure, intended usage, and governance of Secure‑by‑Design checklists, clarifying how they fit into the design process and how they can be maintained alongside other project artefacts.

### ◽ Intended Use and Timing

The SbD review checklist is completed by the product team during the design phase—ideally before high-level design sign-off or epic kickoff—and is refreshed whenever the design changes in a way that could alter the system’s security characteristics. Typical triggers include new integrations, exposure to untrusted networks, handling of sensitive data, adoption of new technologies, or changes to core workflows.

Begin with the full checklist during detailed design sessions—either from the outset of design work or as a dedicated security refinement step—to systematically think through applicable recommendations listed in tab Principal and update the design accordingly. Capture your considerations and decisions either directly within the design artefacts or in a separate security notes document; for internal peer review, assurance, and historical record.

Once these detailed considerations have been addressed, complete the SbD review checklist to sumarize them into a clear, high-level mapping against the security requirements identified earlier. This becomes the main descriptive summary of applied considerations, along with any gaps to address later, and is stored with the project’s artefacts for ongoing reference. Where the project is high‑risk, this document can also support discussions with the AppSec to request guidance or additional assurance. See Escalation and risk thresholds.

### ◽ Checklist Structure

Each checklist entry contains:

- **ID** – Unique identifier for traceability.
- **Control Statement** – The security control or question to be addressed.
- **Status** – _Yes / No / N-A_.
- **Justification** – Stable links to ADRs, diagrams, configurations, relevant policy references, or including brief explanations or justifications where relevant.
- **Severity** – High, Medium, or Low if the control is not implemented.
- **Comment** – Supplementary notes for any status value, including clarification, additional context, or rationale for “N-A” and “No”.

### ◽ SbD Review Checklist

This checklist defines the minimum set of controls that every design is expected to address. A CSV version is available at [`checklist.csv`](https://github.com/OWASP/www-project-secure-by-design-framework/tree/main/resources/artifacts/OWASP-Secure-by-Design-Checklist.csv?raw=true). Each item abstracts key recommendations from tab Principal, streamlined to support a Yes/No/N-A decision backed by evidence. For each control, teams record _Status_, _Justification_, _Severity_, and _Comments_. Items marked (Critical) are generally required to be Yes at design sign-off, or to have an approved, time-bound mitigation plan in place.

#### A. Architecture & Service Design

| ID    | Control Statement                                                                                                                 | Critical |
| ----- | --------------------------------------------------------------------------------------------------------------------------------- | -------- |
| AS-01 | Trust zones (logical network “Spaces”) are enforced; cross‑zone traffic flows only via governed integration layers (gateway/bus). | Yes      |
| AS-02 | Service addressing & discovery are unified and consistent across environments; intra‑zone shortcuts are not used externally.      |          |
| AS-03 | Service boundaries are clear; no circular dependencies; services are right‑sized; each service owns its data.                     |          |
| AS-04 | Architecture avoids long synchronous chains; event‑driven or queued handoffs are used where appropriate.                          |          |
| AS-05 | API/event contracts are defined and versioned; consumers are notified before breaking changes take effect.                        |          |
| AS-06 | Messaging has durability (persistence/replication/retention) with a defined DLQ strategy and triage.                              |          |
| AS-07 | Startup resilience: services handle missing dependencies; autoscaling defined with minimum replicas.                              |          |
| AS-08 | Legacy integrations are isolated behind an anti‑corruption layer; migration path is defined.                                      |          |

#### B. Data Management & Protection

| ID    | Control Statement                                                                                             | Critical |
| :---- | :------------------------------------------------------------------------------------------------------------ | :------- |
| DM-01 | Data are classified with named owners; controls are proportional to classification.                           |          |
| DM-02 | Encryption in transit (TLS/mTLS) and at rest with managed keys and rotation is designed in.                   | Yes      |
| DM-03 | Handlers and workflows are idempotent; duplicate detection/suppression is in place where needed.              |          |
| DM-04 | Cross‑service transactions use Sagas/compensations; 2PC is avoided; prefer intra‑service ACID first.          |          |
| DM-05 | Retention/deletion policies exist per data class; minimization is applied to collection/storage.              |          |
| DM-06 | Consistency model is documented; UX handles staleness/conflicts; event‑sourcing considered where appropriate. |          |

#### C. Reliability & Resilience

| ID    | Control Statement                                                                                                           | Critical |
| :---- | :-------------------------------------------------------------------------------------------------------------------------- | :------- |
| RR-01 | Retries use exponential backoff \+ jitter; default exception handling and safe error models exist.                          |          |
| RR-02 | Circuit breakers/bulkheads protect dependencies; non‑critical features have fallback/degraded modes.                        |          |
| RR-03 | Asynchronous semantics are defined: ordering, delivery guarantees, ack/visibility timeouts; DLQs are monitored.             |          |
| RR-04 | Shared integration layers (gateway/bus) are high‑availability and contain errors to prevent cross‑zone failure propagation. |          |
| RR-05 | All mutating endpoints enforce an Idempotency‑Key; concurrency controls (locks/optimistic concurrency) are in place.        |          |
| RR-06 | Timeouts on all calls; health probes; explicit failover/multi‑AZ strategies for critical paths.                             | Yes      |
| RR-07 | Quotas and autoscaling are defined; rate‑limits enforced at edges; minimum capacity documented.                             |          |
| RR-08 | Caching/CDN/back‑pressure patterns are applied where appropriate; performance guardrails are defined.                       |          |

#### D. Access Control & Secure Communication

| ID    | Control Statement                                                                                              | Critical |
| :---- | :------------------------------------------------------------------------------------------------------------- | :------- |
| AC-01 | All communications use TLS/mTLS; service identity is verified (e.g., mesh‑issued certs).                       | Yes      |
| AC-02 | Central IdP (OIDC/OAuth2) is used; MFA enforced on privileged/admin paths; tokens are short‑lived.             | Yes      |
| AC-03 | RBAC/ABAC governs APIs and publish/consume on messaging; least privilege is applied.                           |          |
| AC-04 | Authorization is centralized at gateway/mesh via policy‑as‑code; shared integration services are governed.     |          |
| AC-05 | Secrets are stored in a secret manager; keys/certs rotate automatically; no secrets in code/logs.              |          |
| AC-06 | Applicable regulatory controls (e.g., privacy/financial) are identified with evidence of design alignment.     |          |
| AC-07 | Least privilege extends to CI/CD, VCS apps, chat‑ops, and third‑party tooling; periodic reviews are scheduled. |          |

#### E. Monitoring, Testing & Incident Readiness

| ID    | Control Statement                                                                                     | Critical |
| :---- | :---------------------------------------------------------------------------------------------------- | :------- |
| MT-01 | Structured centralized logs with correlation/trace IDs; administrative access is logged.              |          |
| MT-02 | Metrics/SLOs and dashboards cover service health and event‑flow anomalies; alerts are actionable.     |          |
| MT-03 | ASVS‑aligned security tests and negative tests from threat‑modeling are planned in the test strategy. |          |
| MT-04 | SbD controls are verifiable in test (isolation, mTLS, authZ denials, rate‑limit behavior).            |          |
| MT-05 | OpenAPI/AsyncAPI and event catalogs are published; ADRs/runbooks are current.                         |          |
| MT-06 | A documented Incident Response plan (roles, comms, evidence handling) exists and is rehearsed.        | Yes      |
| MT-07 | Audit log retention ≥12 months with tamper‑evidence and a searchable hot window is defined.           | Yes      |

### ◽ Escalation & Risk Thresholds

If any critical control is No, or the overall risk score exceeds your team’s threshold, engage AppSec before development proceeds. Critical controls typically include those related to trust boundaries, encryption in transit/at rest, authentication and authorization, and incident readiness.

**Suggested scoring:** Yes \= 0, N-A \= 0 (with justification), No (Low) \= 1, No (Med) \= 2, No (High) \= 4\.  
**Escalate when:** any critical \= No, or total score ≥ 6, or any trigger from §2.4 applies (new external exposure, sensitive data, novel tech, Tier‑1 impact).  
**Review scope:** Security team reviews the checklist and linked evidence, advises on mitigations, and may request deeper verification or threat modeling.

### ◽ Governance and Maintenance

Checklists are version-controlled alongside design documentation in the project repository and remain internal to the team unless explicitly shared for external review.  
 Changes to checklist scope or content follow a documented change-control process, with major changes triggering a version increment and minor editorial changes noted in a changelog.  
Automation is encouraged: repository or CI/CD pipelines can enforce the presence of an up-to-date checklist, verify that all items are answered, and warn when “No” responses lack associated tickets or follow-up actions.

### ◽ Links

You can also [download the checklist as a CSV file](https://github.com/OWASP/www-project-secure-by-design-framework/tree/main/resources/artifacts/OWASP-Secure-by-Design-Checklist.csv?raw=true) for easy offline use and integration into your team’s workflows.
