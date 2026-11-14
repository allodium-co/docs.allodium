---
title: Network and Browser Requirements
description: What your users' browsers and network must allow so the workspace can query your data plane directly.
order: 3
---

# Network and Browser Requirements

The workspace UI is served by Allodium, but your data never passes through
Allodium. When someone runs a query in the SQL editor or opens a table's
**Preview** tab, their browser sends the query straight to your data plane and
receives the results from it. The control plane serves the page and issues a
short-lived access token, and that is all it does.

So each user's browser, not just your cluster, needs to reach the data plane.
This page covers what that takes.

## What the browser connects to

| From the browser to | For |
| --- | --- |
| Your workspace URL | The workspace UI and sign-in |
| `https://wh-<warehouse id>.<data-plane domain>` | Queries on that SQL warehouse, one hostname per warehouse |

The data-plane domain is the one you entered when you created the workspace,
for example `lakehouse.acme.internal`. All traffic to it is HTTPS on port 443,
through the data plane's gateway.

## Network

- **Reachability.** Users must be able to reach `https://*.<data-plane domain>`
  from wherever they use the workspace, for example over your office network or
  VPN. It's normal for the domain to be reachable only from your own network:
  your users can reach it, and Allodium can't.
- **DNS.** `*.<data-plane domain>` must resolve, for your users, to the address
  of the gateway.
- **TLS.** The gateway's certificate must be trusted by your users' browsers.
  If it's issued by an internal CA, that CA has to be installed on their
  devices.
- **Proxies and TLS inspection.** A proxy between the browser and the gateway
  must pass the `Authorization` and `DPoP` request headers through unchanged,
  and must let CORS preflight requests (`OPTIONS`) through without requiring
  authentication.

## Browser

Use a current version of Chrome, Edge, Firefox or Safari. The workspace relies
on standard browser features: Web Crypto, and cross-origin requests (CORS) to
the data-plane domain.

Access tokens for the data plane are tied to the browser tab that requested
them. Each one is valid for about five minutes, is kept in memory only and is
never written to disk, and can't be used without a signing key that the tab
holds and can't export.

### Chrome and Edge: local network access

When the workspace is served from the internet and your data-plane domain
resolves to a private address, as it usually does, Chrome treats each query as
a public website contacting a device on the user's local network. It blocks
the request until the user allows it. Microsoft Edge is built on Chromium and
applies the same restriction.

- **For users:** the first time you run a query, the browser asks whether your
  workspace may access devices on your local network. Choose **Allow**. The
  choice is remembered for that site.
- **For administrators:** to pre-approve this, add your workspace URL to the
  `LocalNetworkAccessAllowedForUrls` browser policy. List the workspace URL
  (the site making the request), not the data-plane domain:

  ```json
  {
    "LocalNetworkAccessAllowedForUrls": ["https://<your workspace host>"]
  }
  ```

  Deploy it the way you manage other browser policies, for example through
  your MDM or Group Policy. Check that it's applied at `chrome://policy`.

If the permission is denied or the prompt is dismissed, the browser doesn't
send the request at all, and the SQL editor reports that it can't reach the
data plane.

## Troubleshooting

The SQL editor and the Preview tab say what went wrong:

- **"Can't reach the data plane at wh-…"**: the browser couldn't connect. Check,
  in this order: that you're on a network or VPN that reaches the data-plane
  domain, that the browser trusts the gateway's certificate (open
  `https://wh-<warehouse id>.<data-plane domain>/` directly and look for a
  certificate warning), and that you allowed local network access if the
  browser asked.
- **"The warehouse … isn't ready" or "isn't available"**: the warehouse is
  starting or stopped. Start it, or wait for it, under **SQL Warehouses**.
- **"didn't accept your sign-in"**: your session ended. Reload the page and
  sign in again.
- **"This workspace has no data-plane domain"**: the workspace was created
  without one, so queries can't run from the browser.
