---

id: getting-started
title: Getting Started
sidebar_position: 2
---

# Getting Started

This guide explains how to deploy **Webmail API** using the published Docker image.

The Webmail API is the core backend service for Webmail. It handles user authentication, mailbox operations, email composition, session management, and communicates with the RMQ Worker to deliver emails.

## Prerequisites

Before deploying Webmail API, ensure the following components are already configured and operational:

* **RabbitMQ** configured for the Webmail RMQ Worker. Complete the RMQ Worker setup first, including the RabbitMQ virtual host, exchange, queues, and user permissions.
* **Webmail BIMI API** deployed and accessible.
* **PostgreSQL** database available.
* **Internal V2 PostgreSQL database** available.

> **Note**
>
> The internal V2 database is currently required by the application but is planned for removal in a future release. If you require assistance configuring this dependency, please contact the development team or join our Discord community.

### Database Schema

The required database schema must be created before starting the API.

Run the SQL script below against your PostgreSQL database:

https://github.com/Yukthi-Systems/WebMail-API/blob/main/docs/init_db_tables.sql

This script creates all required tables used by the Webmail API.

### Memcached

A Memcached instance is required for:

* User session storage
* Temporary application cache
* Frequently accessed application data

### Google reCAPTCHA

Google reCAPTCHA must be configured before starting the application.

The following values are required:

* Google Project ID
* Google API Key
* Google Site Key

### Configuration

All runtime configuration is provided through the Docker Compose file using environment variables.

## Docker Compose

Create the following `docker-compose.yml` file.

```yaml
services:
  webmail-main-api:
    image: rjyspl/webmail-main-api:latest
    container_name: webmail-main-api

    environment:
      - TZ=<timezone>

      # Gunicorn
      - GUNICORN_ARG_WORKERS=<workers>
      - GUNICORN_ARG_THREADS=<threads>
      - GUNICORN_ARG_TIMEOUT=<timeout>
      - GUNICORN_ARG_BIND_PORT=8086

      # PostgreSQL
      - POSTGRES_DB_USERNAME=<username>
      - POSTGRES_DB_PASSWORD=<password>
      - POSTGRES_DB_HOST=<host>
      - POSTGRES_DB_PORT=5432
      - POSTGRES_DB_DATABASE=<database>

      # Alternatively:
      # POSTGRES_DB_URI=postgresql://user:password@host:5432/database

      - POSTGRES_POOL_SIZE=<pool-size>
      - POSTGRES_POOL_MAX_INACTIVE_CONNECTION_LIFETIME=<seconds>

      # Internal V2 Database
      - ADMIN_POSTGRES_DB_USERNAME=<username>
      - ADMIN_POSTGRES_DB_PASSWORD=<password>
      - ADMIN_POSTGRES_DB_HOST=<host>
      - ADMIN_POSTGRES_DB_PORT=<port>
      - ADMIN_POSTGRES_DB_DATABASE=<database>

      # Alternatively:
      # ADMIN_POSTGRES_DB_URI=postgresql://user:password@host:port/database

      - ADMIN_POSTGRES_POOL_SIZE=<pool-size>

      # Memcached
      - MEMCACHED_DB_HOST=<host>
      - MEMCACHED_DB_PORT=11211
      - MEMCACHED_DB_POOL_SIZE=<pool-size>

      # Session expiry (seconds)
      - MAX_AGE_OF_CACHE=28800

      # RabbitMQ
      - RABBITMQ_HOST=<host>
      - RABBITMQ_PORT=5672
      - RABBITMQ_VIRTUAL_HOST=<virtual-host>
      - RABBITMQ_USERNAME=<username>
      - RABBITMQ_PASSWORD=<password>
      - RABBITMQ_EXCHANGE=<exchange>
      - RABBITMQ_ROUTING_KEY=send_emails

      # Google reCAPTCHA
      - GOOGLE_RECAPTCHA_PROJECT_ID=<project-id>
      - GOOGLE_RECAPTCHA_API_KEY=<api-key>
      - GOOGLE_RECAPTCHA_SITE_KEY=<site-key>

      # Logging
      - LOG_LEVEL=<level>
      - LOGS_API_PASSWORD=<password>
      - NUMBER_OF_LOGS_TO_DISPLAY=<count>

      # Application
      - API_KEY=<internal-api-key>
      - API_COOKIE_DOMAIN=.example.com
      - LOCAL_SMTP_HOST_NAME=example.com
      - SMTP_CONNECTION_TIMEOUT=10
      - ALLOWED_ORIGINS=https://api.example.com,https://webmail.example.com

    ports:
      - "127.0.0.1:8086:8086"

    volumes:
      - /var/log/webmail-main-api:/var/log/api

    networks:
      - webmail-network

    restart: always

  postgres-database:
    image: postgres:16
    container_name: postgres-database

    environment:
      - POSTGRES_USER=<username>
      - POSTGRES_PASSWORD=<password>
      - POSTGRES_DB=<database>

    volumes:
      - /data/webmail-pgsql:/var/lib/postgresql/data

    restart: always

    networks:
      - webmail-network

  memcached-database:
    image: memcached:1.6
    container_name: memcached-database

    restart: always

    networks:
      - webmail-network

networks:
  webmail-network:
    driver: bridge
```

Replace all placeholder values with those appropriate for your deployment.

## Start the Application

Start the containers:

```bash
docker compose up -d
```

Verify that all containers are running:

```bash
docker ps
```

View the API logs:

```bash
docker logs -f webmail-main-api
```

If the application starts successfully without any errors, the Webmail API is ready to accept requests.

## Next Steps

At this point, the backend infrastructure is complete.

Proceed with the **Webmail UI** deployment, which will communicate with:

* Webmail API
* Webmail BIMI API
* RabbitMQ (through Webmail API)
* PostgreSQL
* Memcached

Refer to the **Webmail UI Getting Started** guide to complete the deployment.

## Need Help?

If you encounter any issues during deployment, please join our Discord community. We're happy to help with setup, configuration, and troubleshooting.

https://discord.gg/hjsAJF96fY
