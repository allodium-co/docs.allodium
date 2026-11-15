---
title: Catalog Admin Group
description: The identity-provider group that administers a workspace's catalog from the moment the data plane is installed.
order: 2
---

# Catalog Admin Group

Access to the catalog is decided by grants, and a new user has none. Someone
has to be able to create the first schemas and grant access to everyone else.
That's the **catalog admin group**: a group in your identity provider that you
name when you create the workspace.

## What its members can do

Members of the catalog admin group administer the workspace's catalog. They
can:

- browse every schema, table and view in it;
- create and drop schemas and tables;
- grant and revoke access to other groups.

Being a catalog admin is not meant as standing access to all data. It exists
so the catalog can be set up and governed. For sensitive data, keep the group
small, and give the people who only analyze data their own groups with
narrower grants.

## Choosing the group

Use a group that already exists in your identity provider, for example
`lakehouse-admins`. The name must:

- start with a letter or digit;
- use only letters, digits, `.`, `_` and `-`;
- be at most 64 characters.

Group names must be unique in your identity provider. Access is granted by
group name, not by position in a hierarchy, so two groups with the same name
in different parts of your directory would receive the same access.

Group membership reaches Allodium when a user signs in, so someone you add to
or remove from the group gets the change the next time they sign in.

## How it is applied

You enter the group when you create the workspace. It is written into the
workspace's Terraform variables as `catalog_admin_group`, and the data plane
module sets it up when it installs the catalog. Your data plane, not
Allodium, holds the catalog and its grants.

To change the group, update `catalog_admin_group` and apply the module again.
The new group becomes a catalog admin. The previous group keeps its access
until you remove it.

## What users see

A user sees a catalog in the workspace when their groups allow them to list
its schemas. A user whose grants cover only one schema can still query it, but
won't see the catalog in the list. Grant the group the right to list the
catalog's schemas if its members should browse to it.
