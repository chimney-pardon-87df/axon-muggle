---
name: muggle-product-glossary
description: "Terminology and definitions for the Self-Service App Federation Portal — identity protocols, federation concepts, and project codename"
argument-hint:
allowed-tools: Read, Grep, Glob
user-invocable: false
category: reference
skills: []
---

# Self-Service App Federation Portal — Glossary

| Term | Definition |
|------|------------|
| **Muggle** | Project codename. From the Harry Potter series — "muggles" are non-magical people, often used to describe those lacking specific skills or expertise in a particular field. The portal turns federation "muggles" (employees without identity infrastructure expertise) into self-sufficient operators who can federate their own apps without the infra team's specialized knowledge. |
| **IdP (Identity Provider)** | The system that authenticates users and asserts their identity to other applications. In this proposal, Okta is Axon's IdP. |
| **SP (Service Provider)** | The application that relies on the IdP for authentication. When an employee federates a vendor app, that app is the SP. |
| **SAML 2.0** | Security Assertion Markup Language. An XML-based protocol for exchanging authentication data between an IdP and SP. The most common enterprise SSO protocol. |
| **OIDC (OpenID Connect)** | An authentication protocol built on top of OAuth 2.0. A modern alternative to SAML, commonly used by newer applications. |
| **SWA (Secure Web Authentication)** | Okta's proprietary protocol for apps that don't support SAML or OIDC natively. Supported in Phase 2. |
| **ACS URL (Assertion Consumer Service URL)** | The endpoint on the SP where SAML assertions are posted. One of the key SP details needed to configure federation. |
| **Entity ID** | A globally unique identifier for either the IdP or the SP in a SAML federation. Both sides must agree on these values. |
| **Federation** | The process of establishing a trust relationship between an IdP and an SP so users can authenticate once (via the IdP) and access the SP without a separate login. |
| **OIN (Okta Integration Network)** | Okta's catalog of thousands of pre-built app integrations with known configuration profiles. Think of it as an app store for SSO integrations. |
| **OIN Index** | Gesherna's structured, indexed dataset of the full OIN catalog — app names, protocol support, configuration requirements, and setup patterns. Okta does not expose the OIN catalog via API; this index fills that gap. |
| **SCIM (System for Cross-domain Identity Management)** | A protocol for automating user provisioning and deprovisioning between systems. Supported in Phase 2. |
| **SP Infrastructure** | Service provider infrastructure. Relevant to Phase 3, where it provides programmatic SAML/OIDC endpoints on demand for internally built apps, so developers don't need to implement federation protocols themselves. |
| **Magic Link** | A tokenized, time-limited URL that grants a specific external party (e.g., a vendor) access to fill in their side of a federation configuration without needing an Okta account or Axon credentials. Supported in the Post-MVP Intelligence Layer. |

---

## Cross-References

- [Product Overview](product-overview.md)
- [Product FAQs](product-faqs.md)
- [Product Roadmap](product-roadmap.md)
