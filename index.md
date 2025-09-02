---
layout: col-sidebar
title: OWASP Secure by Design Framework
tags: secure-by-design
level: 2
type: documentation
pitch: The OWASP Secure-by-Design Framework provides practical guidance to embed security into software architecture from the start—long before code is written.
---

Draft Version 0.5.0 – Initial Community Review (August 2025\)

## 🔹 Introduction

Secure-by-Design (SbD) is a foundational approach in the software development lifecycle that ensures security is engineered into applications and services from the outset, making them resilient to threats and aligned with both regulatory and organizational policies.

This framework delivers structured, actionable guidance for embedding security during the architecture and system **design phase** of the SDLC—long before code is written. It closes the gap between high-level security requirements and code-level verification standards, enabling teams to design systems where security is a built-in foundation rather than an afterthought.

By applying these practices early, you not only reduce vulnerabilities and costly rework, but also create systems that are inherently more reliable, maintainable, and easier to verify.

### ◽ Scope

This framework focuses exclusively on **design-time decisions**—the architectural and systemic choices that determine how security is built into a solution. It is intended for:

- **System and product architects** who own solution design
- **Product engineers** evolving architecture through agile iterations
- **Security engineers** who review or advise during design

It does **not** cover secure coding practices (e.g., OWASP Top 10, ASVS), implementation-phase testing or scanning, or the threat-modeling methodology itself, which follows design. The guidance here is about shaping secure architecture before development begins, ensuring that implementation work starts from a secure foundation.

### ◽ Project Deliverables

- **Structured Secure-by-Design Framework** – a comprehensive, principle-driven guide for design-phase security.
- **Design-Phase Security Checklist** – actionable review tool for architects and engineers to validate designs.
- **Best-Practice Guides** – covering microservices, resilience, service-to-service interactions, and security architecture patterns.
- **Secure API & Messaging Guidance** – practical recommendations for HTTP, gRPC, Kafka, AMQP, and event-driven designs.
- **Reference Implementations & Real-World Examples** – illustrating how principles are applied in practice.
- **OWASP-Hosted Presentations & Training Materials**

### ◽ Why This Project Matters

Security flaws introduced during system design are often the most costly and complex to remediate—sometimes requiring fundamental architectural changes that ripple through the entire product. By embedding **Secure-by-Design** practices early, we prevent these issues before they materialize, avoiding expensive rework and reducing risk exposure.

Modern systems are increasingly distributed, API-driven, and interconnected. This complexity amplifies potential attack surfaces, dependency risks, and failure modes. Without intentional, principle-based design, security controls become inconsistent, bolt-on, and fragile—leaving gaps that can be exploited.

This project provides a structured, repeatable, and scalable way to align architecture decisions with proven security principles. It empowers:

- **Architects and engineers** to make informed design choices with security built in.
- **Security teams** to engage earlier, influencing system shape rather than reacting to flaws.
- **Organizations** to meet regulatory and customer trust expectations without slowing delivery.

In short, it shifts security from reactive gatekeeping to proactive enablement, making it part of the natural flow of product design and evolution.

### ◽ Relationship to Other OWASP Projects

Secure-by-Design does not exist in isolation—it is a critical link between other OWASP security practices across the entire SDLC. As shown in the diagram, the process begins in the **Planning** phase, where formal security requirements are established (e.g., via [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/) requirements mapping). These define the baseline protections the system must meet. In the **Design** phase, the SbD framework applies structured principles, architectural patterns, and specific controls to address those requirements before any code is written. For high-risk or business-critical projects, an optional [**Threat Modeling**](https://owasp.org/www-community/Threat_Modeling) checkpoint follows to validate that chosen SbD controls mitigate the most critical threats and leave no significant risks unaddressed.

The lifecycle then flows into **Development**, where teams follow [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/) and other secure-coding standards to ensure implementation faithfully reflects the secure design. In the **Test** phase, security verification confirms that all layers—design, code, configuration, and deployment—work together as intended before release. This alignment embeds security at every stage, with SbD providing the architectural backbone that other OWASP practices reinforce. The result: systems that are secure by construction, not by retrofit.

![End-to-End Security Integration in the SDLC](/assets/images/Owasp_secure-by-design_sdlc_integration.png)

<p style="text-align: center; margin-top: -10px">End-to-End Security Integration in the SDLC</p>

#### ➤ Security Requirements

- **Scope:** Defined at the earliest possible stage (Planning) and tracked throughout the lifecycle.
- **Comparison:** Security requirements define _what_ protections the system must deliver; Secure-by-Design prescribes _how_ to embed those protections into the architecture.
- **How They Work Together:** Security requirements set explicit objectives (e.g., confidentiality, integrity, availability, privacy, compliance). SbD provides a disciplined method to translate each requirement into one or more architectural controls, ensuring every requirement is addressed in the blueprint before code is written.
- **Focus:** Requirements specify the “must-haves” (e.g., MFA, data encryption, retention limits). SbD maps these to concrete design elements and vetted patterns—such as least-privilege zones, encrypted channels, and circuit breakers—so the protections are enforced at an architectural level.
- **Outcome:**
  - **Security Requirements:** A vetted, testable list of statements.
  - **SbD:** A hardened architecture—data-flow and component diagrams annotated with controls—ready for secure implementation.
- **Methodology:**
  - **Security Requirements:** Elicit from stakeholders (business, legal, compliance, operations), map from regulations (GDPR, PCI-DSS) and standards, incorporate results of risk/threat analyses, and rank by impact. Update whenever designs change or new risks emerge.
  - **SbD:** Apply core principles (least privilege, defense in depth, secure defaults), select vetted patterns, and validate using a design-time checklist to ensure coverage of all requirements.
- **Documentation:**
  - **Security Requirements:** Captured in a formal Security Requirements Specification (SRS) and compliance/risk traceability matrix.
  - **SbD Artifacts:** Architecture Decision Records (ADRs), annotated diagrams referencing specific patterns, and completed SbD checklists.
- **Example:**
  - **Security Requirement:** “All users must authenticate with at least two factors (password \+ OTP) before accessing any dashboard or API”.
- **SbD Implementation:**
  - Place an OAuth2/OpenID Connect Identity Provider (IdP) in front of all services.
  - Configure MFA enforcement at first login and every 30 days thereafter.
  - Route all user logins through an API Gateway that validates and forwards JWT tokens to downstream services.
  - Document the IdP trust boundary in architecture diagrams; no service accepts credentials directly.

#### ➤ OWASP Threat Modeling

- **Scope:** Conducted after an initial design is complete, focusing on post-design risk analysis.
- **Comparison:** Threat Modeling assumes you already have an architecture to analyze; SbD creates that architecture with embedded controls from the outset.
- **How They Work Together:** By applying SbD first, threat modeling becomes faster and more targeted—identifying edge-case scenarios and advanced threats rather than exposing fundamental design flaws that require costly redesigns.
- **Purpose:**
  - **SbD:** Integrates security principles and proven patterns during design to proactively prevent classes of vulnerabilities.
  - **Threat Modeling:** Evaluates an existing design from an attacker’s perspective, identifying potential threats and determining mitigation strategies.
- **Focus:**
  - **SbD:** Principle- and pattern-driven, emphasizing service isolation, least privilege, data protection, and system resilience.
  - **Threat Modeling:** Risk- and adversary-driven, focusing on attack vectors, likelihood/impact analysis, and specific threat mitigations.
- **Outcome:**
  - **SbD:** Hardened architecture with built-in controls aligned to policies and standards.
  - **Threat Modeling:** Threat models, risk assessments, and updated mitigation plans for the implemented design.
- **Methodology:**
  - **SbD:** Apply principles like least privilege and secure defaults as part of architecture creation.
  - **Threat Modeling:** Use techniques such as STRIDE, DREAD, and attack trees to systematically identify and evaluate threats.
- **Documentation:**
  - **SbD:** Architecture diagrams, design specifications, and security requirement mappings.
  - **Threat Modeling:** Threat model diagrams, risk registers, and revised security requirements.
- **Examples:**
  - **SbD:** Define clear service boundaries, enforce TLS/mTLS, design resilient failure modes.
  - **Threat Modeling:** Analyze authentication workflows, assess third-party integrations, trace sensitive data flows for potential exposure points.

#### ➤ OWASP ASVS

- **Scope:** ASVS is a secure-coding and verification standard applied primarily during the Development and Testing phases to ensure implementations meet agreed-upon security controls.
- **Comparison:**
  - **SbD:** Focuses on _design-time_—embedding security into architecture before code is written.
  - **ASVS:** Focuses on _implementation-time_—verifying that the code, configuration, and deployed system meet specific security requirements.
- **How They Work Together:**
  - SbD produces a hardened blueprint that specifies which controls must be implemented. ASVS provides the verification criteria to confirm those controls are correctly implemented and effective in the running system.
- **Purpose:**
  - **SbD:** Prevents security gaps by addressing them in architecture and design.
  - **ASVS:** Detects implementation gaps or misconfigurations through structured verification.
- **Focus:**
  - **SbD:** High-level design controls (e.g., service isolation, mTLS enforcement, input validation at the gateway).
  - **ASVS:** Detailed verification of implementation aspects (e.g., code correctly validates tokens, TLS configuration resists downgrade attacks, input sanitization is robust).
- **Outcome:**
  - **SbD:** Annotated architecture and design artifacts with mapped security controls.
  - **ASVS:** A verified application meeting security control requirements at the implementation level.
- **Methodology:**
  - **SbD:** Apply design principles and patterns; capture decisions in ADRs and diagrams.
  - **ASVS:** Test and review the application against ASVS’s tiered verification levels (V1–V14 categories). Use automated scanning where applicable but rely heavily on manual verification for logic and security workflows.
- **Documentation:**
  - **SbD:** ADRs, architecture diagrams, control-to-requirement mapping.
  - **ASVS:** Verification reports, test results, defect logs, and remediation tracking.
- **Examples:**
  - **SbD:** Specify that all sensitive data at rest must be encrypted using AES-256 and managed keys in KMS.
  - **ASVS:** Verify that the database schema uses encrypted storage, the KMS integration is correctly configured, keys are rotated according to policy, and no sensitive data is stored unencrypted in logs or caches.

### ◽ Key Differentiators

- **Proactive by Design**: Embeds controls before code is written.
- **Pattern‑Centric**: Goes beyond abstract principles by providing concrete, reusable patterns for microservices, APIs, messaging, and integration scenarios.
- **Checklist‑Driven**: Offers a concise (≤ 40-item) control-oriented design checklist to enable fast, consistent reviews across teams.
- **Extensible**: Built for growth, with a modular structure that supports community contributions, reference architectures, and integration with future automation or tooling.

### ◽ What Is a Secure‑by‑Design Review?

A **Secure-by-Design (SbD) Review** is a structured assessment of an application’s architecture and design, performed _before development begins_, to ensure that security is engineered into the system from the outset.  
 It systematically examines components, data flows, interfaces, dependencies, and trust boundaries to verify that required security controls are built directly into the architectural blueprint.

**Typical outcomes include:**

- Annotated architecture and data-flow diagrams clearly showing applied controls, trust zones, and security-relevant boundaries.
- A completed SbD review checklist (see section SbD Review Checklist) summarizing implemented controls, residual risks, and gaps.
- Architecture Decision Records (ADRs) and a prioritized set of action items to address identified gaps.
- Risk-based recommendation on whether to run a Threat Modeling exercise before development begins.

### ◽ When and How Should It Be Considered?

SbD Reviews should be performed early and iteratively—during the Planning and Design phases, at the start of major epics, and whenever there is a significant architectural change (e.g., new external exposure, introduction of sensitive data, adoption of novel technology, or Tier-1 impact).

**Run an SbD Review whenever:**

- The architecture introduces new trust boundaries or external integrations.
- The system will handle regulated or sensitive data.
- The change could materially affect resilience, scalability, or security posture.

The **review process**, participant roles, and hand-off mechanics are described in **Section Secure-by-Design Process**.

### ◽ Expected Benefits

By applying this framework and running Secure-by-Design reviews, teams can:

- **Enhance Security Posture** – Reduce attack surface and eliminate entire classes of flaws early.
- **Ensure Compliance** – Align architectures with legal, regulatory, and internal security requirements from the outset.
- **Improve Reliability** – Design systems that are robust, scalable, and able to degrade gracefully under failure conditions.
- **Facilitate Collaboration** – Use the SbD Review Checklist as a concise hand-off artifact for rapid, targeted AppSec feedback.
- **Streamline Development** – Identify issues during design to minimize costly rework; produce auditable artifacts (diagrams, ADRs, checklists, threat models) with clear traceability from requirements to implemented controls.

---

## 🔹 SbD Process

The Secure-by-Design Process is the operational playbook for applying Secure-by-Design during the design phase and engaging with AppSec efficiently without creating bottlenecks. It describes the end-to-end process, what a design review examines, when to escalate (risk-based triggers), how the SbD Review Checklist fits into the workflow, and the mechanics for handing off to AppSec. See the **Process tab** for the complete content.

---

## 🔹 SbD Principles & Recommendations

SbD Principles & Recommendations provide authoritative design-time guidance for building systems that are secure by construction. It is organized into six domains (core principles, architecture & service design, data management & protection, reliability & resilience, access control & secure communication, monitoring/testing/incident readiness) and translates the project’s security requirements into concrete, architect-level controls, and provides actionable, design‑time recommendations that architects and product teams apply while shaping systems. See the **Principles tab** for the full content.

---

## 🔹 SbD Review Checklist

The SbD Review Checklist is a condensed, control-oriented tool for self-review and for communicating with AppSec. It ensures the security requirements are verified before implementation. Each item abstracts key recommendations from the principles into a Yes/No/N-A decision with justification. The full checklist appears on the **Checklist tab**; a CSV download is also provided there.

---

## 🔹 SbD Best Practices, Patterns & Reference Architectures

This is a living catalog of Secure-by-Design examples you can study and reuse. It illustrates how the Section Principles & Recommendations appear in real designs (trust boundaries, authN/Z, data protection, resilience, observability) without product‑specific noise. See the **Catalog tab** for the full content—reference architectures, reusable patterns, and contribution instructions.

---

## 🔹 Links

Download the full [Secure-by-Design Framework (PDF)](https://github.com/OWASP/www-project-secure-by-design-framework/tree/main/resources/docs/OWASP-Secure-by-Design-Framework-v0.5.pdf?raw=true)
