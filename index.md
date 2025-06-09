---

layout: col-sidebar
title: OWASP Secure by Design Framework
tags: example-tag
level: 2
type: documentation
pitch: A very brief, one-line description of your project

---

OWASP Secure by Design Framework provides structured guidance on embedding security during the architecture and system design phase. Unlike OWASP Top 10, ASVS, or secure coding guidelines, this project does not cover secure coding, vulnerabilities, or security assessments. Instead, it ensures that:

* Architectural patterns enforce least privilege, isolation, and resilience.
* Microservice boundaries and API gateways follow strong security models.
* Messaging and event-driven security are designed upfront rather than patched later.
* Service communication and dependencies are structured to minimize risk.
* Data protection, encryption strategies, and compliance considerations are built into the architecture.

What this project does not cover:

* No secure coding practices (covered by OWASP ASVS and Top 10).
* No implementation-phase security (focuses only on design-phase security decisions).
* No vulnerability assessments (not a vulnerability-focused project).
* No threat modeling (Secure by Design lays the foundation, while threat modeling assesses a completed design for risks).

How this complements OWASP Threat Modeling:

* Secure by Design comes first to enforce security at the foundation.
* Threat Modeling follows to assess the architecture for risks before development starts.
* Without Secure by Design, Threat Modeling often reveals preventable security gaps, leading to costly redesigns.

This project fills a missing gap in OWASP by ensuring security is built into architecture before development begins, reducing the need for later security fixes and assessments.

### Road Map
Phase 1: Initial Draft (3-5 Months)

* Define core secure design principles.
* Structure the framework into modules (e.g., service isolation, API security, data security, etc.).
* Publish a draft document for early feedback.

Phase 2: Community Contributions (5-10 Months)

* Open the project for public review and contributions.
* Add real-world case studies and best practices.
* Conduct OWASP meetups and discussions to refine guidance.

Phase 3: Formal OWASP Framework Release (10-18 Months)

* Publish Version 1.0 of the OWASP Secure by Design Framework.
* Integrate with other OWASP projects (SAMM, ASVS, Cheat Sheets).
* Develop training materials and workshops.

Phase 4: Continuous Improvement

* Adapt the framework based on new threats and architectural trends.
* Collaborate with OWASP Threat Modeling, SAMM, and ASVS to ensure alignment.
