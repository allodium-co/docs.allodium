---
title: Grant Groups Access
description: Give the groups in your identity provider access to the catalog, with catalog roles and grants.
order: 3
---

# Grant Groups Access

Access to the catalog is granted to **groups**, never to individual users.
Users, service accounts and group membership all stay in your identity
provider. Allodium doesn't keep a second user directory. In the workspace you
decide what each group may do.

Members of the [catalog admin group](02-catalog-admin-group.md) manage this
access, and only they can add groups.

## How access fits together

- A **catalog role** is a named set of **grants**. Each grant is a privilege
  on the catalog or on one of its schemas, tables or views, for example
  `TABLE_READ_DATA` on a schema.
- A **group** is a group from your identity provider, added to the workspace
  by its exact name.
- Giving a group a catalog role gives every member of the group that role's
  grants.

A user's access to the catalog is the sum of the roles held by all of their
groups. A user in no group with a role has no access to the catalog.

## Give a group access

1. **Add the group.** Open the **Groups** page and choose **New group**.
   Enter the group's name exactly as your identity provider sends it. You can
   add a group before any of its members has signed in.
2. **Create a catalog role.** Open the catalog, go to **Roles**, choose
   **New role**, and name it after what it allows, for example
   `analysts_read`.
3. **Grant privileges to the role.** On the role's page, choose **Add
   grant**, then a resource type, the privilege, and the schema, table or
   view it applies to. Leave the path blank for a grant on the whole
   catalog. Repeat for each privilege.
4. **Give the role to the group.** On the role's page, under **Groups**,
   choose **Assign group** and pick the group. You can also type a
   new group's name there, and it's added as in step 1.

The **Groups** page lists each group with the catalog roles it holds. A
table's **Access** tab lists the grants that reach that table and the groups
that hold them.

### Example: read-only analysts

For a group `analysts` that should browse and read every table in the
catalog, create a role `analysts_read` with these grants on the catalog:

- `NAMESPACE_LIST`, so its members can see the catalog and its schemas;
- `TABLE_LIST` and `VIEW_LIST`, so they can see the tables and views;
- `TABLE_READ_PROPERTIES` and `VIEW_READ_PROPERTIES`, so they can open them;
- `TABLE_READ_DATA`, so they can read the data.

Then give `analysts_read` to `analysts`.

A user sees a catalog only if one of their groups may list its schemas. A
group whose grants cover a single schema can use it, but its members won't
find the catalog in the list without `NAMESPACE_LIST`.

### Example: one table

For a group `finance` that should read only the `orders` table in the `sales`
schema, create a role `orders_read` with these grants:

| Privilege                   | On                    | What it allows                                     |
| --------------------------- | --------------------- | -------------------------------------------------- |
| `TABLE_READ_DATA`           | the table `orders`    | Querying the table.                                |
| `NAMESPACE_LIST`            | the catalog           | Seeing the catalog and its schemas (`SHOW SCHEMAS`). |
| `NAMESPACE_READ_PROPERTIES` | the schema `sales`    | Opening the schema (needed for `SHOW TABLES`).     |
| `TABLE_LIST`                | the schema `sales`    | Seeing the schema's tables (`SHOW TABLES`).        |

Then give `orders_read` to `finance`.

Its members can now browse to `sales.orders` and query it. In the SQL
editor, the catalog is `lakehouse`:

```sql
SHOW SCHEMAS FROM lakehouse;
SHOW TABLES FROM lakehouse.sales;
SELECT * FROM lakehouse.sales.orders LIMIT 10;
```

`TABLE_LIST` shows the names of every table in `sales`, including tables the
group can't read. A query on any of those other tables is denied.

`TABLE_READ_DATA` is the only grant needed to query the table. The other
three are for finding it. Without them, members can still query
`lakehouse.sales.orders` by its full name, but `SHOW SCHEMAS`, `SHOW TABLES`
and queries on `information_schema` fail, and the catalog isn't in their
list.

A listing fails as a whole. If the catalog refuses part of it, the result
isn't trimmed to what the user may see; the statement fails. So a query on
`information_schema` that reaches a schema the group can't list fails too.
To let a group browse every schema, give it the listing grants on the
catalog, as in the analysts example.

### Metadata-only access

`TABLE_READ_PROPERTIES` without `TABLE_READ_DATA` lets members open a table
and see its columns, but not query it. A SQL query on the table is denied.
To let a group query a table, grant `TABLE_READ_DATA`.

See [SQL access](04-sql-access.md) for how these grants apply to each query.

## Group names

Group names follow the same rules as the catalog admin group: up to 64
letters, digits, `.`, `_` and `-`, starting with a letter or digit. They are
matched exactly and are case-sensitive.

Access is granted by group name, not by position in your directory. Two groups
with the same name in different parts of your directory get the same access,
so keep the group names you use here unique.

A few names are reserved by the data plane and can't be added as groups:
`service_admin`, `ALL`, `trino-catalog-reader` and `spark-catalog-writer`.

## When changes take effect

- **A new group** can take up to five minutes to reach its members.
- **Grants and roles** that you add or remove apply to the members' next
  requests.
- **Changes to group membership** are made in your identity provider, and
  reach Allodium the next time the user signs in.

## Remove access

To take access away from a group, remove it from each role on that role's
page. To withdraw a privilege from everyone who holds it through a role,
revoke the grant on the role's page.

A group with no roles has no access, but it stays on the **Groups** page.
