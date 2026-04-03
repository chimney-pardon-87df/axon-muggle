---
name: muggle-product-features-phase2
description: Detailed feature documentation for Phase 2 — OIN integration, infra dashboard, SCIM provisioning, SWA support, and approval workflows
argument-hint:
allowed-tools: Read, Grep, Glob
user-invocable: false
category: product
skills: []
---

# Phase 2 — OIN Integration & Templates — Features

## OIN Integration

### What It Is and How It Works

The OIN Integration feature connects the portal to the OIN Index — Gesherna's structured, indexed dataset of the full Okta Integration Network catalog. The index includes app names, protocol support (SAML, OIDC, SWA), configuration requirements, and setup patterns for every app listed in the OIN.

Okta does not expose the OIN catalog via API. The OIN Index fills that gap. It is a Gesherna-owned data product, continuously maintained as the OIN catalog evolves — new apps are added, existing configurations are updated, and deprecated entries are flagged. Axon would be the first licensee. Axon licenses access to the index but does not own it and does not bear the maintenance burden.

The index powers **template-driven registration**: for OIN-listed apps, the user selects the app by name (e.g., "Salesforce") and the portal generates a pre-configured integration with validated defaults. No manual entry of Service Provider details is required. The user confirms rather than configures.

A **validation layer** cross-references user-provided or vendor-provided configuration details against known-good OIN configurations. It flags mismatches, inconsistencies, or potential errors before the integration is created in Okta. Examples of issues caught: incorrect ACS URLs, mismatched entity IDs, unsupported protocol selections, missing required attributes, and certificate format issues. Whether a flagged issue blocks creation or is treated as a warning depends on the severity and the specific mismatch.

When combined with the Claude intelligence layer, Claude can validate vendor-provided configuration details against known-good OIN configurations. If something does not match — for example, a vendor provides an ACS URL that differs from the OIN-listed default — Claude flags the discrepancy and asks targeted follow-up questions to resolve it. The validation layer provides deterministic, rule-based checks; Claude adds a conversational intelligence layer on top that can interpret ambiguous inputs, explain why something was flagged, and guide the user toward resolution. They are complementary.

For apps not found in the OIN Index, the portal falls back to the standard manual registration flow established in Phase 1 (MVP). No user is blocked by the absence of an OIN listing. Custom or internal apps that are not part of the OIN continue to use the manual flow.

### Key Details and Mechanics

- The OIN Index covers thousands of SaaS applications, including the most common enterprise tools.
- Template-driven registration eliminates manual entry of entity IDs, ACS URLs, certificate requirements, and other Service Provider details for OIN-listed apps.
- The validation layer is deterministic and rule-based; Claude intelligence is conversational and complementary — neither replaces the other.
- Updates to the OIN Index are continuous and delivered as part of the license.
- Protocol support information per app (SAML, OIDC, SWA, SCIM) is included in the index, enabling informed decisions about integration approach.

### Deliverables and Success Criteria

**Deliverables:**
- OIN Index integration — structured access to the full OIN catalog from the portal.
- Template-driven registration — select app by name, receive pre-configured integration with validated defaults.
- Validation layer — cross-reference configuration details against known-good OIN configurations, flag mismatches before integration creation.
- Fallback to manual entry — non-OIN apps use the Phase 1 manual registration flow with no regression.

**Success Criteria:**
- OIN Index is queryable from the portal with sub-second response times.
- Template-driven registration covers the top OIN-listed apps by enterprise adoption.
- Validation layer catches common configuration mismatches (incorrect ACS URLs, mismatched entity IDs, unsupported protocol selections) before integration creation.
- Non-OIN apps continue to function through the existing manual flow with no regression.
- Reduction in failed or misconfigured integrations compared to Phase 1 baseline.

### Dependencies and Interactions

- SWA apps in the OIN are included in the index, enabling template-driven SWA setup (see SWA section).
- SCIM support information per app is surfaced through the OIN Index, guiding users on provisioning availability (see SCIM section).
- Claude intelligence layer uses OIN data to power AI-assisted validation.
- Phase 1 manual registration flow serves as the fallback for non-OIN apps.
- Infra dashboard tracks template-based registrations in the audit trail.
- Approval workflows may define different policies for OIN-listed vs custom apps.

---

## Infra Dashboard

### What It Is and How It Works

The infra dashboard is a visibility tool built for Axon's infrastructure team. It provides a consolidated view of all self-service app registrations created through the portal — including an audit trail, protocol distribution (SAML vs OIDC vs SWA), and app health metrics.

The portal removes the infra team from the critical path of app federation. Self-service means employees no longer wait for infra to create integrations. But the infra team retains responsibility for the overall identity infrastructure. They need visibility into what is being created, by whom, and whether it is healthy. The dashboard provides that visibility without reintroducing the infra team as a bottleneck.

Okta's admin console shows all app integrations, but it is a general-purpose tool. The infra dashboard is purpose-built for the self-service portal context — it consolidates the specific information the infra team needs into a single view designed for oversight rather than configuration.

The infra team retains full admin access through Okta. They can intervene when needed but are no longer required for routine federation requests.

### Key Details and Mechanics

- **Audit trail** — Complete, searchable record of all self-service registrations: who created each integration, when, what protocol was used, and what configuration was applied. Every self-service action is logged.
- **Protocol distribution** — Visual breakdown of integrations by protocol (SAML, OIDC, SWA), helping the infra team understand the composition of the federated app portfolio.
- **App health metrics** — Real-time status of active integrations, including connectivity health and configuration integrity. Unhealthy or misconfigured integrations are surfaced within minutes of detection.
- **Certificate lifecycle tracking and expiration alerts** — The dashboard surfaces certificate expiration timelines from the certificate lifecycle tracking feature. The infra team gets advance visibility into upcoming expirations to prevent outages.
- **Alert capabilities** — Part of Phase 2 scoping. The baseline delivers visibility; alerting (e.g., notifications for expiring certificates or unhealthy integrations) is a natural extension.

The dashboard is not available in the MVP (Phase 1). In Phase 1, the infra team has visibility through Okta's native admin console. The dashboard is a Phase 2 deliverable that consolidates and presents this information in a purpose-built view. The MVP prioritizes removing the infra team from the critical path; a purpose-built dashboard is valuable but not essential to proving the self-service model.

### Deliverables and Success Criteria

**Deliverables:**
- Audit trail — complete, searchable record of all self-service app registrations.
- Protocol distribution view — visual breakdown of integrations by protocol.
- App health metrics — real-time status of active integrations including connectivity and configuration integrity.
- Certificate lifecycle integration — surface certificate expiration timelines with advance visibility for renewal.

**Success Criteria:**
- Infra team can view all self-service registrations in a single consolidated view.
- Audit trail is complete — every self-service action is logged and searchable.
- Protocol distribution accurately reflects the current state of all portal-created integrations.
- App health metrics surface unhealthy or misconfigured integrations within minutes of detection.
- Certificate expiration timelines are visible with sufficient lead time for renewal.

### Dependencies and Interactions

- Approval workflows integrate with the dashboard — approval decisions, pending requests, and rejection reasons are visible alongside registration and health data.
- SCIM provisioning events are tracked in the audit trail.
- OIN Integration template-based registrations are tracked in the dashboard.
- Federation validation results are surfaced in app health metrics.
- SWA integrations appear in the protocol distribution view with accurate protocol classification.

---

## SCIM Provisioning

### What It Is and How It Works

SCIM — System for Cross-domain Identity Management — is a protocol for automating user provisioning and deprovisioning between systems. When a user is created, updated, or deactivated in Okta, SCIM automatically pushes those changes to the federated app. This eliminates the need for manual account creation and removal in each individual SaaS application.

Federation handles **authentication** — proving who you are. SCIM handles **provisioning** — creating and managing your account in the target app. These are independent capabilities. With federation alone, users authenticate via Okta SSO, but their accounts in the target app must be created and managed separately (often by the app vendor or an admin). SCIM adds automation to that account management.

SCIM automates the user lifecycle in the target application:
- **Create** — When a user is assigned to an app in Okta, SCIM creates their account in the target app automatically.
- **Update** — When a user's attributes change in Okta (name, role, department, etc.), SCIM pushes those changes to the target app.
- **Deactivate** — When a user is removed from an app assignment or deactivated in Okta, SCIM deactivates or removes their account in the target app.

Together, federation and SCIM provide complete lifecycle management: users authenticate via SSO (federation), and their accounts are automatically created, updated, and deactivated (SCIM) as their status in Okta changes.

### Key Details and Mechanics

- Federation is typically configured first because it establishes the trust relationship between Okta and the target app. SCIM provisioning is then layered on top of that relationship.
- The target app must expose a SCIM endpoint — the app vendor must have built SCIM support into their product. Many major SaaS apps support SCIM (Salesforce, Slack, Zoom, Box, etc.), but not all.
- For apps without SCIM support, user provisioning remains manual on the vendor side. The portal does not change this limitation — it is a constraint of the target application.
- The OIN Index (Phase 2) includes SCIM support information per app. For apps not in the OIN, the vendor's documentation is the authoritative source.
- SCIM provisioning is managed through the same portal interface used for federation. Users who create a federation can optionally enable provisioning for SCIM-capable apps in the same workflow.
- SCIM is in Phase 2 because the MVP focuses on federation — the bottleneck the portal addresses first. Provisioning builds on federation and is added once the foundation is proven.

### Deliverables and Success Criteria

**Deliverables:**
- SCIM provisioning support — enable SCIM-based user provisioning for apps that expose SCIM endpoints. The portal manages the SCIM connection configuration alongside the existing federation setup.
- Automated user lifecycle management — support core SCIM operations: create, update, and deactivate user accounts in target applications based on Okta assignment and attribute changes.
- Portal integration — SCIM provisioning managed through the same interface as federation, with optional enablement for SCIM-capable apps in the same workflow.
- OIN Index integration — leverage the OIN Index to surface SCIM support information per app, guiding users on whether provisioning is available.

**Success Criteria:**
- SCIM provisioning can be configured through the portal for apps that support it.
- User create, update, and deactivate operations execute successfully via SCIM when triggered by Okta assignment changes.
- The portal clearly indicates which apps support SCIM and which do not.
- Provisioning events are logged and visible in the infra dashboard audit trail.
- No regression to existing federation (Phase 1) capabilities.

### Dependencies and Interactions

- Federation (Phase 1) is a prerequisite — you federate an app before you provision users to it.
- OIN Index provides SCIM support data per app, guiding users on provisioning availability.
- Provisioning events are visible in the infra dashboard audit trail.
- SCIM provisioning may trigger additional approval requirements in the approval workflow engine.
- App management (ongoing management of federated and provisioned apps) is a related capability.

---

## SWA (Secure Web Authentication)

### What It Is and How It Works

Secure Web Authentication (SWA) is Okta's proprietary protocol for apps that do not support SAML or OIDC natively. SWA is credential injection via a browser plugin — it stores the user's credentials for a target application in Okta's vault, and when the user accesses the app, Okta's browser plugin automatically fills in the login form with the stored credentials. The user gets a seamless login experience, and the credentials are managed centrally in Okta rather than by each individual user.

SWA is a fallback — a pragmatic solution for apps that cannot do better, not a preferred protocol.

SWA is less secure than true federation. SAML and OIDC use token-based authentication — the user's actual credentials are never shared with the target app. SWA stores and injects credentials, which means the credentials exist in Okta's vault and are transmitted to the app's login form. SWA is better than users managing their own credentials independently, but it does not provide the same security guarantees as SAML or OIDC. If an app supports SAML or OIDC, those protocols should always be used instead of SWA.

SWA is typically used for legacy web applications, simple web apps with form-based login, and niche tools that have not implemented SAML or OIDC. These are often older or smaller applications where the vendor has not invested in modern federation standards. SWA apps are typically a small fraction of the total app portfolio — the "long tail" of apps where the vendor has not built federation support.

### Key Details and Mechanics

- SWA requires the Okta browser plugin to be installed and configured on the user's machine.
- Many SWA-only apps are already cataloged in the OIN. The OIN Index includes these apps along with their SWA configuration details. Template-driven registration (Phase 2) can provide pre-configured SWA setups for known apps, reducing manual configuration.
- The OIN Index includes protocol support information per app. If an app only supports SWA (no SAML or OIDC), that information is surfaced, allowing informed decisions about integration approach.
- SWA is in Phase 2 because the MVP focuses on SAML and OIDC, which cover the vast majority of enterprise SaaS applications. SWA extends portal coverage to the remaining apps after the core federation experience is proven.
- The portal provides browser plugin configuration guidance as part of the SWA integration creation workflow — clear instructions for installing and configuring the Okta browser plugin.

### Deliverables and Success Criteria

**Deliverables:**
- SWA app integration creation — enable creation of SWA-based app integrations via the Okta Admin API through the portal for apps that do not support SAML or OIDC.
- Template support for known SWA apps — leverage the OIN Index to provide pre-configured SWA setups for OIN-cataloged apps, reducing manual configuration.
- Browser plugin configuration guidance — clear instructions for installing and configuring the Okta browser plugin, surfaced as part of the SWA integration creation workflow.

**Success Criteria:**
- SWA integrations can be created through the portal for both OIN-listed and custom apps.
- Template-driven registration works for SWA apps in the OIN Index, pre-populating configuration details.
- Users receive clear guidance on browser plugin requirements during the SWA setup flow.
- SWA integrations appear correctly in the infra dashboard with accurate protocol classification.
- No regression to existing SAML and OIDC federation capabilities (Phase 1).

### Dependencies and Interactions

- OIN Index provides templates for known SWA apps via template-driven registration.
- Infra dashboard tracks SWA integrations in the protocol distribution view.
- Phase 1 SAML/OIDC registration flow is the foundation; SWA extends protocol coverage.
- Federation validation provides context for SWA configurations.
- Approval workflows may differentiate policies for SWA integrations vs SAML/OIDC integrations.

---

## Approval Workflows

### What It Is and How It Works

Approval workflows are an optional gating mechanism that requires approval before a self-service app integration goes live. When an employee creates a new app federation through the portal, the proposed integration can be routed to one or more approvers for review. The approver — a manager, security team member, infra team lead, or other designated reviewer — examines the proposed integration and approves or rejects it.

While pending approval, the integration is not active. It exists as a pending request in the portal. The employee can see its status. The approver can see the full details of the proposed configuration before making a decision. If rejected, the employee is notified along with any feedback from the approver, and the integration is not created in Okta. The employee can revise and resubmit, or escalate through other channels.

The critical design point: **the MVP (Phase 1) has no approval gating**. Adding approval workflows in the MVP would reintroduce a human bottleneck — the very problem the portal is designed to solve. Phase 2 adds approval workflows as an optional governance layer once the self-service foundation is proven. At that point, the organization knows which categories of integrations benefit from oversight and which can proceed freely.

The MVP still has governance through Okta's existing mechanisms — Okta admin policies, group-based access controls, and the infra team's visibility through Okta's admin console. Approval workflows add a portal-native governance layer in Phase 2.

### Key Details and Mechanics

- **Configurable approval chains** — Approvers are configurable. Options include: the requesting employee's manager, a member of the security team, the infra team lead, or a combination (multi-step approval chain). Approval chains can include multiple steps — for example, manager approval followed by security team approval.
- **Optional gating** — Approval is not required for all integrations. Policies define which categories of apps require approval and which can proceed without it. Some categories might require approval (e.g., apps that handle sensitive data, apps requesting broad SCIM provisioning) while others can proceed freely (e.g., standard productivity tools with well-known OIN configurations). Self-service integrations that do not require approval continue to work with no additional friction.
- **Configurable policies** — Policies can differentiate between app categories (e.g., OIN-listed vs custom, SAML vs SWA, with SCIM vs without). Policies can be adjusted over time as the organization's comfort with self-service federation grows — tightened for new categories of risk, loosened for well-understood patterns.
- **Approval chains are updatable** — They can be changed after deployment as organizational needs change. The goal is governance that adapts to the organization, not the other way around.
- **Approval turnaround time is measurable** — Provides data to optimize governance policies over time.

### Deliverables and Success Criteria

**Deliverables:**
- Approval workflow engine — configurable workflow system that gates app integration creation behind one or more approval steps. Integrations in scope are held in a pending state until approved or rejected.
- Configurable approval chains — define who approves and in what order, tailored to Axon's organizational structure.
- Optional gating — policies define which categories require approval and which proceed freely, keeping self-service frictionless for low-risk integrations while adding oversight where needed.
- Infra dashboard integration — approval decisions, pending requests, and rejection reasons visible in the infra dashboard alongside registration and health data.

**Success Criteria:**
- Approval workflows can be configured and activated without disrupting existing self-service flows.
- Approval chains are flexible — support for single-approver and multi-step chains.
- Policies can differentiate between app categories (OIN-listed vs custom, SAML vs SWA, with SCIM vs without).
- Pending, approved, and rejected integrations are clearly tracked in the portal and the infra dashboard.
- Self-service integrations that do not require approval continue to work with no additional friction.
- Approval turnaround time is measurable, providing data to optimize governance policies over time.

### Dependencies and Interactions

- Infra dashboard displays approval activity — decisions, pending requests, rejection reasons.
- OIN-listed apps may have different approval requirements than custom apps.
- SCIM provisioning may trigger additional approval requirements (e.g., apps requesting broad SCIM provisioning).
- Phase 1 self-service flow has no approval gating — approval workflows layer on top without disrupting existing capabilities.
- Approval policies may differentiate by protocol (SAML, OIDC, SWA) and by whether SCIM provisioning is involved.
