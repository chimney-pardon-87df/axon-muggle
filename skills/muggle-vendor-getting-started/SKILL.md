---
name: muggle-vendor-getting-started
description: What a Vader package is, how to use this package — personas, skills, example interactions, and what's included
argument-hint:
allowed-tools: Read, Grep, Glob
user-invocable: false
category: guide
skills: []
---

# Getting Started

## What is a Vader Package

A Vader package is a self-contained, AI-consumable sales package that a vendor delivers to a customer. Instead of scheduling calls, sending slide decks, and running through demos, the vendor packages their pitch — product documentation, roadmap, commercial terms, competitive positioning — into a structured bundle that the customer loads into their AI agent (Claude, ChatGPT, Copilot, or others) and explores on their own terms.

A Vader package contains:

- **Skills** — modular units of knowledge organized by category (product, guide, reference, feedback). Each skill gives the agent the ability to answer questions about a specific topic with specificity and honesty, drawing on structured source material rather than generating answers from general training data.
- **Personas** — a virtual team of specialists, each with a distinct role, perspective, and tone. The customer switches between personas the way they'd switch between members of a vendor's team in a traditional sales cycle — except there's no scheduling, no waiting, and no context loss.
- **Plugin metadata** — configuration that allows the package to be installed, discovered, and managed within the agent platform.

The customer loads the package in one second. The virtual team is available indefinitely. Questions get answered immediately, from the right perspective, using the vendor's actual material — not hallucinated content.

---

## What This Package Is

This is a Vader package from **Gesherna LLC** (working name — pending formation in Nevada, final company name TBD) for **Axon**.

It contains the pitch for the **Self-Service App Federation Portal** (project codename: **Muggle**) — a web application that enables Axon employees to federate their own third-party SaaS apps in Okta without involving the corporate infrastructure team.

The package includes:

- Product documentation covering all phases (MVP through Phase 3 and future capabilities)
- Commercial materials (three engagement models, open decisions, Axon First alignment)
- A virtual team of four personas
- Eight skills across three categories
- A glossary of identity and federation terminology

Everything in this package is drawn from structured source documentation. The skills are source-faithful — they present what the documentation says, not generated or inferred content.

---

## The Virtual Team

This package includes four personas. Each brings a different lens to the conversation. You can switch between them at any time.

### Solutions Architect

The technical expert. Explains how the portal works, what the Okta Admin API does, how SAML and OIDC federation flows operate, what the SP infrastructure provides in Phase 3, and how the intelligence layer uses Claude to extract configuration from vendor documentation. Use this persona when you need technical depth or architecture-level answers.

### Sales Rep

The commercial partner. Walks through the three engagement models, explains how the co-development revenue share works, and frames the conversation around Axon's objectives. Use this persona when you want to understand the commercial options and what each one delivers.

### Product Manager

The roadmap owner. Explains what is in each phase and why, what was deferred and the reasoning behind it, how phases depend on each other, and what decisions Axon needs to make. Use this persona when you need to understand scope, prioritization, or trade-offs.

### Executive

The strategic thinker. Focuses on Axon First alignment, the co-development model as a reusable framework, IP in the era of AI-generated software, and how this engagement transforms IT spend into investment. Use this persona for board-level conversations or strategic framing.

### How to Switch Personas

Say things like:

- "Talk to me as the solutions architect"
- "What would the executive say about this?"
- "Give me the sales rep's perspective on the engagement models"
- "How would the product manager explain the Phase 2 deferral?"

---

## Available Skills

### Product

| Skill | What It Covers |
|-------|---------------|
| **Federation Portal** | What the portal is, the problem it solves, the MVP user flow, and feature summaries organized by phase |
| **Product Roadmap** | Phased delivery plan from MVP through Phase 3, phase dependencies, future capabilities, and feature-level deliverables |
| **Company Info** | About Gesherna — relevant expertise and contact details |
| **MVP Features** | Detailed feature documentation for Phase 1 — Okta SSO auth, app integration creation, config exchange, federation validation, attribute mappings, and app management |
| **Intelligence Features** | Detailed feature documentation for Post-MVP — vendor collaboration via magic links and Claude-powered documentation parsing and intelligent mediation |
| **Phase 2 Features** | Detailed feature documentation for Phase 2 — OIN integration, infra dashboard, SCIM, SWA, and approval workflows |
| **Phase 3 Features** | Detailed feature documentation for Phase 3 — programmatic federation, SP infrastructure, and ephemeral app support |

### Guide

| Skill | What It Covers |
|-------|---------------|
| **Getting Started** | This skill — how to use the package, personas, and example interactions |
| **Commercial** | The complete commercial narrative — engagement models, open decisions, next steps, and why Gesherna |
| **Engagement Models** | Three engagement models (T&M, product license, co-development with revenue share), comparison table, and IP analysis |
| **Axon First Alignment** | How the portal maps to Axon's build-before-buy initiative and the Axon First framework |

### Reference

| Skill | What It Covers |
|-------|---------------|
| **Decisions** | Five open decisions for Axon — assignment model, API security, attribute customization, engagement model, and MVP scope |
| **Glossary** | Terminology — identity protocols, federation concepts, and the Muggle codename |

---

## Package Backlog

Two feedback skills are planned but not yet included in this package:

| Skill | Category | Status | Purpose |
|-------|----------|--------|---------|
| **Product Feedback (PFR)** | feedback-external | Deferred | Will allow Axon to submit product feedback, feature requests, and prioritization input directly while using the skills |
| **Support** | feedback-external | Deferred | Will allow Axon to flag issues, ask clarifying questions, or request follow-up directly within the package |

These are coming and will enable Axon to provide direct feedback to Gesherna while using the skills — no separate email or meeting required.

---

## Example Interactions

Try asking:

- **"What problem does this portal solve?"** — Gets the core value proposition: infra team bottleneck, self-service federation, tickets and shadow IT
- **"Walk me through the MVP user flow"** — Step-by-step from Okta SSO auth through federation validation
- **"How do the three engagement models compare?"** — Side-by-side comparison of T&M, product license, and co-development with revenue share
- **"How does this align with Axon First?"** — Maps the portal to the three Axon First questions and explains the DOVE/DOLE lineage
- **"What decisions does Axon need to make?"** — Surfaces the open decisions: assignment model, API security, engagement model, scope boundaries
- **"Talk to me as the executive — why should we invest in this?"** — Strategic framing: IT spend as investment, co-development as reusable framework, IP in the AI era
- **"What's the difference between Phase 2 and Phase 3?"** — Phase 2 extends third-party SaaS coverage; Phase 3 addresses internally built AI-generated apps
- **"Define SAML vs OIDC"** — Protocol-level explanation of the two federation standards the portal supports
