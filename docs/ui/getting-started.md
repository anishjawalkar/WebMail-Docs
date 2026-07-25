---
id: getting-started
title: Getting Started
sidebar_position: 2
---

# Getting Started

This guide explains how to deploy **Webmail UI** using containers. While this documentation uses **Docker** as the reference container runtime, Webmail UI can be deployed using any OCI-compatible container platform.

## Prerequisites

Before deploying Webmail UI, ensure the following services are already deployed and accessible.

### Required Services

| Service | Purpose |
|---------|---------|
| Webmail API | Primary backend that provides authentication, mailbox operations, contacts, templates, filters, and all application APIs. |
| BIMI API | Provides BIMI logo lookups and branding information displayed by the UI. |

> **Note**
>
> Webmail UI depends on both the Webmail API and BIMI API. These services must be running before the UI is started.

## Deployment Order

Deploy the services in the following order to satisfy runtime dependencies.

```text
1. PostgreSQL
2. RMQ Worker
3. Webmail API
4. BIMI API
5. Webmail UI
```

Once all backend services are running, start Webmail UI and verify connectivity to both the Webmail API and BIMI API.

### Backend Dependency Chain

The overall deployment consists of multiple services.

```text
Webmail UI
    │
    ├── Webmail API
    │       │
    │       ├── PostgreSQL
    │       ├── Memcached
    │       └── RMQ Worker
    │
    └── BIMI API
```

## Container Runtime

Webmail UI can be deployed using any container runtime capable of running OCI images.

This documentation uses **Docker** as the deployment example.

Install Docker by following the official installation guide:

https://docs.docker.com/engine/install/

## Supported Deployment Platforms

Webmail UI is distributed as a standard OCI container image and can be deployed using any compatible container platform, including:

- Docker
- Podman
- Kubernetes
- Docker Swarm
- OpenShift
- Nomad

This documentation uses Docker only as the reference implementation.

## Deploying Webmail UI

The published Docker image can be deployed using the following Docker Compose configuration.

```yaml
services:
  webmail-ui:
    image: rjyspl/webmail-web-ui:latest
    container_name: webmail-ui

    environment:
      TZ: Asia/Kolkata

      # Webmail API
      VITE_API_URL: https://webmail-api.example.com

      # BIMI API
      VITE_DNS_API_URL: https://dns.api.example.com
      VITE_DNS_API_KEY: your_dns_api_key_here

      # Optional
      VITE_APP_VERSION: 1.5.9-beta
      VITE_RECAPTCHA_KEY: your_recaptcha_site_key_here

    ports:
      - "127.0.0.1:3000:3000"

    restart: always
```

Start the container:

```bash
docker compose up -d
```

Verify that the container is running:

```bash
docker ps
```

The UI will now be available at:

```
http://localhost:3000
```

or through the reverse proxy configured for your deployment.

## Verify the Deployment

After starting the container, verify that the deployment is functioning correctly.

1. Confirm the container is running.

```bash
docker ps
```

2. Open the Webmail UI in a browser.

```text
http://localhost:3000
```

or access it through your configured reverse proxy.

3. Verify that:

- The login page loads successfully.
- The UI can communicate with the Webmail API.
- BIMI logos load correctly (if the BIMI API is configured).
- No errors appear in the browser developer console or container logs.

## Next Steps

Once Webmail UI is running:

1. Verify that the UI can reach the Webmail API.
2. Verify connectivity to the BIMI API.
3. Configure your reverse proxy (Nginx, Caddy, Traefik, HAProxy, etc.).
4. Enable HTTPS before exposing the service publicly.

See the following sections for more information:

- Configuration
- Deployment
- Architecture