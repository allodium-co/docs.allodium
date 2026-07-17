---
title: Compute Orchestration
description: How a declared warehouse or Spark cluster becomes running infrastructure in your data plane.
order: 2
---

# Compute Orchestration

This is the one piece of real orchestration the control plane does: turning
a compute declaration — "a medium Trino warehouse," "a Spark cluster with
four executors" — into an actual running deployment in your cluster, and
keeping it that way.

Allodium does not provide or orchestrate data ingestion. Getting data into
your Iceberg tables is up to whatever tooling you already use — a Spark job,
dbt, or anything else that can write to the catalog. What Allodium
orchestrates is the compute that queries those tables once they're there.

Read [desired state and reconciliation](/docs/architecture-guide/compute-orchestration/desired-state-and-reconciliation)
for how a declaration in the workspace portal actually reaches your cluster.
