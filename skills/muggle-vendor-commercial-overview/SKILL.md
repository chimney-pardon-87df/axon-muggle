---
name: muggle-vendor-commercial-overview
description: The complete commercial narrative — engagement models, open decisions, next steps, and why Gesherna. The Sales Rep's primary skill.
argument-hint:
allowed-tools: Read, Grep, Glob
user-invocable: false
category: guide
skills: []
---

# Commercial Overview

This skill covers the full commercial side of the Self-Service App Federation Portal engagement: how it's structured, what Axon needs to decide, what happens next, and who Gesherna is.

---

## Engagement Models

Gesherna LLC (working name — final company name TBD) proposes three engagement structures for Axon's consideration. Each reflects a different stance on IP ownership, maintenance responsibility, cost structure, and commercial upside.

### Comparison

| Dimension | Option 1: T&M (Full Ownership) | Option 2: Product License | Option 3: Co-Development with Revenue Share |
|-----------|-------------------------------|--------------------------|---------------------------------------------|
| **Upfront cost** | Highest (full rate) | Lower (Gesherna retains IP) | Moderate (discounted rate) |
| **IP ownership** | Axon owns everything | Gesherna owns everything | Axon gets source code + internal use rights; Gesherna retains commercialization license |
| **Ongoing maintenance** | Axon's responsibility | Included in license | Axon self-maintains; benefits from Gesherna's product improvements |
| **Access to future phases** | Separate engagements required | Included as roadmap progresses | Available through Gesherna's product; Axon can also build independently |
| **OIN Index** | Axon owns their copy | Licensed from Gesherna | Licensed from Gesherna |
| **Revenue participation** | None | None | Axon earns ongoing revenue share from Gesherna's commercial sales |
| **Revenue share mechanics** | N/A | N/A | Split percentage pre-negotiated in the initial contract, carried into AWS/Azure Marketplace listing. Marketplace handles disbursement — no custom tracking or reconciliation. |
| **Best for** | Full control; internal capacity to maintain and evolve | Capability without maintenance burden | Source code access + commercial upside; IT spend reframed as investment |

### Option 1 — Time & Materials (Full Ownership)

Gesherna builds the portal to Axon's specifications. Axon owns the deliverable — codebase, architecture, and any derived data assets including the OIN index if developed.

- Billed at hourly/daily rate
- Axon owns all IP produced
- Axon responsible for ongoing maintenance, hosting, and evolution
- Future phases are separate engagements

### Option 2 — Product License

Gesherna builds, owns, and operates the portal as a product. Axon licenses it — similar to purchasing enterprise software.

- Lower upfront cost (Gesherna retains IP)
- Ongoing license fee includes maintenance, updates, and access to new features as the roadmap progresses
- Axon benefits from Phase 2/3 capabilities as they're developed, without funding the full R&D
- The OIN Index remains Gesherna IP, licensed as part of the product
- Gesherna can license the same product to other customers, improving the product for everyone

### Option 3 — Co-Development with Revenue Share

Gesherna builds the portal on a time & materials basis at a discounted rate. Axon receives the full source code and can use, modify, and deploy it internally. Gesherna retains a license to commercialize the IP as a product for other customers. In return, Axon receives a percentage of revenue from Gesherna's commercial sales of the product.

- Discounted T&M rate in exchange for Gesherna's commercialization rights
- Axon gets full source code and internal use rights
- Gesherna retains a license to productize and sell to other customers
- Axon earns ongoing revenue share from Gesherna's commercial sales
- Revenue share is transparent and verifiable: the split percentage is pre-negotiated and defined in the initial engagement contract. That rate is then carried directly into the marketplace listing. The product is listed on the AWS and Azure Marketplaces with Axon registered as a payee at the agreed rate. The marketplace handles disbursement — no custom royalty tracking, no quarterly reconciliation, no renegotiation. Both parties see the same numbers from day one.
- Axon is credited as the design partner — their real-world requirements shape the product
- Future product improvements driven by Gesherna's broader customer base benefit Axon as well

### IP in the Era of AI-Generated Software

Traditional IP ownership assumes the code itself is the moat — it took months or years to build, so owning it meant something. When AI can rebuild equivalent functionality in days, paying a premium for exclusive ownership of source code is paying for an asset with diminishing defensibility. The real value is in the problem definition, the architectural decisions, the OIN knowledge base, and the operational experience of maintaining federated identity at scale. Option 3 acknowledges this reality and builds an honest commercial relationship around it. Axon gets the source code as a safety net, earns from commercialization, and avoids paying a premium for exclusivity that doesn't meaningfully protect them.

This is bigger than one engagement. Gesherna may be the first vendor to deliver apps under Axon's AI-first initiative, but it won't be the last. The IP ownership question will come up with every external contractor who walks through the door. The reality is the same every time: a vendor can hand over source code and assign IP to Axon, but the vendor still learned how to build it, and AI makes it trivial to rebuild in a new form afterward. Traditional IP assignment creates an illusion of exclusivity that doesn't hold.

Option 3 establishes a reusable framework for how Axon engages external vendors in the AI era. Instead of paying full price for an IP asset with diminishing defensibility, Axon invests at a discount and earns ongoing revenue from commercialization. Every external engagement becomes a potential revenue stream, not just a cost center. This fundamentally changes Axon's posture toward external development — from "minimize vendor spend" to "find more problems worth solving, because each one is an investment." The co-development model doesn't slow Axon's AI-first initiative. It accelerates it.

---

## Decisions for Axon

The following decisions must be made collaboratively between Gesherna and Axon.

### 1. User and Group Assignment Model

**The question:** Once an app integration is created and validated, who gets access to it?

**The constraint:** The portal must not become a directory browser. A bad actor could use a naive implementation to discover any user in the company. The assignment model must limit user visibility to what the registering user is already entitled to see.

| Option | Description | Pros | Cons |
|--------|-------------|------|------|
| **Pre-existing groups only** | User associates the app with Okta groups that already exist and that they have a relationship to (member, owner, manager) | No user search exposed; clean security model | Depends on Axon having good group hygiene; limited flexibility |
| **Email list** | User provides a list of email addresses. Portal validates they exist without exposing any other user data | No browsing or autocomplete; simple | Manual; no validation that the user should be granting access to those people |
| **Defer to existing workflows** | Portal creates the app integration only. Assignment happens through Axon's existing Okta processes (IT service desk, etc.) | Keeps MVP scope tight; sidesteps the security problem entirely | Doesn't fully remove the infra team from the process |

**Recommendation for discussion:** The "defer" option keeps the MVP focused on the federation bottleneck (which is the actual pain point) without taking on the access management problem. Assignment can be added in a later phase with proper security design.

### 2. API Token Scoping and Security

**The question:** The portal requires Okta Admin API access with privileges broad enough to create app integrations, manage users, and read group memberships. How is this token secured, scoped, and audited?

This is a conversation with Axon's infra and security teams. Gesherna will propose a security architecture during technical discovery, but Axon must define acceptable risk boundaries.

### 3. Attribute Mapping Customization

**The question:** Do we ship sensible defaults only (email, first name, last name), or allow users to customize SAML attribute statements and OIDC claims?

**Trade-off:** Defaults reduce configuration errors and keep the UX simple. Customization reduces the need to involve the infra team for edge cases where the vendor requires non-standard attributes.

**Recommendation for discussion:** Ship defaults in MVP. Add customization as a post-MVP capability with guardrails (e.g., pick from a predefined list of Okta user profile attributes rather than free-form entry).

### 4. Engagement Model

**The question:** How should this engagement be structured commercially? See the Engagement Models section above for three options.

**Key considerations:** Who owns the IP? Who maintains the product long-term? Does Axon want to participate in the commercial upside? How does Axon view the relationship between IT spend and investment?

### 5. Scope Boundaries for MVP

**The question:** Which Phase 1 features are must-haves for launch vs. fast-follows?

| Feature | Must-have or fast-follow? |
|---------|--------------------------|
| SAML support | Must-have |
| OIDC support | Must-have or fast-follow? |
| Federation validation testing | Must-have or fast-follow? |

---

## Next Steps

### 1. Select Engagement Model

Review the three options and determine which aligns with Axon's commercial objectives. Option 3 (co-development with revenue share) is recommended for discussion as the model that best serves both parties — Axon gets source code and revenue share, Gesherna gets commercialization rights and a design partner.

This decision affects IP ownership, maintenance responsibility, access to future phases, and whether the engagement generates ongoing returns for Axon. It should be made before scoping begins, as the engagement model shapes what "done" looks like.

### 2. Scope the MVP

Confirm which Phase 1 features are must-haves for launch vs. fast-follows. All Phase 1 features (Okta SSO auth, SAML app integration creation, two-way config exchange, default attribute mappings, basic app ownership) are assumed must-haves. The open questions are OIDC support and federation validation testing. Vendor collaboration via magic links and iterative configuration exchange are Post-MVP Intelligence Layer — not in scope for Phase 1.

### 3. Technical Discovery

Gesherna and Axon's infra and security teams align on:

- **API token scoping and security** — what privileges the portal requires, how the token is secured, scoped, and audited
- **User and group assignment model** — which of the three options fits Axon's environment
- **Security architecture** — how the portal integrates with Axon's security posture
- **Hosting environment** — where the portal runs and how it's deployed

This is a working session, not a presentation. Both sides bring constraints and requirements to the table.

### 4. Define Acceptance Criteria

What does "done" look like for Phase 1? Acceptance criteria should cover:

- Functional requirements (can a user create a working SAML integration without infra team involvement?)
- Security requirements (API token scoped to minimum necessary privileges, audit trail in Okta system logs)
- Performance requirements (portal responsiveness, Okta API latency tolerance)
- Operational requirements (monitoring, alerting, error handling)

Acceptance criteria are defined collaboratively and documented before build begins.

### 5. Build Phase 1

Execute the MVP build. Delivery cadence, review process, and communication rhythm are established during scoping and discovery.

---

## Why Gesherna

> **Note:** Gesherna LLC is a working name. The business is pending formation in Nevada. The final company name is TBD.

Gesherna LLC specializes in enterprise identity infrastructure and AI-driven automation. The Self-Service App Federation Portal sits at the intersection of both.

### Relevant Expertise

- Deep experience with federated identity architecture (SAML, OIDC, SCIM) and Okta's platform
- Existing investment in SP infrastructure that provides programmatic SAML/OIDC endpoints for Phase 3
- The OIN Index — a Gesherna data product that makes intelligent federation possible at scale
- Desktop automation capabilities that enable the bilateral automated federation vision in the future roadmap
- A product vision that extends beyond a single customer — Axon would be the design partner for a product that addresses a gap every enterprise faces

### Contact

| Field | Value |
|-------|-------|
| **Entity** | Gesherna LLC (working name — pending formation in Nevada) |
| **Address** | 8545 W Warm Springs Rd, Suite A-4 #247, Las Vegas, NV 89113 |
| **Email** | muesteve@grayfox.media |
| **Phone** | 212.960.8133 |
