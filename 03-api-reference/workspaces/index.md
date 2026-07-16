---
title: Workspaces API
description: Programmatically create, configure, and retire workspaces.
order: 1
---

# Workspaces API

A **workspace** is a tenant boundary: a place where a team logs in, with its own
catalog, roles, and compute allocation. The Workspaces API lets you manage that
lifecycle as code, which is how larger organizations stamp out consistent
environments for many teams.

This sub-section walks through the workspace resource — its fields, its
lifecycle states, and the operations available on it. Because a workspace ties
together a catalog, a set of principals, and a data plane, creating one through
the API is a good end-to-end tour of how the resources relate.

See [create a workspace](01-create-workspace.md) for a concrete example.
