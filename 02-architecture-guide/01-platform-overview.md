---
title: Platform Overview
description: The control plane, data plane, and how a request travels between them.
order: 1
---

# Platform Overview

At the highest level, Allodium is two cooperating systems. The **control plane**
is the brain: it holds the catalog metadata, the governance policies, the user
directory, and the desired state of every workspace. The **data plane** is the
muscle: it holds the actual data in your object storage and runs the compute
that answers queries.

## How a request flows

When a user runs a query, the control plane authenticates them, checks the
governance policy for the tables involved, and resolves the physical location of
the data from the catalog. It then dispatches the work to a compute pool in the
correct data plane. Results are produced *in* the data plane and returned to the
user; the control plane never sees the row-level data, only the metadata and the
policy decisions.

## What this buys you

This separation gives you a single pane of glass across many environments while
guaranteeing data residency: a data plane in Frankfurt keeps its data in
Frankfurt regardless of where your administrators log in from. It also means the
blast radius of any one component is small — the control plane can restart
without touching running compute, and a data plane can operate on cached policy
if the control plane is briefly unreachable.
