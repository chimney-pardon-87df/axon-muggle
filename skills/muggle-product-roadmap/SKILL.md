---
name: muggle-product-roadmap
description: Phased delivery plan from MVP through Phase 3, including future capabilities and phase dependencies
argument-hint:
allowed-tools: Read, Grep, Glob
user-invocable: false
category: product
skills: []
---

# Self-Service App Federation Portal — Product Roadmap

## Phase Dependencies

```
Phase 1 (MVP) ──── required for all subsequent phases
     │
     ├── Post-MVP Intelligence Layer (enhances Phase 1, benefits from Phase 2's OIN Index)
     │
     ├── Phase 2 (OIN Index & Templates) ──── independent of Phase 3
     │
     └── Phase 3 (AI-Generated App Federation) ──── independent of Phase 2
                                                               but benefits from it

Future Capabilities ──── build on any combination of Phases 1-3
```

Phase 1 is the foundation. Phases 2 and 3 can be pursued independently or in parallel. The intelligence layer enhances Phase 1 and becomes significantly more powerful when combined with Phase 2's OIN Index, but does not strictly require it.

---

## Phase 1 — MVP

The core application. A web app that wraps the Okta Admin API into a guided, user-friendly experience for registering third-party SAML and OIDC app integrations.

### Okta SSO Auth

Portal access via Okta SSO. Users authenticate with existing Axon credentials.

- Portal registered as an OIDC application in Okta
- Axon's MFA, session, and conditional access policies apply automatically
- No separate user registration or credential management
- Foundational infrastructure — fully delivered in MVP, prerequisite for every other feature
- **Success criteria:** Any Axon employee with an active Okta account can access the portal without creating a new account or providing separate credentials

### App Integration Creation

SAML 2.0 and OIDC app integrations created in Okta via the Admin API.

- User selects protocol and enters SP details (ACS URL/Entity ID for SAML; redirect URIs for OIDC)
- Portal creates the integration in Okta and returns IdP details to the user
- Input validation prevents malformed configurations
- **Success criteria:** Users can create working SAML 2.0 and OIDC app integrations without infra team involvement

### Config Exchange

Two-way exchange of IdP and SP configuration details between portal and vendor.

- After integration creation, portal surfaces IdP-side configuration: SSO URL, IdP Entity ID, signing certificate (SAML); client ID, client secret (OIDC)
- Values presented clearly for user to copy and deliver to vendor
- Manual handoff in MVP
- **Success criteria:** Users receive all IdP details needed to complete federation with their vendor, no infra team involvement required

### Federation Validation

Automated testing via test user creation, federation execution, and auditable verification.

- Test user creation in Okta, assignment to the app integration, automated federation test execution
- Pass/fail results recorded in Okta's system logs for audit purposes
- On failure, detailed error surfacing and routing back to config exchange
- **Success criteria:** Every new app integration is validated via automated federation test before being considered complete

### Attribute Mappings

Sensible defaults for standard user attributes (email, first name, last name).

- Automatic default attribute mappings applied at integration creation time
- No user configuration required
- **Success criteria:** Every new integration includes correct attribute mappings for email, first name, and last name without manual input

### App Management

Basic ownership model — the user who creates the integration is the owner.

- Ownership tied to the authenticated identity from Okta SSO
- User assignment deferred to existing Axon workflows
- **Success criteria:** Every integration has a clear owner, established automatically

---

## Post-MVP Intelligence Layer

This layer adds AI capabilities on top of the Phase 1 foundation. It does not replace Phase 1 — it makes it smarter.

### Vendor Collaboration

Magic links for vendor delegation and iterative, stateful configuration exchange.

- **Magic link generation:** Axon employees generate tokenized, time-limited URLs scoped to a specific integration. Vendors use the link to access a vendor-facing portal and provide SP details directly. No Okta account or Axon credentials required.
- **Vendor-facing portal:** A scoped interface accessible via magic link. Vendors see only the fields relevant to their side of the configuration.
- **Stateful, iterative configuration tracking:** Each update from either side is recorded with chronological history of what was provided, what changed, and by whom.
- **Notification on vendor completion:** Axon employee is notified when vendor completes their side.
- **Token management:** Time-limited, scoped links. Expired links can be regenerated. Revocation supported.
- **Enhanced by Phase 2:** When Phase 2's OIN Index is available, vendor-provided details can be validated against known-good configurations. Discrepancies are flagged with targeted follow-up prompts.
- **Success criteria:** Vendors can complete their side of federation without Okta accounts. Time-to-federation reduced by eliminating manual back-and-forth.

### Claude Intelligence

Documentation-first onboarding and intelligent mediation of the config exchange.

- **Documentation parsing and protocol detection:** Claude reads vendor setup documentation (URL or uploaded file), determines SAML or OIDC, selects the appropriate form.
- **SP detail extraction and form pre-fill:** Claude extracts SP details from vendor docs and presents a pre-filled form for confirmation. Fields that could not be extracted are clearly marked for manual entry.
- **Intelligent mediation of vendor exchanges:** Claude interprets vendor-specific terminology, maps it to Okta fields, surfaces discrepancies for human resolution.
- **Mismatch detection and guided resolution:** Flags wrong certificate format, trailing slashes in Entity IDs, attribute name discrepancies, protocol mismatches, missing or misformatted redirect URIs.
- **Enhanced by Phase 2:** With OIN Index, Claude validates vendor-provided details against known-good configurations and generates precise follow-up questions (e.g., "The OIN template expects the ACS URL at /sso/saml, but the vendor provided /saml/acs. Which is correct for your instance?").
- **Success criteria:** Users can complete federation by providing a link to vendor docs rather than manually entering SP details. Failed federation attempts due to misconfiguration measurably reduced.

---

## Phase 2 — OIN Integration & Templates

Consume the Okta Integration Network catalog to power an intelligent template experience. Adds protocol coverage, provisioning, governance, and operational tooling.

### OIN Integration

OIN Index, template-driven registration, and validation against known-good configs.

- **OIN Index integration:** Connect the portal to Gesherna's structured dataset of the full OIN catalog (app names, protocol support, configuration requirements, setup patterns). Continuously maintained. Licensed to Axon as a Gesherna-owned data product.
- **Template-driven registration:** For OIN-listed apps, users select the app by name and receive a pre-configured integration with validated defaults. No manual SP detail entry required. Covers thousands of SaaS applications.
- **Validation layer:** Cross-reference user-provided or vendor-provided details against known-good OIN configurations. Flags mismatches (incorrect ACS URLs, mismatched entity IDs, unsupported protocol selections, missing attributes, certificate format issues) before integration creation.
- **Fallback to manual entry:** Non-OIN apps use the Phase 1 manual flow. No user is blocked by the absence of an OIN listing.
- **Success criteria:** OIN Index queryable with sub-second response times. Template-driven registration covers top OIN-listed apps. Reduction in failed or misconfigured integrations.

### Infra Dashboard

Visibility into all self-service registrations — audit trail, protocol distribution, app health.

- **Audit trail:** Complete, searchable record of all self-service registrations (who, when, what protocol, what configuration).
- **Protocol distribution view:** Visual breakdown by protocol (SAML, OIDC, SWA).
- **App health metrics:** Real-time status of active integrations including connectivity and configuration integrity.
- **Certificate lifecycle integration:** Surface expiration timelines with advance visibility for renewal planning.
- **Success criteria:** Infra team can view all self-service registrations in a single consolidated view. Unhealthy integrations surfaced within minutes.

### SCIM

SCIM provisioning for automated user lifecycle management.

- Enable SCIM-based user provisioning for apps that expose SCIM endpoints
- Core SCIM operations: create, update, deactivate user accounts based on Okta assignment changes
- Managed through the same portal interface used for federation
- OIN Index surfaces SCIM support information per app
- **Success criteria:** SCIM provisioning configurable through the portal. Create/update/deactivate operations execute successfully. Provisioning events logged and visible in infra dashboard.

### SWA

Secure Web Authentication support for apps that don't support SAML or OIDC natively.

- SWA app integration creation via Okta Admin API through the portal
- Template support for known SWA apps via OIN Index
- Browser plugin configuration guidance as part of SWA setup flow
- SWA is a fallback protocol for legacy or simple web apps — less secure than true federation but better than users managing credentials independently
- **Success criteria:** SWA integrations created for both OIN-listed and custom apps. No regression to SAML/OIDC capabilities.

### Approval Workflows

Gating for app integration creation — require approval before integrations go live.

- **Approval workflow engine:** Configurable system that holds integrations in pending state until approved or rejected.
- **Configurable approval chains:** Manager, security team, infra team, or combination. Multi-step chains supported. Tailored to Axon's organizational structure.
- **Optional gating:** Policies define which categories require approval and which proceed freely. Self-service remains frictionless for low-risk integrations.
- **Infra dashboard integration:** Approval decisions, pending requests, and rejection reasons visible alongside registration and health data.
- **Success criteria:** Approval workflows activated without disrupting existing self-service flows. Approval turnaround time measurable.

---

## Phase 3 — AI-Generated App Federation

As Axon's AI-first initiative matures, teams will move beyond procuring third-party SaaS and begin building internal, AI-generated applications. These apps also need identity governance, but the federation model is fundamentally different: there is no vendor providing SP details, and apps may be ephemeral or rapidly iterated.

### Programmatic Federation

Programmatic federation, SP infrastructure, and ephemeral app support.

- **API-driven app integration creation:** Developers call a single API to register an internally built app and receive a federated identity endpoint. No manual SP detail entry, no ticket, no vendor back-and-forth.
- **SP infrastructure:** Generates SAML 2.0 and OIDC endpoints on demand for each app, with Okta remaining the IdP. App developers do not need to implement SAML or OIDC themselves. The SP infrastructure provides protocol endpoints so the app receives identity tokens without building protocol support.
- **Ephemeral app support:** Integrations can be given a TTL or linked to a project lifecycle. Automatic cleanup of Okta integrations and SP endpoints when apps are decommissioned, preventing orphaned configurations.
- **Platform transition:** The portal evolves from an Okta configuration tool into an identity control plane. SP infrastructure provides the service provider layer; Okta provides the IdP. The portal manages federation across both third-party SaaS (Phases 1-2) and internally built apps (Phase 3).
- **Dependencies:** Requires Phase 1 as foundation. Independent of Phase 2 but complementary.
- **Success criteria:** Internal app developers can register apps and receive federated identity endpoints via a single API call. Ephemeral integrations automatically cleaned up at end of lifecycle. No orphaned Okta configurations.

---

## Future Capabilities

These extend naturally from the platform and represent additional value for Axon and future licensees. Listed in approximate order of complexity and dependency.

| Capability | Description | Builds On |
|------------|-------------|-----------|
| **Access Reviews** | Periodic recertification workflows. App owners confirm whether assigned users still need access, supporting compliance requirements. | Phase 1 |
| **App Health Dashboard** | Federation posture at a glance — total federated apps, protocol breakdown, certificate expiration timeline, apps with no active users, growth trends. | Phase 1 + Phase 2 |
| **Shadow IT Discovery** | Integration with proxy/network logs to surface SaaS apps employees are already using that aren't federated. Proactive recommendations to bring ungoverned apps under Okta. | Phase 1 + Phase 2 |
| **Multi-IdP Support** | Abstract the IdP layer so the portal works with Okta, Entra ID, Ping, or others. Expands the addressable market beyond Okta-only shops. | Phase 1 |
| **Bilateral Automated Federation** | An AI agent configures both sides of the federation — drives the vendor's admin console to complete SP-side config while simultaneously configuring the IdP side in Okta. Zero human configuration on either side. | Phase 1 + Intelligence Layer + Phase 2 |

---

## External Context: Axon First

Axon First is Axon's internal build-before-buy initiative — the formal successor to DOVE and DOLE. This engagement aligns with and reinforces the Axon First thesis. Each phase of the portal maps to the Axon First framework:

- **Phase 1 (MVP):** "Can we prototype it?" — Yes, and the prototype becomes the product under the co-development model.
- **Post-MVP (Intelligence Layer):** Claude Enterprise as the accelerant, matching Axon First's positioning of Claude as the tool enabling rapid prototyping.
- **Phase 2 (OIN Integration):** OIN Index as domain expertise Axon cannot easily replicate internally — the "leverage" answer to the third Axon First question.
- **Phase 3 (Programmatic Federation):** Platform transition creating durable infrastructure that scales with Axon's AI-first initiative.
- **Co-Development Model (All Phases):** IT spend reframed as investment. Axon gets source code (build ethos), Gesherna brings domain expertise (leverage), Axon earns revenue share (investment, not expense).

---

## Feature Detail

For detailed feature documentation including full mechanics, success criteria, and deliverables per feature, see the phase-level feature skills:

- **Phase 1 MVP Features** — `muggle-product-features-mvp`
- **Post-MVP Intelligence Features** — `muggle-product-features-mvp-post`
- **Phase 2 Features** — `muggle-product-features-phase2`
- **Phase 3 Features** — `muggle-product-features-phase3`
