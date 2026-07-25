---
id: intro
title: Introduction
sidebar_position: 1
slug: /
---

# Webmail API

Webmail API is the backend service that [Webmail UI](/docs/ui) talks to. It's a
[FastAPI](https://fastapi.tiangolo.com/) application that sits in front of a real IMAP /
SMTP / [ManageSieve](https://www.rfc-editor.org/rfc/rfc5804) mail server and exposes it
as a JSON REST API, plus a small amount of its own state (contacts, templates, per-user
settings) in PostgreSQL.

Outgoing mail isn't sent directly by this API — sending and drafting are handed off to a
RabbitMQ queue, consumed by the [RMQ worker](/docs/worker).

## What it does

- **Authenticates** against a real IMAP + SMTP account and keeps the session in
  Memcached — see [Authentication](./reference/auth.md)
- **Reads and manages mail** — list, fetch, search, move, copy, flag, and delete
  messages, entirely by talking IMAP to the mail server per request — see
  [Email](./reference/email.md)
- **Manages folders and IMAP ACLs** (shared-mailbox permissions) and quota — see
  [Folders & ACL](./reference/folders.md)
- **Manages a contact address book** stored in Postgres — see
  [Contacts](./reference/contacts.md)
- **Manages Sieve filters** — server-side mail filtering rules, via the ManageSieve
  protocol — see [Sieve Filters](./reference/sieve.md)
- **Provisions mail domains**, for operators running Webmail as a multi-tenant platform
  — see [Admin](./reference/admin.md) (this mirrors the UI's
  [Admin Console](/docs/ui/admin-panel))

## Where to go next

- [Getting Started](./getting-started.md) — run the API locally using docker.
- [Architecture](./architecture.md) — how the layers fit together, and the "why" behind
  a few unusual design choices (no ORM, per-request IMAP auth, two Postgres databases)
- [Configuration](./configuration.md) — every environment variable
- Interactive API reference: once running, Swagger UI is available at `/docs` and
  ReDoc at `/redoc` on your running instance
