---
title: Desired State and Reconciliation
description: The path from a declared warehouse in the workspace portal to a running Trino cluster.
order: 1
---

# Desired State and Reconciliation

## From a declaration to a database row

When you declare a warehouse size in the workspace portal, the control
plane resolves that size to a concrete shape — heap size, worker mode,
replica bounds — against a defaults table it maintains, and writes it as a
row scoped to your workspace. The agent never sees the size label you
picked; it only ever receives the resolved shape.

## Push, not poll

That write fires a database trigger, which notifies a dispatcher process.
The dispatcher reassembles your workspace's full desired compute state —
every warehouse and Spark cluster you've declared — into a single message,
and pushes it down the open connection to your agent. It's always a full
snapshot, not a diff: the latest push simply wins, and the same snapshot is
re-sent whenever your agent reconnects, so there's no drift to reconcile
between control-plane and agent state.

## Reconciling in your cluster

The agent writes what it receives to a custom resource in your cluster,
which is its own local source of truth from that point on. A reconciler
loop — running on a timer and on change events — continuously compares that
declared state against what's actually deployed, and converges real Trino
and Spark workloads to match: creating, resizing, or removing them as
needed. Because this loop runs entirely in your cluster, it keeps
converging on the last known desired state even if the connection to the
control plane drops — a network blip doesn't take your compute down.

## What comes back

As it reconciles, the agent reports the observed status of each workload
(starting, running, stopped, error) and periodic resource usage back to the
control plane, which is what powers the health view in the workspace portal
and metered billing. Neither of those reports ever contains anything about
the data those workloads are processing — see
[the trust boundary](/docs/architecture-guide#the-trust-boundary).
