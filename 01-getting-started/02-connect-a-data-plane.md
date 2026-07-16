---
title: Connect a Data Plane
description: Register a data plane with the control plane and verify the handshake.
order: 2
---

# Connect a Data Plane

A **data plane** is the account and region where your data is stored and where
compute runs. Keeping data planes separate from the control plane is what lets
Allodium orchestrate your platform without ever holding your data — the control
plane issues instructions, the data plane executes them locally.

## Register

From the control plane, create a new data plane and select the cloud provider
and region. Allodium generates a scoped set of credentials describing exactly
the permissions it needs — object storage for the lakehouse, compute for query
engines, and networking for internal traffic. Apply those credentials in the
target account so the agent can provision resources on your behalf.

## Verify the handshake

Once the credentials are in place, the agent completes a handshake with the
control plane: it provisions a small probe, confirms it can read and write to
object storage, and reports back. A connected data plane shows a green status,
its storage bucket, and available compute capacity.

If the handshake fails, check that the credentials carry the full permission set
and that the region you selected matches where the credentials are valid.

## Next

You now have somewhere for data to live. Continue to
[provision a workspace](../02-architecture-guide/index.md) once you understand
how the pieces fit together, or create one directly from the control plane.
