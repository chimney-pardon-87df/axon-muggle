---
name: muggle-product-overview
description: What the Self-Service App Federation Portal is, the problem it solves, and how it works across all phases
argument-hint:
allowed-tools: Read, Grep, Glob
user-invocable: false
category: product
skills: []
---

# Self-Service App Federation Portal

> **Project Codename: Muggle** — from the Harry Potter series, where "muggles" are non-magical people. This portal turns federation muggles into self-sufficient operators.

## The Problem

Axon's corporate infrastructure team is the bottleneck for app federation. Every time an employee procures a third-party SaaS application, the infra team must manually configure it in Okta — setting up SAML or OIDC, configuring attribute mappings, managing certificates, and assigning users. As Axon's AI-first initiative accelerates the pace of app adoption across the organization, this manual process doesn't scale. Tickets back up, employees wait, and shadow IT fills the gap.

The core issue: the infra team's expertise is required for a process that could be systematized, and their involvement in every integration creates a bottleneck that slows the entire organization.

## The Solution

A self-service web application that enables any Axon employee to federate their own third-party apps within Okta — without involving the infrastructure team.

The portal wraps the Okta Admin API into a guided, user-friendly experience for registering third-party SAML and OIDC app integrations. Users authenticate with their existing Axon credentials, provide the service provider details from their vendor, and the portal handles the rest — creating the integration, returning the identity provider details, and validating that federation actually works before real users depend on it.

The infra team is removed from the critical path entirely. They retain full visibility in Okta but are no longer the gatekeepers for every integration.

As the product matures beyond MVP, intelligence replaces manual effort: users paste a link to vendor documentation instead of filling in forms, Claude extracts the right values, and the back-and-forth with the vendor becomes AI-mediated. But the MVP delivers value immediately with a clean, guided manual flow.

## Who This Is For

**Primary users:** Axon employees who procure or manage third-party SaaS applications and need those apps federated with Okta for single sign-on.

**Secondary beneficiaries:** Axon's corporate infrastructure team, who are freed from the bottleneck of manually configuring every integration while retaining full visibility and oversight.

**Vendors:** Third-party SaaS providers who need to exchange federation configuration details with Axon. In later phases, the portal streamlines the vendor side of the exchange as well.

## MVP User Flow

1. User authenticates to the portal via Okta SSO
2. Selects a protocol (SAML or OIDC) and enters the SP details their vendor provided (ACS URL, Entity ID, redirect URIs, etc.)
3. The portal creates the app integration in Okta via the Admin API
4. The portal returns the IdP-side details (SSO URL, IdP Entity ID, signing certificate for SAML; client ID/secret for OIDC) for the user to provide back to the vendor
5. The portal creates a test user, assigns the app, and validates that federation actually works — generating an auditable record in Okta's logs
6. Once validated, the app is eligible for user assignment and federation is live

## What This Is Not

- **Not a replacement for Okta.** The portal sits on top of Okta and automates what the infra team does today. Okta remains the identity provider.
- **Not an access management tool (in MVP).** The MVP focuses on the federation bottleneck — getting apps configured and validated. User assignment follows existing Axon workflows.
- **Not a one-time project.** The portal is the foundation for a phased roadmap that includes AI-assisted onboarding, OIN catalog integration, and programmatic federation for internally built applications.

---

## Features by Phase

### Phase 1 — MVP

**Okta SSO Auth** — Portal access via Okta SSO. Users authenticate with their existing Axon credentials. The portal is registered as an OIDC application in Okta. Axon's MFA, session, and conditional access policies apply automatically. No separate registration or credential management required. If a user does not have an Okta account, they cannot access the portal — it relies entirely on Okta as the source of truth for identity.

**App Integration Creation** — SAML 2.0 and OIDC app integrations created in Okta via the Admin API. Users select a protocol and enter service provider details (ACS URL/Entity ID for SAML; redirect URIs for OIDC). The portal creates the integration and returns IdP details. Input validation prevents malformed configurations. Apps not in the OIN are handled manually; OIN template-driven registration is added in Phase 2.

**Config Exchange** — Two-way exchange of IdP and SP configuration details between the portal and the vendor. After integration creation, the portal surfaces the IdP-side configuration (SSO URL, IdP Entity ID, signing certificate for SAML; client ID and client secret for OIDC). In MVP, the user manually delivers IdP details to the vendor.

**Federation Validation** — Automated end-to-end testing via test user creation, app assignment, federation test execution, and auditable verification in Okta's system logs. Validates the entire chain — Okta configuration, protocol settings, and the vendor's SP setup — without touching real user accounts. On failure, detailed error surfacing routes the user back to config exchange.

**Attribute Mappings** — Sensible defaults for standard user attributes (email, first name, last name) applied automatically at integration creation time. No user configuration required. Custom attribute mappings are deferred to post-MVP, where users will pick from a predefined list of Okta user profile attributes with guardrails to prevent misconfiguration.

**App Management** — Basic ownership model. The user who creates the integration is the owner, established automatically based on the authenticated user's identity. User assignment is deferred to existing Axon workflows. Full lifecycle management (edit, deactivate, transfer ownership, certificate lifecycle) is delivered in Phase 2.

### Post-MVP Intelligence Layer

**Vendor Collaboration** — Magic links for vendor delegation: tokenized, time-limited URLs that grant vendor contacts scoped access to fill in their side of a federation configuration without Okta accounts or Axon credentials. Iterative, stateful configuration exchange tracks the full history of what was provided, what changed, and by whom. Notifications alert the Axon employee when the vendor completes their side.

**Claude Intelligence** — Documentation-first onboarding: users provide a link to vendor setup documentation, Claude reads it, determines the protocol (SAML or OIDC), extracts SP details, and presents a pre-filled form for confirmation. Manual entry becomes the fallback rather than the default. Intelligent mediation of the config exchange: Claude interprets vendor-specific terminology, maps it to Okta fields, and flags mismatches (wrong certificate format, trailing slashes in Entity IDs, attribute name discrepancies, protocol mismatches). The intelligence layer enhances Phase 1 — it does not replace it.

### Phase 2 — OIN Integration & Templates

**OIN Integration** — OIN Index (Gesherna's structured dataset of the full Okta Integration Network catalog), template-driven registration for OIN-listed apps (users select an app by name and receive a pre-configured integration with validated defaults), and a validation layer that cross-references user-provided details against known-good OIN configurations. Non-OIN apps fall back to the Phase 1 manual flow.

**Infra Dashboard** — Visibility tool for the infrastructure team: consolidated view of all self-service registrations with audit trail, protocol distribution (SAML vs OIDC vs SWA), app health metrics, and certificate expiration timelines. Purpose-built for the self-service portal context, not a replacement for Okta's admin console.

**SCIM** — SCIM provisioning for automated user lifecycle management (create, update, deactivate accounts in target applications based on Okta assignment changes). Federation handles authentication; SCIM handles provisioning. Not all apps support SCIM — the OIN Index surfaces SCIM support information per app.

**SWA** — Secure Web Authentication support for apps that do not support SAML or OIDC natively. SWA uses Okta's browser plugin to inject credentials into app login forms. A fallback protocol for legacy or simple web applications. Template support for known SWA apps via the OIN Index.

**Approval Workflows** — Optional governance layer that gates app integration creation behind configurable approval chains (manager, security team, infra team, or combination). Integrations in scope are held pending until approved or rejected. Policies define which categories of apps require approval and which proceed freely, keeping self-service frictionless for low-risk integrations.

### Phase 3 — AI-Generated App Federation

**Programmatic Federation** — API-driven federation for internally built, AI-generated applications. Developers call a single API to register an app and receive a federated identity endpoint. SP infrastructure generates SAML and OIDC endpoints on demand, with Okta remaining the IdP — app developers do not need to implement protocol support themselves. Ephemeral app support with TTL-based lifecycle management and automatic cleanup. The portal evolves from an Okta configuration tool into an identity control plane managing federation across both third-party SaaS (Phases 1-2) and internally built apps (Phase 3).

---

## Feature Detail

For detailed feature documentation including mechanics, success criteria, and deliverables, see the phase-level feature skills:

- **Phase 1 MVP Features** — `muggle-product-features-mvp`
- **Post-MVP Intelligence Features** — `muggle-product-features-mvp-post`
- **Phase 2 Features** — `muggle-product-features-phase2`
- **Phase 3 Features** — `muggle-product-features-phase3`

---

## About Gesherna

This product is proposed by Gesherna LLC (working name — final company name TBD). Gesherna specializes in enterprise identity infrastructure and AI-driven automation.
