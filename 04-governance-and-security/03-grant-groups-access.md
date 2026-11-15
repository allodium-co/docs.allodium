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

Only members of the [catalog admin group](02-catalog-admin-group.md) can grant
access.

## How access fits together

- A **catalog role** is a named set of **grants**. Each grant is a privilege
  on the catalog or on one of its schemas, tables or views, for example
  `TABLE_READ_DATA` on a schema.
- A **group** is a group from your identity provider, added to the workspace
  by its exact name.
- Giving a group a catalog role gives every member of the group that role's
  grants.

A user's access is the sum of the roles held by all of their groups. A user in
no group with a role has no access.

## Give a group access

1. **Add the group.** Open the **Groups** page and choose **New group**.
   Enter the group's name exactly as your identity provider sends it. You can
   add a group before any of its members has signed in.
2. **Create a catalog role.** Open the catalog, go to **Roles**, and create a
   role named after what it allows, for example `analysts_read`.
3. **Grant privileges to the role.** On the role's page, under **Grant
   privilege**, choose a resource type, the privilege, and the schema, table
   or view it applies to. Leave the path blank for a grant on the whole
   catalog. Repeat for each privilege.
4. **Give the role to the group.** On the role's page, under **Groups**,
   choose **Give a group this role** and pick the group. You can also type a
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
