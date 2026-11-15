---
title: Deploy the Data Plane
description: Run the Terraform module against your own Kubernetes cluster to install and connect the agent.
order: 2
---

# Deploy the Data Plane

The data plane is your own Kubernetes cluster plus an S3-compatible object
store you already control. Deploying it installs the Allodium agent and the
compute services (Polaris, Trino, Spark) into that cluster — Allodium never
reaches into your infrastructure to do this for you.

## Prerequisites

- A Kubernetes cluster you administer, with a default `StorageClass` for
  persistent volumes.
- Credentials for an S3-compatible object store (endpoint, access key,
  secret key, region) — any provider that speaks the S3 API works, this is
  not tied to a specific cloud.
- Outbound network access from that cluster to the control-plane endpoint
  from the previous step. The data plane runs a default-deny egress network
  policy, so you must pass the CIDR range the control-plane endpoint
  resolves to — without it, the agent is silently unable to reach the
  control plane.
- The workspace ID, enrollment token, and control-plane endpoint from
  [Create a Workspace](/docs/getting-started/create-a-workspace).

## Deploy

Run the published Terraform module against your cluster, supplying:

- `control_plane_endpoint`, `workspace_id`, `enrollment_token` — from the
  workspace you just created.
- `s3_endpoint`, `s3_access_key`, `s3_secret_key`, `s3_region` — your own
  object store credentials.
- `control_plane_cidrs` — the CIDR the control-plane endpoint resolves to,
  so the default-deny egress policy admits the connection.
- `catalog_admin_group` — the group in your identity provider that
  administers the catalog. You choose it here, in your own configuration;
  Allodium never stores it. See
  [Catalog Admin Group](/docs/governance-and-security/catalog-admin-group).

The module installs two Helm releases into your cluster: one for storage
(Postgres) and one for compute (Polaris, Trino, Spark, and the agent itself).
No image builds and no manual `kubectl apply` are required.

## What happens on connect

Once the agent starts, it opens a single outbound connection to the control
plane and presents the workspace ID and enrollment token. The control plane
checks the token against the one it issued, and on success:

- The workspace flips from `provisioning` to `active`.
- The agent begins reporting a heartbeat and the observed state of your
  compute workloads.

The connection is agent-initiated and outbound-only — the control plane never
opens a connection into your cluster.

## Verify

The workspace's status in the accounts portal turns green once the agent has
connected and sent a heartbeat within the last few minutes. If it stays on
`provisioning`:

- Confirm `control_plane_cidrs` actually covers the control-plane endpoint's
  resolved address — this is the most common cause of a silently blocked
  connection.
- Confirm the enrollment token wasn't rotated or mistyped; a mismatched token
  is rejected outright.

## Next

Once the workspace is active, log into the workspace portal to define a
catalog and roles — that happens there, not as part of this deploy.
