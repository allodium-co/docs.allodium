---
title: Architecture Guide
description: The control plane and data plane split, and the trust boundary between them.
order: 2
---

# Architecture Guide

Allodium is two cooperating systems: a **control plane** that orchestrates
compute, and a **data plane** — your own Kubernetes cluster and your own
object storage — where your data actually lives, where your catalog lives,
and where queries actually run.

## The layers at a glance

- **Storage and catalog, entirely in your data plane.** Your data sits in
  your object storage as Iceberg tables, described by a catalog (Apache
  Polaris) that also runs in your cluster. Allodium provisions it; Allodium
  does not hold it.
- **Compute, orchestrated by the control plane, run by your data plane.**
  You declare a Trino warehouse or Spark cluster; the control plane turns
  that into a compute shape and pushes it to an agent running in your
  cluster, which reconciles real Trino and Spark deployments against it.
- **Governance, split by what it governs.** Who can create a workspace or
  approve a tenant is a control-plane concern. Who can read a given table is
  enforced entirely in the data plane, against your catalog, at query time.

## The trust boundary

This is the single most important thing to understand about the system: the
control plane and the agent in your cluster exchange exactly one kind of
message — a **desired compute shape** going down (how big a Trino warehouse,
how many Spark executors) and **status plus resource usage** coming back up.
Nothing else crosses that boundary. Query text, query results, table and
column metadata, catalog grants, and storage credentials are never part of
that exchange — this isn't a policy choice made at the UI layer, it's
enforced by the shared schema the two sides speak: a compute-shape message
has no field to put any of that in.

Start with the [platform overview](/docs/architecture-guide/platform-overview)
for how the two planes fit together, then read
[compute orchestration](/docs/architecture-guide/compute-orchestration) for
how a declared warehouse becomes a running Trino cluster.
