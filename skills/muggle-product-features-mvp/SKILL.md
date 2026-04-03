---
name: muggle-product-features-mvp
description: Detailed feature documentation for Phase 1 MVP — Okta SSO auth, app integration creation, config exchange, federation validation, attribute mappings, and app management
argument-hint:
allowed-tools: Read, Grep, Glob
user-invocable: false
category: product
skills: []
---

# Phase 1 — MVP Features

The Self-Service App Federation Portal ships six features in its Phase 1 MVP. Together they form a complete self-service path: authenticate, create an app integration, exchange configuration with the vendor, validate that federation works, map user attributes, and establish ownership of the integration. Everything below is MVP-scoped unless explicitly marked otherwise.

---

## Okta SSO Authentication

### What it is and how it works

Okta SSO Authentication is the foundational access layer. The portal is registered as an OIDC application in Okta and uses standard OIDC/OAuth 2.0 to authenticate users. There is no separate user directory, no registration step, and no portal-specific credentials. Users navigate to the portal, get redirected to Okta's login page, sign in with their existing Axon credentials (including any MFA prompts), and are redirected back with an active session. If the user already has an active Okta session, they may not be prompted again — standard SSO behavior.

### Key technical details

- The portal inherits Axon's existing MFA policies, session policies, and conditional access rules automatically.
- Access is implicitly scoped to anyone with an active Okta account. If a user is not in Okta, they cannot access the portal — there is no fallback.
- The portal does not manage its own user directory. Okta is the sole source of truth for identity.

### MVP scope and success criteria

| | |
|---|---|
| **Deliverables** | Portal registered as an OIDC application in Okta. Users authenticate via Okta SSO using existing Axon credentials. Axon's MFA, session, and conditional access policies apply automatically. No separate user registration or credential management. |
| **Success Criteria** | Any Axon employee with an active Okta account can access the portal without creating a new account or providing separate credentials. Authentication inherits all existing Okta security policies. |

### What evolves later

Nothing. Okta SSO authentication is foundational infrastructure — it is fully delivered in MVP with no additional phases planned. It is a prerequisite for every other feature in the portal.

---

## App Integration Creation

### What it is and how it works

This feature enables Axon employees to create new third-party app integrations in Okta through the portal, without filing a ticket or involving the infrastructure team. The portal wraps the Okta Admin API to programmatically register SAML 2.0 and OIDC app integrations based on the service provider details the user provides.

The flow: user selects a protocol, enters the service provider details their vendor has given them, and submits. The portal translates those inputs into Okta Admin API calls to register the app, configure protocol settings, and return the identity provider details the user needs to complete federation with their vendor.

### Key technical details

**User inputs by protocol:**

- **SAML 2.0:** ACS URL (Assertion Consumer Service URL) and Entity ID at minimum. Additional settings such as NameID format and signature options may apply depending on the vendor.
- **OIDC:** Redirect URIs (login and logout), and any grant types or response types required by the vendor.

The portal validates inputs before creating the integration — malformed configurations are rejected before any API call is made. Created integrations are visible and manageable in Okta's admin console.

In MVP, all integrations are created manually — the user selects a protocol and enters the SP details regardless of whether the app exists in the Okta Integration Network (OIN). There is no template pre-population.

Apps that do not support SAML or OIDC cannot be federated through the portal in MVP and must still go through the infra team.

### MVP scope and success criteria

| | |
|---|---|
| **Deliverables** | Portal supports SAML 2.0 and OIDC app integration creation. User selects protocol, enters SP details (ACS URL/Entity ID for SAML; redirect URIs for OIDC). Portal creates the integration in Okta via Admin API and returns IdP details to the user. Input validation prevents malformed configurations. |
| **Success Criteria** | Users can create working SAML 2.0 and OIDC app integrations without infra team involvement. Created integrations are visible and manageable in Okta's admin console. |

### What evolves later (Phase 2)

- **SWA (Secure Web Authentication):** Support for apps that do not support SAML or OIDC. SWA is Okta's approach to password-based SSO for legacy or less capable applications.
- **OIN template-driven registration:** For apps in the Okta Integration Network catalog, the portal pre-populates protocol settings and SP details from the template, eliminating manual entry for many common apps and reducing configuration errors.

---

## Config Exchange

### What it is and how it works

The config exchange is the two-way handoff of federation configuration details between the portal (representing Okta as the IdP) and the third-party vendor (the SP). Federation requires both sides to have each other's configuration. The exchange works in three steps:

1. **User provides SP details:** The user enters the service provider details their vendor has given them (ACS URL, Entity ID for SAML; redirect URIs for OIDC). This happens during app integration creation.
2. **Portal returns IdP details:** After creating the integration in Okta, the portal surfaces the identity provider details the user needs to provide back to their vendor:
   - **SAML:** SSO URL, IdP Entity ID, and the signing certificate.
   - **OIDC:** Client ID and client secret.
3. **User delivers to vendor:** The user takes the IdP details and provides them to their vendor through whatever channel the vendor supports (admin console, support ticket, etc.).

### Key technical details

- In MVP, this is a manual handoff. The portal gives the user the values; the user is responsible for delivering them to the vendor.
- Values are presented clearly in the portal for the user to copy directly.
- No infra team involvement is required to retrieve IdP configuration.
- If the configuration needs revision after the initial exchange, the user must manually coordinate changes with their vendor and update the portal accordingly.

### MVP scope and success criteria

| | |
|---|---|
| **Deliverables** | After the user provides SP details and the integration is created, the portal surfaces the IdP-side configuration: SSO URL, IdP Entity ID, and signing certificate for SAML; client ID and client secret for OIDC. Values are presented clearly for the user to copy and deliver to their vendor. |
| **Success Criteria** | Users receive all IdP details needed to complete federation with their vendor. No infra team involvement required to retrieve IdP configuration. Users can copy values directly from the portal. |

### What evolves later (Post-MVP)

- **Vendor-collaboration via magic links:** Vendors can submit SP details directly to the portal, removing the user as the middleman for the configuration exchange.
- **Iterative stateful exchange:** Both sides can propose and accept configuration changes through the portal, with revisions tracked and managed.
- **Claude-intelligence mediation:** AI-mediated exchange that identifies configuration mismatches and suggests corrections, reducing back-and-forth cycles between user and vendor.

---

## Federation Validation

### What it is and how it works

Federation validation is an automated end-to-end test that verifies a newly created app integration actually works before real users depend on it. Rather than trusting that configuration is correct based on inputs alone, the portal proves it by executing a real federation flow.

**The validation sequence:**

1. The portal creates a temporary test user in Okta.
2. The test user is assigned to the app integration.
3. The portal executes an automated federation test — initiating an SSO flow for the test user against the app.
4. The result (success or failure) is captured and an auditable record is generated in Okta's system logs.
5. On success, the integration is marked as validated. On failure, the portal surfaces detailed failure information and routes the user back to the config exchange to resolve the issue.

### Key technical details

- **Test users** ensure federation works end-to-end without affecting real accounts. The entire chain is validated: Okta configuration, protocol settings, and the vendor's SP setup.
- **Failure details** include what went wrong, at what stage of the federation flow, and any error messages returned by Okta or the service provider. Validation can be re-run after changes are made.
- **Audit trail:** Federation validation events — test user creation, app assignment, federation test execution, and the pass/fail result — are recorded in Okta's system logs. These logs are Axon's existing source of truth for identity and access events, so validation records integrate naturally into Axon's compliance and audit workflows.

### MVP scope and success criteria

| | |
|---|---|
| **Deliverables** | Automated test user creation in Okta. Test user assignment to the app integration. Automated federation test execution against the configured app. Auditable verification — pass/fail results recorded in Okta's system logs. On failure, detailed error surfacing and routing back to the config exchange for resolution. |
| **Success Criteria** | Every new app integration is validated via an automated federation test before being considered complete. Validation results are recorded in Okta's system logs for audit purposes. Users receive actionable failure details when validation does not pass. |

### What evolves later

Federation validation is fully delivered in MVP with no additional phases currently planned. It is complete as shipped.

---

## Attribute Mappings

### What it is and how it works

Attribute mappings define how user profile fields are passed from Okta (the IdP) to the vendor's app (the SP) during federation. When a user signs in to a third-party app via SSO, the IdP sends a set of attributes — email address, first name, last name, etc. — that the SP uses to identify and provision the user. Attribute mappings control which fields are sent and how they map between the two systems.

### Key technical details

- **MVP defaults:** The portal automatically applies default attribute mappings for the three standard fields that virtually every SaaS application expects: **email, first name, and last name**. These defaults are applied at integration creation time — no user configuration required.
- **Design rationale:** Defaults reduce configuration errors and keep the UX simple. The vast majority of third-party apps need email, first name, and last name and nothing else. Allowing free-form customization in MVP would increase the surface area for misconfiguration without meaningfully expanding the set of apps that can be federated.

### MVP scope and success criteria

| | |
|---|---|
| **Deliverables** | Automatic default attribute mappings applied to every new integration: email, first name, and last name. No user configuration required — defaults are applied at integration creation time. |
| **Success Criteria** | Every new app integration includes correct attribute mappings for email, first name, and last name without manual input. Standard SaaS apps receive the attributes they need to identify and provision users. |

### What evolves later (Post-MVP)

- **Custom attribute mappings with guardrails:** Users can add custom attribute mappings beyond the defaults. Mappings are selected from a predefined list of Okta user profile attributes — not free-form entry. Guardrails prevent mapping nonexistent fields or exposing sensitive attributes. This balances flexibility with safety: users can map additional attributes their vendor requires, but they cannot introduce mappings that reference nonexistent fields or expose sensitive attributes.

---

## App Management

### What it is and how it works

App management establishes who is responsible for each integration and provides the foundation for lifecycle management. In MVP, the user who creates the integration is automatically the owner. Ownership is established at creation time based on the authenticated user's identity (from Okta SSO). The owner is the point of contact for the integration and has visibility into its status.

### Key technical details

**Ownership model (MVP):** Intentionally minimal. The focus is on establishing who is responsible for each integration. Full lifecycle management capabilities (edit, deactivate, transfer) are deferred to Phase 2.

**User assignment — the three options under consideration:**

The portal's focus is on solving the federation bottleneck (getting the app configured in Okta), not replacing Axon's existing processes for managing who has access to which apps. Three assignment models are under consideration:

1. **Pre-existing groups only:** Users can assign an Okta group that already exists, but cannot create new groups or enumerate users.
   - Limits scope; leverages existing group structure.
2. **Email list:** Users provide a list of email addresses to assign. The portal validates that the addresses correspond to Okta accounts without exposing the directory.
   - More flexible; still avoids full directory exposure.
3. **Defer to existing workflows:** The portal does not handle assignment at all. Users follow whatever process Axon already has for requesting app access.
   - Keeps the MVP focused on federation; avoids introducing a new assignment workflow that conflicts with Axon's existing processes.

**The recommendation is option 3 — defer assignment to existing workflows** — to keep the MVP focused on the federation bottleneck. Assignment can be layered in later based on Axon's preference.

**Certificate management (MVP):** Signing certificates are generated as part of SAML integration creation, but lifecycle management (renewal, rotation, expiration alerting) is deferred to Phase 2.

### MVP scope and success criteria

| | |
|---|---|
| **Deliverables** | Automatic ownership assignment — the user who creates the integration is the owner. Ownership is tied to the authenticated identity from Okta SSO. User assignment is explicitly deferred to existing Axon workflows. |
| **Success Criteria** | Every integration has a clear owner. Ownership is established automatically without additional user input. The portal does not introduce a new assignment workflow that conflicts with Axon's existing processes. |

### What evolves later (Phase 2)

- **App lifecycle management:** Owners can edit integration settings, deactivate integrations that are no longer needed, and transfer ownership to another Axon employee.
- **Certificate lifecycle management:** Tracking of SAML signing certificate expiration dates, alerting owners before certificates expire, and guided renewal workflows. Goal: no integrations break due to unnoticed expired certificates.
- **Assignment model:** TBD based on the decision between the three options above. Whichever model Axon selects will be implemented in Phase 2.
