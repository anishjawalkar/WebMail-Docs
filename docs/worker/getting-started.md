---
id: getting-started
title: Getting Started
sidebar_position: 2
---

# Getting Started

This guide explains how to deploy the **Webmail RMQ Worker** using the published Docker image.

The RMQ Worker is responsible for processing email jobs published by **Webmail API**. It consumes messages from RabbitMQ, constructs RFC 822 compliant email messages, delivers them over SMTP, stores sent messages in the user's IMAP **Sent** folder, and saves drafts when required.

## Architecture

```
                    Internet
                        │
                        ▼
                   Webmail UI
                        │
                        ▼
                  Webmail API
                        │
              Publishes email jobs
                        │
                        ▼
                  RabbitMQ Exchange
                        │
               Routes messages to
                        │
                        ▼
                 send_emails Queue
                        │
                        ▼
               Webmail RMQ Worker
                ┌────────┴─────────┐
                │                  │
                ▼                  ▼
          SMTP Server        IMAP Server
                │                  │
                ▼                  ▼
         Deliver message     Save Sent/Draft

                    │
             On processing failure
                    ▼
             dead_mails Queue
```

The RMQ Worker does not expose an HTTP API and does not use a database. Its only responsibility is consuming email jobs from RabbitMQ and processing them.

---

## Prerequisites

Before deploying the worker, RabbitMQ must already be installed and configured.

RabbitMQ Installation Guide:

https://www.rabbitmq.com/docs/download

### 1. Create a Virtual Host

Create a dedicated Virtual Host for Webmail instead of using the default `/` virtual host.

Example:

```
webmail
```

Using a dedicated virtual host isolates Webmail resources from other RabbitMQ applications.

### 2. Create an Exchange

Inside the newly created virtual host, create an exchange.

The exchange name should match the value configured in Webmail API.

Example:

- Exchange Name: `web_mail`

Use the actual exchange name from your Webmail API configuration if it differs.

### 3. Create a User

Create a RabbitMQ user that has permission to:

- Configure
- Read
- Write

on the new virtual host.

This user will be used by both **Webmail API** and the **RMQ Worker**.

### 4. Create Queues

Create the following queues.

#### send_emails

This queue receives outgoing email jobs published by Webmail API.

The RMQ Worker consumes messages from this queue and sends the emails.

#### dead_mails

If an email cannot be processed successfully, the worker moves the message to this queue instead of discarding it.

Administrators can inspect this queue to determine why processing failed.

### 5. Bind the Queue

Bind the queues to the exchange created earlier.

Once complete, RabbitMQ is ready for the RMQ Worker.

If you encounter any issues configuring RabbitMQ, feel free to ask in our Discord community:

https://discord.gg/hjsAJF96fY

---

## Docker Compose

Create a `docker-compose.yml` file.

```yaml
services:
  webmail-worker-rmq:
    image: rjyspl/webmail-worker-rmq:latest
    container_name: webmail-worker-rmq

    environment:
      - TZ=<timezone>

      - LOG_MAX_SIZE_MB=<max-log-size>
      - LOG_MAX_BACKUPS=<max-backups>
      - LOG_MAX_AGE_DAYS=<max-age>
      - LOG_COMPRESS=<true-or-false>
      - LOG_LEVEL=<log-level>
      - LOG_CONSOLE=<true-or-false>

      - RB_HOST=<rabbitmq-host>
      - RB_PORT=<rabbitmq-port>
      - RB_USERNAME=<rabbitmq-user>
      - RB_PASSWD=<rabbitmq-password>
      - RB_VHOST=<virtual-host>

      - RB_EXCHANGE=<exchange-name>

      - RB_MAIN_QUEUE=send_emails
      - RB_DEAD_QUEUE=dead_mails

      - RB_CONSUMER_TAG=<consumer-tag>

      - AMQPR_CON_DELAY=<reconnect-delay-seconds>

    restart: always

    volumes:
      - ./logs:/app/logs
```

Replace each placeholder with the values configured during the RabbitMQ setup.

---

## Start the Worker

Start the container:

```bash
docker compose up -d
```

Verify that the container is running:

```bash
docker ps
```

View the worker logs:

```bash
docker logs -f webmail-worker-rmq
```

The worker is ready once it successfully connects to RabbitMQ and begins consuming messages.

---

## What the Worker Does

The worker performs the following operations:

- Consumes email jobs from RabbitMQ.
- Builds RFC 822 compliant email messages.
- Delivers outgoing mail over SMTP.
- Stores sent emails in the user's IMAP **Sent** folder.
- Saves drafts directly to IMAP.
- Processes attachments and inline images.
- Moves failed messages to the `dead_mails` queue for later inspection.

---

## Where to Go Next

- [Architecture](./architecture.md) — worker design and processing model
- [Configuration](./configuration.md) — environment variables
- [Message Format](./message-format.md) — RabbitMQ message schema published by Webmail API