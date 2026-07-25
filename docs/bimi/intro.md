---
id: intro
title: Introduction
sidebar_position: 1
slug: /
---

# Webmail BIMI API

Webmail BIMI API (`webmail-bimi-api`) is a small standalone Express/TypeScript service
with two unrelated jobs that happen to live in the same deployable:

1. **Email-authentication DNS lookups** — given a domain, resolve and parse its BIMI,
   SPF, DKIM, DMARC, and MX records server-side and return them as JSON, so
   [Webmail UI](/docs/ui) doesn't have to do raw DNS resolution from the browser (which
   it can't) or from [Webmail API](/docs/api).
2. **White-label branding assets** — serve the per-company logo/background images and
   config that power the branded `/:slug` login pages, backed by a flat JSON config file
   plus an images directory on disk. This is what the UI's
   [Admin Console → Branding tab](/docs/ui/admin-panel#branding-tab) uploads to.

It has no database — company branding config lives in a JSON file
(`src/config/companies.json` by default), and images live on a plain filesystem path.
There's no per-user data here at all; it's pure lookup/serving infrastructure.

Once running, the service exposes REST endpoints for DNS validation and company branding, which are consumed by Webmail UI.

## What it does

- **DNS record lookups** — `/api/dns/bimi`, `/spf`, `/dkim`, `/dmarc`, `/mx`,
  `/a-record`, and a combined `/validate` that runs all five checks and scores the
  domain — see [DNS Records](./reference/dns-records.md)
- **Branding config & assets** — `/api/dns/company-config`, `/bg-image`, `/logo-image`,
  and an upload endpoint to create/update a company's branding — see
  [Branding](./reference/branding.md)
- **API-key auth** on everything except `/health` and `/`, plus a simple in-memory
  per-IP rate limiter — see [Configuration](./configuration.md)

## Where to go next

- [Getting Started](./getting-started.md) — deploy using the recommended Docker image
- [Alternative Deployment](./alternative-deployment.md) — run the service directly from source
- [Architecture](./architecture.md) — service design, request flow, and DNS resolution
- [Configuration](./configuration.md) — environment variables and runtime configuration
- [DNS Records reference](./reference/dns-records.md) — DNS lookup and validation endpoints
- [Branding reference](./reference/branding.md) — company branding configuration and asset management
