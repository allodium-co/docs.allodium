---
title: Getting Started
description: Install the agent, connect your first data plane, and provision a workspace.
order: 1
---

# Getting Started

Allodium is a data platform you run in your own cloud. The control plane
orchestrates workspaces, catalogs, and governance; the data plane is where your
data actually lives and where compute runs. Nothing leaves your account without
you asking it to.

This section takes you from zero to a working environment. By the end you will
have the agent installed, a data plane connected and reporting healthy, and a
first workspace where your team can start querying data.

## What you'll do

1. **Install the agent.** A single lightweight process that registers your
   infrastructure with the control plane and keeps it in sync.
2. **Connect a data plane.** Point Allodium at the account and region where your
   data should reside, and confirm the handshake.
3. **Provision a workspace.** Create the first tenant boundary your team logs
   into, complete with a catalog and default roles.

## Before you begin

You'll need administrative access to the cloud account that will host the data
plane, permission to create service credentials, and outbound network access
from that account to the Allodium control plane. If you're evaluating rather
than deploying, the same steps work against a sandbox data plane.

Work through the pages in order — each one assumes the previous step succeeded.
