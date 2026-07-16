---
title: Identity & Access
description: Map principals to your IdP and control access with policy on catalog objects.
order: 1
---

# Identity & Access

Access control in Allodium starts with identity and ends with policy. Every
actor — a person or an automated service — is a **principal**, and every
principal is authorized against **policy** attached to catalog objects.

## Identity

Principals map to your existing identity provider, so you don't run a second user
directory. People sign in through your IdP; service principals authenticate with
scoped credentials. Group membership from your IdP flows through, which lets you
express policy in terms of teams rather than individuals.

## Access policy

Policy is attached to catalog objects — catalogs, schemas, and tables — and
grants principals specific capabilities such as reading a table, writing to it,
or managing its grants. Policy is evaluated centrally on every request, so a
grant added or revoked takes effect immediately and consistently across the
console, queries, and the API.

## Least privilege by default

New principals start with no access. You grant capabilities explicitly, ideally
to groups, and lean on short-lived credentials for automation. Because the same
policy engine governs every access path, there's no back door that bypasses it —
a principal that can't read a table in the console can't read it through the API
either.
