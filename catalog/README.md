---
title: test
layout: null
tab: true
order: 6
tags: secure-by-design
---

## Secure-By-Design Best Practices, Patterns & Reference Architectures (Catalog)

The Secure-by-Design Catalog is a practical, growing library of examples that show how to embed security directly into system architecture. It includes both end-to-end reference architectures and smaller, reusable design patterns.

Each entry links to a self-contained case with its own README, diagrams, design notes, and—where applicable—runnable assets such as Terraform/Helm manifests, mock services, and test data. These examples demonstrate how the framework’s principles are applied in real designs so teams can adapt them with confidence.

The catalog is **opinionated but minimal**: its goal is to highlight how key controls—such as trust boundaries, authentication and authorization, data protection, resilience, and observability—are put into practice, without drowning readers in product-specific detail. As a community-extensible resource, new examples and improvements are always welcome.

### ◽ Reference Architectures (sample cases)

End-to-end sample systems.

- Samples list will be updated.

### ◽ Atomic Patterns Library (reusable building blocks)

Smaller, focused patterns you can drop into many designs.

- Samples list will be updated.

### ◽ Contribution

We welcome community contributions to the Secure-by-Design Best Practices, Patterns & Reference Architectures. If you would like to add a new sample pattern or reference case, please review the [Contributing Guidelines](https://github.com/OWASP/www-project-secure-by-design-framework?tab=contributing-ov-file).

If you are contributing specifically to the Catalog, please also ensure your submission considers the following additional points:

- Each case should include a README with overview, diagram, controls spotlight, design notes/ADRs, sample SbD review checklist, and operational notes.
- Trust boundaries must be explicit, with clear enforcement points.
- Secure defaults should be applied throughout (TLS, least privilege, validation at first hop).
- Include a completed SbD review checklist (see Section 4 SbD Review Checklist in the main doc for reference).

### ◽ Disclaimer

Samples are **educational**. Always validate against your project’s security requirements, threat model, and regulatory context. Adapt configs and policies before deploying to production.
