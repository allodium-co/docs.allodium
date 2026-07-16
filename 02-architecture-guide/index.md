---
title: Architecture Guide
description: How ingestion, the lakehouse, catalog, and governance layers fit together.
order: 2
---

# Architecture Guide

Allodium is built around a clean split between a **control plane** that
orchestrates and a **data plane** that stores and computes. This section
explains how the major layers — ingestion, the lakehouse, the catalog, and
governance — relate to each other, so you can reason about where your data is,
who can touch it, and how a query flows through the system.

## The layers at a glance

- **Ingestion** brings data in, whether streamed continuously or loaded in
  batches, and lands it in open table formats.
- **The lakehouse** is the storage foundation: columnar files in your own object
  storage, described by open table metadata, queryable by multiple engines.
- **The catalog** is the map — the namespaces, tables, schemas, and versions
  that make the raw storage discoverable and governable.
- **Governance** wraps every layer: identity, access policy, and audit apply
  uniformly whether a user is browsing the catalog or running a query.

## Why the split matters

Because storage and compute live in your data plane, Allodium can add
orchestration, discovery, and governance on top without becoming a place your
data has to pass through. The control plane knows *about* your tables; it does
not hold them. That boundary is the single most important thing to understand
before reading the rest of this guide.

Start with the [platform overview](01-platform-overview.md), then dig into the
[ingestion](ingestion/index.md) sub-section for how data arrives.
