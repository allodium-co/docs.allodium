---
title: SQL Access
description: How your catalog grants decide what each user can read and write through SQL, and the limits that apply.
order: 4
---

# SQL Access

Every SQL query in a workspace runs as the user who sent it. The grants on
their groups' catalog roles decide which tables the query can read or
write. This page explains how that works, which grants a query needs, and
the limits that apply.

## How a query is authorized

- **The query runs as the user.** A warehouse runs each query as the user
  named in their access token. A query can't ask to run as someone else.
- **The catalog sees the user too.** When a query needs a table, the
  warehouse asks the catalog for it with the same user's token. The catalog
  checks that user's grants, not the warehouse's.
- **Grants come from groups.** The catalog is Apache Polaris, running in
  your data plane. Each group you add is a Polaris principal role with the
  group's name. A user's access is the sum of the grants on the catalog
  roles their groups hold. See [grant groups access](03-grant-groups-access.md).
- **The warehouses have no access of their own.** They hold their own
  catalog principal, which they need to start, and it has no grants. The
  data plane removes any grant it finds on that principal every time you
  install or upgrade. A query can't fall back on the warehouse's access.

The SQL editor shows a denied query as access denied.

### What grants control today

Grants decide which tables a query can load from the catalog. They don't
yet limit storage: the warehouses read table files from your object store
with a key that covers the whole bucket. So file reads aren't yet scoped per
table.

## Which grants a query needs

| To                                        | The user's groups need                                    |
| ----------------------------------------- | --------------------------------------------------------- |
| Query a table                             | `TABLE_READ_DATA` on the table, or on its schema or the catalog |
| See the catalog and its schemas (`SHOW SCHEMAS`) | `NAMESPACE_LIST` on the catalog                    |
| See a schema's tables (`SHOW TABLES`)     | `NAMESPACE_READ_PROPERTIES` and `TABLE_LIST` on the schema |

Metadata-only grants aren't enough. A user with `TABLE_READ_PROPERTIES` but
not `TABLE_READ_DATA` can open the table in the catalog, but a query on it
is denied.

Listing grants are separate from read grants. A user with only
`TABLE_READ_DATA` can query the table by its full name, such as
`lakehouse.sales.orders`. But `SHOW SCHEMAS`, `SHOW TABLES` and queries on
`information_schema` fail for them.

For a worked example, see
[one table](03-grant-groups-access.md#example-one-table).

## Write time limit

A write must commit before the user's access token expires. This applies to
`INSERT`, `CREATE TABLE AS SELECT` and `MERGE`.

- **Tokens last about five minutes.** A write uses the token it was sent
  with, so it has whatever remains of that token's lifetime: at most about
  five minutes, and often less.
- **A longer write fails when it commits.** The error says the write isn't
  authorized. The write isn't committed, so the table is unchanged.
- **Reads aren't limited this way.** A read checks access with the catalog
  when the query starts.

Interactive SQL is the supported use. Keep each write to what commits in a
few minutes, and split a large load into several smaller `INSERT`
statements. A separate path for long-running writes isn't available yet.

## Spark

Spark isn't yet part of governed multi-user workspaces.

Spark executors read storage with a key that covers the whole bucket. Your
catalog grants don't limit what Spark code can read: anyone who can run
Spark code in a workspace can read the data of every table in the bucket.

Use Spark only in a workspace where everyone who can run Spark code may read
all of its data.
