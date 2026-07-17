---
title: Platform Overview
description: What the control plane actually holds, what the data plane holds, and how a query really runs.
order: 1
---

# Platform Overview

Allodium is two cooperating systems, but not split the way "control plane
vs. data plane" often implies. The **control plane** holds tenancy and
workspace state and the desired shape of your compute — not your data, and
not your catalog. The **data plane** — your own Kubernetes cluster plus your
own object storage — holds the data, runs the catalog, and runs the compute
that actually answers queries.

## How a query really runs

A query does not route through the control plane. A user or BI tool connects
directly to Trino running in your data plane, through a per-workspace
address exposed from your own cluster; Trino authenticates that connection
itself against your identity provider and enforces access against the
catalog it already has open, locally. The control plane is not in that path
and never sees the query, its results, or the tables involved.

One honest caveat: the workspace portal today includes a SQL editor and
catalog browser that run *through* the control-plane process, for
convenience — a query typed there executes in your data plane's Trino the
same way, but the request and its results currently pass through the
control plane on their way back to your browser rather than connecting you
to Trino directly. The control plane still doesn't store any of it, but this
is a narrower exception to "the control plane is never in the query path"
than the general case above, and worth knowing if you're reasoning about
exposure precisely.

## What actually crosses the control-plane/data-plane boundary

Only two things: a **desired compute shape** (how large a Trino warehouse or
Spark cluster should be) pushed down to the agent in your cluster, and
**status plus resource usage** reported back up for billing and health. See
[the trust boundary](/docs/architecture-guide#the-trust-boundary) for what
that deliberately excludes.

## What this buys you

Because your data, catalog, and compute all live in infrastructure you
control, a data plane in a given region keeps its data there regardless of
where the control plane itself runs. It also limits blast radius: the agent
in your cluster keeps reconciling the compute you've already declared even
if the control plane is briefly unreachable, and a control-plane restart
doesn't touch running Trino or Spark workloads.
