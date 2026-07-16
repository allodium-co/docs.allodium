# Allodium Documentation Content

This repository is the **single source of truth** for the content published at
`allodium.co/docs`. It contains nothing but Markdown and images. There is no
build step, no theme, and no JavaScript toolchain here.

The Allodium marketing site (a Go service in the `controlplane` repo) renders
these files directly. A git-sync sidecar keeps a checkout of this repo on a
shared volume next to the server; the server reads the folder tree, builds the
sidebar navigation from it, and renders each Markdown file through the site's
existing page layout with [goldmark](https://github.com/yuin/goldmark). Because
of that, **you never touch the Go binary to publish docs.** Edit Markdown here,
merge, and the sync sidecar picks it up. Your page shows up once the sidecar has
pulled the new commit — no rebuild, no redeploy of the application.

## Folder and section conventions

The folder tree *is* the site structure. There is no separate config file that
lists pages or defines the menu.

- **A folder is a section.** Its name becomes a nav group.
- **A nested folder is a sub-section.** Nesting can go as deep as you need.
- **Each folder should contain an `index.md`.** That file is the landing page
  for the section — the page shown when a reader clicks the section itself
  rather than one of its pages.
- **Every other `*.md` file is a page** inside that section.
- **Numeric filename prefixes control ordering.** Use a two-digit prefix such
  as `01-`, `02-`, `10-` on both folders and files. The prefix is stripped from
  the URL and the displayed title, so `01-getting-started/02-connect-a-data-plane.md`
  is served at `/docs/getting-started/connect-a-data-plane`. Ordering falls back
  to alphabetical when no prefix is present, but always prefer prefixes so the
  sidebar reads in a deliberate order.

### Example tree

```
docs.allodium/
├── README.md
├── 01-getting-started/
│   ├── index.md                     → /docs/getting-started
│   ├── 01-install-the-agent.md      → /docs/getting-started/install-the-agent
│   └── 02-connect-a-data-plane.md   → /docs/getting-started/connect-a-data-plane
├── 02-architecture-guide/
│   ├── index.md
│   ├── 01-platform-overview.md
│   └── ingestion/                   ← nested folder = sub-section
│       ├── index.md
│       └── 01-streaming-vs-batch.md
└── ...
```

## Frontmatter schema

Every Markdown page — including each `index.md` — begins with a YAML frontmatter
block delimited by `---`. Three fields are used:

| Field         | Required | Purpose                                                                 |
| ------------- | -------- | ----------------------------------------------------------------------- |
| `title`       | yes      | The page heading and its label in the sidebar / breadcrumbs.            |
| `description` | yes      | One-sentence summary used for the `<meta name="description">` tag and search snippets. |
| `order`       | no       | Integer that overrides filename ordering within a folder when present.  |

`order` is a redundant-but-explicit companion to the numeric filename prefix;
when both exist, `order` wins. Keeping the prefix in sync with `order` keeps the
file listing readable on disk.

### Example page

```markdown
---
title: Connect a Data Plane
description: Register a data plane with the control plane and verify the handshake.
order: 2
---

# Connect a Data Plane

Your first paragraph of real content goes here...
```

## Adding a new page

1. Pick the section folder it belongs in (or create a new one — see below).
2. Create a Markdown file with a numeric prefix, e.g. `03-scaling.md`.
3. Add the frontmatter block (`title`, `description`, optional `order`).
4. Write the content in Markdown. Commit and merge.

That's it. No Go code, no template edits, no redeploy. Once the git-sync sidecar
pulls the commit, the page and its sidebar entry appear automatically.

## Adding a new section or sub-section

1. Create a new folder. Prefix it with a two-digit number to place it in the
   nav order (e.g. `05-integrations/`).
2. Add an `index.md` with frontmatter to give the section a landing page.
3. Add pages inside it as above. To create a sub-section, nest another folder
   with its own `index.md`.

## What lives where

- **Content (this repo):** all Markdown, images, and the folder structure that
  defines navigation.
- **Rendering (controlplane repo):** the Go server, goldmark pipeline, layout
  template, sidebar generation, routing, and search indexing.

If something about how a page *renders* looks wrong (styling, routing, search),
that's a controlplane concern. If a page's *content or placement* is wrong, fix
it here.
