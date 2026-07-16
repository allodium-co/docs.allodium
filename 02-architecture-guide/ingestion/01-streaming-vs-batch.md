---
title: Streaming vs. Batch
description: When to ingest continuously and when to load in scheduled batches.
order: 1
---

# Streaming vs. Batch

Allodium supports two ingestion styles, and most platforms end up using both.
The right choice depends on how fresh the data needs to be and how the source
produces it.

## Streaming

Streaming ingestion consumes records continuously — from a message queue, a
change-data-capture feed, or an event stream — and commits them to the lakehouse
in small, frequent increments. Use it when freshness matters: dashboards that
must reflect the last few minutes, operational tables, or anomaly detection.

## Batch

Batch ingestion loads data on a schedule, in larger chunks — a nightly export, a
periodic file drop, or a backfill. It is simpler to reason about, cheaper for
high-volume historical loads, and the natural fit for sources that only produce
data periodically.

## Choosing

Favor streaming when the cost of stale data is high and the source already emits
events. Favor batch when the source is file-based or the freshness requirement
is measured in hours rather than seconds. Both paths write to the same open
table formats, so a table can start as a batch load and later gain a streaming
feed without a migration.
