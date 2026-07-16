---
title: Authentication
description: Obtain and use API credentials, and understand how requests are scoped.
order: 1
---

# Authentication

Every request to the Allodium API carries a credential that identifies the
calling principal and determines what it's allowed to do. Authentication and
authorization are the same system that governs the console, so an API token can
never do more than the principal behind it could do interactively.

## Credentials

Create an API credential from the console or from another API call, scoped to a
specific principal. Credentials can be scoped down further — to a single
workspace or to read-only operations — so that automation holds the least
privilege it needs. Treat credentials like passwords: store them in a secret
manager, rotate them on a schedule, and revoke any that leak.

## Making a request

Send the credential with each request. The control plane resolves it to a
principal, evaluates the governance policy for the resources you're touching,
and either fulfills the request or returns an authorization error explaining
which policy denied it.

## Expiry and rotation

Credentials can carry an expiry. Short-lived credentials are strongly preferred
for automation — pair a rotation job with the API so tokens are refreshed before
they lapse rather than living indefinitely.
