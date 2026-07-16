---
title: Governance & Security
description: Policies, identity, audit logging, and compliance controls.
order: 4
---

# Governance & Security

Governance in Allodium is not a bolt-on module — it's the layer every request
passes through. Whether someone browses the catalog, runs a query, or calls the
API, the same identity, policy, and audit machinery applies. This section
explains how those controls work and how to configure them for your compliance
needs.

## The three pillars

- **Identity** — who the caller is. Principals map to your identity provider, so
  people and services authenticate with credentials you already manage.
- **Policy** — what the caller may do. Access is expressed as policy against
  catalog objects, evaluated centrally by the control plane on every request.
- **Audit** — what actually happened. Every meaningful action is recorded in an
  append-only log you can export and retain.

## Data residency as a control

Because data never leaves the data plane, geographic and regulatory boundaries
are enforced by architecture rather than by policy alone. A data plane pinned to
a region keeps its data there; governance policy then layers per-object access
control on top.

Read [identity and access](01-identity-and-access.md) for the policy model, then
the [audit](audit/index.md) sub-section for how activity is recorded.
