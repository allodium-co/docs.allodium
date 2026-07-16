---
title: Create a Workspace
description: Provision a new workspace and its default catalog through the API.
order: 1
---

# Create a Workspace

Creating a workspace through the API provisions the tenant boundary, attaches it
to a data plane, and seeds it with a default catalog and role set — the same
result you'd get from the console's provisioning flow, but reproducible and
reviewable in version control.

## What you provide

At minimum you name the workspace and choose the data plane it should run in.
Optionally you can specify the initial administrators, a compute allocation, and
whether to seed a starter catalog. The control plane validates that the target
data plane is healthy and that the calling principal is allowed to create
workspaces there.

## What happens

The control plane records the desired state and hands provisioning to the agent
in the chosen data plane. The workspace first appears in a provisioning state,
then transitions to active once its catalog and compute are ready. Poll the
resource or subscribe to events to know when it's usable.

## Cleaning up

Retiring a workspace is symmetric: the API marks it for deletion, the agent
tears down its compute, and — subject to your retention policy — its catalog
entries are archived. Data in object storage is never deleted implicitly; that
remains an explicit, separately audited action.
