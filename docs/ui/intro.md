---
id: intro
title: Introduction
sidebar_position: 1
slug: /
---

# Webmail UI

Webmail UI is the web client of the Webmail project — a fast, modern, self-hostable email
client built with React, TypeScript, and Vite.

It talks to the [Webmail API](/docs/api) for mail data, contacts, templates, and Sieve
filter management, and mail delivery/archival is handled asynchronously by the
[RMQ worker](/docs/worker).

## What's in this project

- **Mailbox** — threaded message list, reading pane, search, drag-and-drop organization
- **Composer** — rich text editor (Tiptap) with attachments and reusable templates
- **Contacts** — a full address book: create, edit, bulk import/export (CSV, XLSX, vCard)
- **Settings** — accounts, folders, Sieve filters, vacation responder, and appearance
- **Login** — authentication flow, including per-company branded login pages
- **Admin console** — an internal, API-key-gated tool for provisioning mail domains
  (see [Admin Console](./admin-panel.md) — this is operator tooling, not an end-user
  feature)

## Where to go next

- [Getting Started](./getting-started.md) — run the UI locally
- [Architecture](./architecture.md) — how the codebase is organized
- [Features](./features.md) — a detailed tour of every feature
- [Keyboard Shortcuts](./keyboard-shortcuts.md) — the full shortcut reference
- [Admin Console](./admin-panel.md) — domain provisioning and branding
- [Configuration](./configuration.md) — environment variables
- [Alternative Deployment](./alternate-deployment.md)
- [Contributing](./contributing.md) — lint rules and PR process
