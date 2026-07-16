---
title: Audit Logs
description: The audit event model and how to export logs to your SIEM.
order: 1
---

# Audit Logs

Allodium records an audit event for every meaningful action: authentication,
policy changes, catalog changes, workspace lifecycle operations, and access
decisions on queries. Each event captures the principal, the action, the target
object, the outcome, and a timestamp — enough to reconstruct a sequence of
events after the fact.

## What's captured

Both successful and denied actions are logged. A denied query attempt is often
more interesting than a successful one, so the log records the policy decision
and which policy produced it, not just the fact that access was blocked.

## Getting logs out

Audit events are available through the API and can be streamed to your own
destination — a SIEM, a log warehouse, or object storage — so they live
alongside the rest of your security telemetry and survive independently of
Allodium's retention. Configure an export once and events flow continuously.

## Retention

Set a retention window that matches your compliance obligations. Because the log
is append-only, retention only governs how long events remain queryable within
Allodium; anything you've exported is yours to keep indefinitely.
