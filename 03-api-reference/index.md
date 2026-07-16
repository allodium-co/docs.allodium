---
title: API Reference
description: Endpoints, authentication, and schemas for automating your platform.
order: 3
---

# API Reference

Everything you can do in the Allodium console you can also do through its API.
The API is how teams automate provisioning, wire Allodium into CI/CD, and manage
platforms as code rather than by clicking through a UI.

This section documents authentication, the resource model, and the endpoints for
each object type. The API is versioned and stable within a major version;
breaking changes ship under a new version prefix, never silently.

## Shape of the API

The API is organized around the same resources you see in the console:
workspaces, catalogs, data planes, principals, and policies. Each resource
supports the usual lifecycle operations, and every response includes the
metadata the control plane uses internally so that automation can make the same
decisions the UI does.

## Where to start

- Read [authentication](01-authentication.md) first — every request needs a
  credential.
- Then explore the [workspaces](workspaces/index.md) sub-section for a worked
  example of managing a resource end to end.
