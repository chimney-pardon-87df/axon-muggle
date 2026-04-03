---
name: muggle-product-features-phase3
description: Detailed feature documentation for Phase 3 — programmatic federation, SP infrastructure, and ephemeral app support for internally built AI applications
argument-hint:
allowed-tools: Read, Grep, Glob
user-invocable: false
category: product
skills: []
---

# Phase 3 — AI-Generated App Federation — Features

As Axon's AI-first initiative matures, teams will move beyond procuring third-party SaaS and begin building internal, AI-generated applications. These apps also need identity governance, but the federation model is fundamentally different: there is no vendor providing SP details, and apps may be ephemeral or rapidly iterated. Phase 3 introduces programmatic federation, SP infrastructure, and ephemeral app support to address this shift.

## Programmatic Federation

Programmatic federation is API-driven app integration creation designed for internally built apps. Instead of manual metadata exchange between a vendor and an Okta admin, app developers call an API and receive a federated identity endpoint. There is no manual SP detail entry, no ticket, and no back-and-forth with a vendor — because the "vendor" is an internal team or an AI-generated tool.

The workflow is a single API call. The API caller is both the app developer and the integration creator. Developers call a single API to register an internally built app and receive a federated identity endpoint. This replaces the multi-step portal form used in Phase 1 for third-party SaaS apps.

## SP Infrastructure

The SP infrastructure generates SAML 2.0 and OIDC endpoints on demand for each app, with Okta remaining the IdP (Identity Provider). App developers do not need to implement SAML or OIDC themselves — the SP infrastructure provides protocol endpoints so the app receives identity tokens without building protocol support.

The SP infrastructure acts as the SP (Service Provider) layer. When an internally built app registers through the portal API, the SP infrastructure generates the protocol endpoints — SAML ACS URL, OIDC redirect URIs, and related configuration — and registers them with Okta. The app authenticates users through these SP-generated endpoints, which federate back to Okta. The app never interacts with Okta directly.

## Ephemeral Apps

Ephemeral apps are app integrations that are short-lived, matching the lifecycle of AI-generated tools that may exist for a project or a sprint rather than permanently. In an AI-first engineering environment, a team might generate a tool for a specific analysis, use it for two weeks, and discard it. These tools still need identity governance while they exist.

The portal supports creating and cleaning up ephemeral integrations through two lifecycle mechanisms:

- **TTL (time-to-live):** When an app is registered programmatically, it can be given a TTL. When the TTL expires, the integration is cleaned up automatically.
- **Project lifecycle linking:** An app integration can be linked to a project lifecycle. When the project ends, the integration is decommissioned.

When an app is decommissioned — whether by TTL expiration, project lifecycle completion, or explicit removal — the portal removes both the Okta integration and the SP endpoints. This automatic cleanup prevents orphaned configurations from accumulating. No orphaned Okta configurations remain from decommissioned internal apps.

## Portal Evolution

Phase 3 represents a platform transition. The portal evolves from a tool that automates Okta configuration into an identity layer that sits between Okta and the app ecosystem:

- The **SP infrastructure** provides the service provider layer.
- **Okta** provides the IdP.
- The **portal** is the control plane — it manages the creation, configuration, and lifecycle of federated identity across both third-party SaaS (Phases 1 and 2) and internally built apps (Phase 3).

The portal becomes an identity control plane, not just an Okta configuration tool.

## How Phase 3 Differs from Phase 1

Phase 1 automates the creation of integrations for third-party SaaS apps where a vendor has provided SP details. The workflow involves a multi-step portal form, vendor-provided metadata, and manual configuration steps.

Phase 3 removes the vendor from the equation entirely. There is no vendor providing SP details — the API caller is both the app developer and the integration creator. The workflow is a single API call, not a multi-step portal form. The SP infrastructure generates the protocol endpoints (SAML ACS URL, OIDC redirect URIs) that would otherwise come from a vendor. The app authenticates through these generated endpoints, which federate back to Okta, and the app never interacts with Okta directly.

Phase 1 establishes the portal, the Okta API integration, and the core workflows. Phase 3 extends that foundation in a different direction — from automating vendor-provided integrations to generating the SP layer itself for apps that have no vendor.

## Dependencies

Phase 3 is independent of Phase 2 but benefits from it. Phase 2 introduces OIN intelligence and SWA support for third-party apps. Phase 3 addresses a different axis: internally built apps.

Both require Phase 1 as the foundation. Phase 1 establishes the portal, the Okta API integration, and the core workflows that Phase 2 and Phase 3 extend in different directions. Phase 3 requires Phase 1; it does not require Phase 2.

## Deliverables and Success Criteria

### Deliverables

- **API-driven app integration creation:** Developers call a single API to register an internally built app and receive a federated identity endpoint.
- **SP infrastructure:** Generates SAML 2.0 and OIDC endpoints on demand for each app, with Okta remaining the IdP. App developers do not need to implement SAML or OIDC themselves.
- **Ephemeral app support with lifecycle management:** Integrations can be given a TTL or linked to a project lifecycle, with automatic cleanup of Okta integrations and SP endpoints when the app is decommissioned.
- **Portal evolution:** Portal evolves from an Okta configuration tool into an identity control plane that manages federation across both third-party SaaS (Phases 1-2) and internally built apps (Phase 3).

### Success Criteria

- Internal app developers can register apps and receive federated identity endpoints via a single API call.
- SP-generated endpoints successfully federate back to Okta.
- Ephemeral app integrations are automatically cleaned up at end of lifecycle.
- No orphaned Okta configurations from decommissioned internal apps.
