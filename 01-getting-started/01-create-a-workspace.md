---
title: Create a Workspace
description: Create a workspace in the accounts portal and collect its one-time enrollment token.
order: 1
---

# Create a Workspace

A workspace is the boundary your data plane connects to — every deployed
agent belongs to exactly one workspace, and every workspace belongs to your
tenant. You create it before you deploy anything.

## Prerequisites

You need a login to the accounts portal. If you don't have one yet, submit an
access request; once an admin approves it, a tenant and your first login are
created for you.

## Create the workspace

From the accounts portal, create a new workspace. It asks for:

- **A name.**
- **A data-plane domain**: the DNS zone your users' browsers reach the data
  plane under. See [Network and Browser Requirements](/docs/getting-started/network-and-browser-requirements).

There's no cloud provider or region picker, because Allodium doesn't provision
anything into your infrastructure on your behalf.

Creating the workspace generates:

- A workspace ID (`ws-` followed by a 10-digit number).
- A one-time **enrollment token**. This is shown once, at creation time — it
  is never stored in reversible form, so if you lose it you'll need to
  rotate it (the accounts portal has a "regenerate token" action, which
  invalidates the old one).
- The control-plane endpoint address the data plane will connect to.

The workspace starts in `provisioning` status. It has no compute, no catalog,
and no data yet — those come from your own object store and cluster once the
data plane is deployed and connects.

## Next

With a workspace ID, enrollment token, and control-plane endpoint in hand,
continue to [Deploy the Data Plane](/docs/getting-started/deploy-the-data-plane).
