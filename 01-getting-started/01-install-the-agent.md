---
title: Install the Agent
description: Deploy the Allodium agent and register it with the control plane.
order: 1
---

# Install the Agent

The agent is the bridge between your infrastructure and the Allodium control
plane. It runs inside your environment, opens an outbound connection to the
control plane, and reconciles the desired state (workspaces, catalogs, compute
pools) against what actually exists. It never accepts inbound connections, which
keeps its network footprint small and auditable.

## Install

The agent ships as a single container image and a Helm chart that follows the
same per-service pattern as the rest of the platform. Deploy it into the cluster
or account you intend to manage, providing the enrollment token issued when you
created your organization.

Once running, the agent enrolls itself and appears in the control plane as a
pending node awaiting approval.

## Verify

A healthy agent reports its version, the region it's running in, and a heartbeat
timestamp that updates every few seconds. If the node stays in the pending state,
the most common causes are an expired enrollment token or blocked egress to the
control plane endpoint.

## Next

With the agent enrolled, you're ready to
[connect a data plane](02-connect-a-data-plane.md).
