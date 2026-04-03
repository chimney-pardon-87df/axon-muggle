---
name: muggle-customer-open-questions
description: "Open decisions for Axon — assignment model, API security, attribute customization, engagement model, and MVP scope"
argument-hint:
allowed-tools: Read, Grep, Glob
user-invocable: false
category: reference
skills: []
---

# Decisions for Axon

## Overview

The following decisions must be made collaboratively between Gesherna and Axon. They are listed with context and options to facilitate discussion.

---

## 1. User and Group Assignment Model

**The question:** Once an app integration is created and validated, who gets access to it?

**The constraint:** The portal must not become a directory browser. A bad actor could use a naive implementation to discover any user in the company. The assignment model must limit user visibility to what the registering user is already entitled to see.

| Option | Description | Pros | Cons |
|--------|-------------|------|------|
| **Pre-existing groups only** | User associates the app with Okta groups that already exist and that they have a relationship to (member, owner, manager) | No user search exposed; clean security model | Depends on Axon having good group hygiene; limited flexibility |
| **Email list** | User provides a list of email addresses. Portal validates they exist without exposing any other user data | No browsing or autocomplete; simple | Manual; no validation that the user should be granting access to those people |
| **Defer to existing workflows** | Portal creates the app integration only. Assignment happens through Axon's existing Okta processes (IT service desk, etc.) | Keeps MVP scope tight; sidesteps the security problem entirely | Doesn't fully remove the infra team from the process |

**Recommendation for discussion:** The "defer" option keeps the MVP focused on the federation bottleneck (which is the actual pain point) without taking on the access management problem. Assignment can be added in a later phase with proper security design.

---

## 2. API Token Scoping and Security

**The question:** The portal requires Okta Admin API access with privileges broad enough to create app integrations, manage users, and read group memberships. How is this token secured, scoped, and audited?

This is a conversation with Axon's infra and security teams. Gesherna will propose a security architecture during technical discovery, but Axon must define acceptable risk boundaries.

---

## 3. Attribute Mapping Customization

**The question:** Do we ship sensible defaults only (email, first name, last name), or allow users to customize SAML attribute statements and OIDC claims?

**Trade-off:** Defaults reduce configuration errors and keep the UX simple. Customization reduces the need to involve the infra team for edge cases where the vendor requires non-standard attributes.

**Recommendation for discussion:** Ship defaults in MVP. Add customization as a post-MVP capability with guardrails (e.g., pick from a predefined list of Okta user profile attributes rather than free-form entry).

---

## 4. Engagement Model

**The question:** How should this engagement be structured commercially?

See [Engagement Models](engagement-models.md) for three options.

**Key considerations:** Who owns the IP? Who maintains the product long-term? Does Axon want to participate in the commercial upside? How does Axon view the relationship between IT spend and investment?

---

## 5. Scope Boundaries for MVP

**The question:** Which Phase 1 features are must-haves for launch vs. fast-follows?

| Feature | Must-have or fast-follow? |
|---------|--------------------------|
| SAML support | Must-have |
| OIDC support | Must-have or fast-follow? |
| Federation validation testing | Must-have or fast-follow? |

---

## Cross-References

- [Engagement Models](engagement-models.md) — Commercial options in detail
- [Product Roadmap](../product-roadmap.md) — What's in each phase
- [Product FAQs](../product-faqs.md) — Common questions
