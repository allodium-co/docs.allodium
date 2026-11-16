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
- An S3-compatible object store with an existing bucket for this data plane,
  and an access key for it. Any provider that speaks the S3 API works; this
  is not tied to a specific cloud.
- A certificate for `*.<data-plane domain>`: an existing TLS Secret in the
  cluster, or a cert-manager issuer that can issue one.
- Outbound network access from the cluster to the control plane, your object
  store and your Kubernetes API server.
- The `allodium-<workspace id>.auto.tfvars` file from
  [Create a Workspace](/docs/getting-started/create-a-workspace).

## Deploy

Run the published Terraform module against your cluster. Its inputs come from
two places.

**The issued file.** The `allodium-<workspace id>.auto.tfvars` file you
downloaded when you created the workspace sets the workspace ID, enrollment
token, control-plane endpoint, data-plane domain, workspace origin and token
settings. Put it next to your Terraform configuration unchanged. Terraform
loads it automatically.

**Your own tfvars.** Set these yourself, for example in `terraform.tfvars`.
The issued file lists them in a comment at the end:

| Input | What it is |
|---|---|
| `tls` | The certificate for `*.<data-plane domain>`: an existing TLS Secret (`{ secret_name = "lakehouse-tls" }`) or a cert-manager issuer. |
| `catalog_admin_group` | The group in your identity provider that administers the catalog. Allodium never stores it. See [Catalog Admin Group](/docs/governance-and-security/catalog-admin-group). |
| `chart_version` | The data-plane release to install. |
| `postgres_password` | The admin password for the catalog's Postgres. |
| `s3_endpoint`, `s3_bucket`, `s3_access_key`, `s3_secret_key` | Your object store and an existing bucket for this data plane's tables. `s3_region` is optional and defaults to `us-east-1`. |
| `object_store_cidrs` | The CIDRs `s3_endpoint` resolves to. |
| `control_plane_cidrs` | The CIDRs that `control_plane_endpoint` and `jwks_uri` in the issued file resolve to. |
| `api_server_cidrs` | Your cluster's Kubernetes API server endpoints, from `kubectl get endpoints kubernetes -n default`. |

The data plane denies outbound traffic by default, and network policies match
addresses, not host names. So outside the cluster, the data plane can reach
only the addresses in the three CIDR inputs: your object store, the control
plane, and your Kubernetes API server.

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
