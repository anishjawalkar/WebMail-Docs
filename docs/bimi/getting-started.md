---
id: getting-started
title: Getting Started
sidebar_position: 2
---

# Getting Started

This guide explains how to deploy **Webmail BIMI API** using the published Docker image. This is the recommended deployment method for both development and production.

> Recomended that to do this in first step. (1)

## Prerequisites

- Docker
- Docker Compose
- A directory on the host to store branding images and configuration
- A host with at least **2 vCPUs** and **2 GB RAM**
- A directory on the host to store branding images and configuration

The BIMI API does not require a database. Company branding configuration is stored in a JSON file, and uploaded branding assets are stored on the local filesystem.

## Docker Compose

Create a `docker-compose.yml` file:

```yaml
services:
  dns-lookup-api:
    image: rjyspl/webmail-bimi-api:latest
    container_name: dns-lookup-api
    environment:
      - PORT=3001
      - NODE_ENV=development
      - CORS_ORIGIN=http://localhost:3000,http://localhost:5173
      - API_KEYS=default:changeme
      - DOMAIN_IMAGES_PATH=/data/images
      - COMPANIES_CONFIG_PATH=/data/config/companies.json
    ports:
      - "127.0.0.1:3001:3001"
    restart: always
    volumes:
      - ./data:/data
```

Start the service:

```bash
docker compose up -d
```

## Verify the deployment

Confirm the service is running:

```bash
curl http://localhost:3001/health
```

A successful response indicates that the API is running correctly.

## Next steps

Before using the service in production:

- Replace the default API key with a secure value.
- Configure `CORS_ORIGIN` for your Webmail UI deployment.
- Configure `DOMAIN_IMAGES_PATH` and `COMPANIES_CONFIG_PATH` if branding assets are stored in a different location.

For configuration details, see the [Configuration](./configuration.md) guide.
