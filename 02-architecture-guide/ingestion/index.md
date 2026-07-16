---
title: Ingestion
description: How data enters Allodium and lands in the lakehouse.
order: 1
---

# Ingestion

Ingestion is the front door of the platform: it is how raw data becomes a
governed, queryable table. This sub-section covers the ingestion paths Allodium
supports and the trade-offs between them.

Everything you ingest lands in open table formats in your own object storage.
There is no proprietary storage layer to lock you in — the same tables are
readable by any engine that speaks the open format, and Allodium simply adds
orchestration, cataloging, and governance on top.

Read [streaming vs. batch](01-streaming-vs-batch.md) to choose the right path
for a given source.
