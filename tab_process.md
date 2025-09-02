---
title: Process
layout: null
tab: true
order: 2
tags: secure-by-design
---

## SbD Process

The Secure-by-Design Process is the operational playbook for applying Secure-by-Design during the design phase and engaging with AppSec efficiently—without creating bottlenecks. It describes the end-to-end process, what a design review examines, when to escalate, how the SbD Review Checklist fits into the workflow, and the mechanics for handing off to AppSec.

Two complementary tools underpin the process:

- **SbD Principles & Recommendations** – The full guidance across all domains (architecture, data, resilience, access control, monitoring). Used actively during design. _(See Tab Principals)._
- **SbD Review Checklist** – A concise Yes/No/N-A questionnaire capturing the most critical controls, used for self-review and as the communication bridge to AppSec. _(See tab Checklist)._

Each team designates a **Security Champion** as the primary security contact. They own the checklist, coordinate any required AppSec engagement, and ensure that design artifacts remain complete, accurate, and in sync with the implemented architecture.

### ◽ Process Overview

1. **Capture Security Requirements (Planning)**
   - Define _what must be true_ (CIA, privacy, compliance) in the SRS/backlog with traceability.
2. **Draft High‑Level Architecture (Design)**
   - Sketch components, trust zones, data flows, external dependencies, and assumptions.
3. **Apply SbD Principles & Patterns**
   - Select patterns (service isolation, mTLS, idempotency, circuit breakers, schema governance) that satisfy the requirements.
   - Consider the recommendations detailed in the Principals tab.
   - _Security Champion involved for early alignment._
4. **Complete the SbD Review Checklist**
   - Answer Yes/No/N‑A for each control and add comments/links to ADRs, diagrams, and policies. (Detailed in the Checklist tab)
   - _Security Champion facilitates completeness and clarity._
5. **Internal Peer Review**
   - Another architect/engineer reviews the design \+ checklist; unresolved “No” items become actions.
6. **Risk Triage**
   - Low / Normal Risk → Proceed to development (step 8).
   - High / Critical Risk → Ping AppSec (attach the checklist \+ security requirements \+ diagrams).
   - _Security Champion_ — acts as the point of contact with AppSec.
7. **Optional Threat Modeling & AppSec Review (when triggered)**
   - If risk triggers are met, the product team performs a lightweight or full threat model (methods like STRIDE) before development, with AppSec support if needed.
   - AppSec reviews the checklist and threat model, focusing on “No”/“N-A” items with high impact, and recommends mitigations.
8. **Finalize Design Artifacts**
   - Update diagrams, ADRs, checklist status, and (if applicable) threat model.
9. **Handoff to Development**
   - Ensure the design and checklist are part of the dev onboarding pack.
10. **Design‑Drift Watch**
    - For any major change (new data type, new exposure, new tech), repeat steps 3–8.

### ◽ What the Secure‑by‑Design Review Covers

A Secure-by-Design review evaluates the architecture and high-level design of a system against a curated set of domains from the SbD Principles & Recommendations.

The review inspects the following domains:

- **Core Secure Design Principles:** Confirmation that foundational principles—such as least privilege, defense in depth, secure defaults, and zero-trust boundaries—are explicitly applied in the architecture.
- **Architecture & Service Design:** Assessment of service boundaries, trust zones, inter-service communication methods, integration points, and scalability/resilience patterns.
- **Data Management & Protection:** Verification of data classification, ownership, encryption (in transit/at rest), retention/deletion policies, and auditability mechanisms.
- **Reliability & Resilience:** Evaluation of fault tolerance, error handling, failover, backoff/retry policies, bulkhead and circuit-breaker patterns, and performance safeguards.
- **Access Control & Secure Communication:** Review of authentication and authorization approaches (user/service), secure channel enforcement (TLS/mTLS), secret/key management, and policy enforcement mechanisms.
- **Monitoring, Testing & Incident Readiness:** Confirmation that observability, logging, metrics, and incident response capabilities are designed in from the start, with sufficient detail to detect, investigate, and recover from security events.

### ◽ Risk-Based Escalation & Threat Modeling

The Secure-by-Design process is designed to be lightweight for most changes, but specific risk triggers require escalation to a Threat Modelin**g** activity—either facilitated by AppSec or performed with their guidance. Threat Modeling provides a deeper, attacker-oriented review to validate that the proposed architecture’s controls adequately mitigate the most critical risks.

**Escalation Triggers** (any one of these should prompt a threat model before development begins):

- **Sensitive or Regulated Data:** Handling personally identifiable information (PII), payment card data (PCI), health records (PHI), or other regulated datasets.
- **New External Exposure:** Introducing new public-facing interfaces—such as APIs, message endpoints, or event streams—that may require abuse-case and misuse-case analysis.
- **Novel Technologies or Patterns:** Adopting new frameworks, infrastructure components, or architectural patterns (e.g., a message broker not previously used, a new orchestration platform, or emerging protocols) that lack established security baselines in your environment.
- **High-Business-Impact Services (Tier-1):** Delivering or significantly modifying critical services whose compromise would have severe operational, financial, or reputational consequences.

When triggered, the Threat Modeling session:

- Starts with the current SbD-hardened architecture as input,
- Analyzes potential attack vectors and abuse scenarios,
- Validates that existing controls sufficiently reduce risk,
- Identifies additional mitigations if needed.

Outputs typically include an updated threat model diagram, a prioritized risk register, and action items to address uncovered gaps—feeding back into the SbD artifacts (ADRs, diagrams, checklists).

### ◽ SbD Review Checklist — Usage

It’s a quick, control‑oriented snapshot that communicates what’s in place and what needs work, enabling fast AppSec team triage. Refer to tab Review Checklist, for more details.

Each row includes: _(a)_ control name, _(b)_ Yes/No/N‑A, _(c)_ comment “how/where implemented” with links (ADR‑IDs, diagram anchors, policy refs), _(d)_ evidence link (optional).

**When to complete.**

- Before any AppSec request.
- On major design change (new trust boundary, sensitive data, new external exposure, novel tech).
