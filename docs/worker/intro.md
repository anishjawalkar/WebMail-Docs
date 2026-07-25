---
id: intro
title: Introduction
sidebar_position: 1
slug: /
---

# RMQ Worker

The RMQ Worker (`rmq-webmail-composer`) is the service that actually delivers mail. When
[Webmail API](/docs/api)'s `POST /email/send` or `POST /email/draft` endpoints are
called, the API doesn't send anything itself — it publishes a message to RabbitMQ and
returns immediately. This worker consumes that queue, builds a real RFC 822 email, sends
it over SMTP, and stores a copy on the IMAP server (in Sent, or as a draft).

It's a small, single-purpose Go service — no HTTP server, no database of its own. Its
entire job is: consume a message, send or save an email, acknowledge the message.

## What it does

1. Consumes email jobs from a RabbitMQ queue
2. Builds a properly formatted RFC 822 message — headers, text/HTML body, attachments,
   inline attachments — using [enmime](https://github.com/jhillyerd/enmime)
3. For a **send**: delivers it over SMTP, then appends a copy to the sender's IMAP
   "Sent" folder, and cleans up any draft the message was sent from
4. For a **draft save**: appends (or replaces) the draft directly on IMAP — no SMTP
   involved
5. On failure, moves the message to a dead-letter queue with the failure reason attached,
   rather than losing it or retrying forever

## Where to go next

- [Getting Started](./getting-started.md) — run the worker locally using docker
- [Architecture](./architecture.md) — the consumer/worker-pool model, and how SMTP/IMAP
  connections are pooled
- [Message Format](./message-format.md) — the JSON contract the queue message must
  match (this is what [Webmail API](/docs/api) publishes)
- [Configuration](./configuration.md) — environment variables
