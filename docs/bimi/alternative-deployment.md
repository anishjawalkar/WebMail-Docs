---

id: alternative-deployment
title: Alternative Deployment
sidebar_position: 3
-------------------

# Alternative Deployment

This guide explains how to run **Webmail BIMI API** directly from source without using containers.

For most deployments, the recommended approach is to use the published Docker image as described in **Getting Started**. Running from source is primarily intended for development, debugging, or contributing to the project.
> **Note**
>
> This guide covers running the service directly from source. For most deployments, using the published Docker image described in **Getting Started** is recommended.
Once running, the service exposes REST endpoints for DNS validation and company branding, which are consumed by Webmail UI.

## Prerequisites

Before running the service from source, ensure the following are installed:

* Node.js 20 or later (Node.js 24 is recommended)
* npm
* Outbound DNS access (the service queries public DNS servers directly)

Unlike the Webmail API, no database is required. Company branding configuration is stored in a JSON file, while uploaded branding assets are stored on the local filesystem.

## Clone and Install

```bash
git clone https://github.com/Yukthi-Systems/WebMail-BIMI-API.git
cd WebMail-BIMI-API
npm install
```

## Configure Environment Variables

Copy the example environment file:

```bash
cp .env.example .env
```

At minimum, generate a secure API key instead of using the default value. This key is used by clients such as Webmail UI to authenticate requests to the Webmail BIMI API.
```bash
npm run genkey -- my-app-name
```

The command prints an `API_KEYS=name:key` entry that can be copied into your `.env` file.

For a complete list of configuration options, see the [Configuration](./configuration.md) guide.

## Run the Development Server

Start the application with:

```bash
npm run dev
```

The development server uses `tsx watch` and automatically reloads whenever source files change.

By default, the service listens on port **3001**.

Verify that it is running:

```bash
curl http://localhost:3001/health
```

A successful response indicates that the Webmail BIMI API has started correctly and is ready to accept requests.

## Build for Production

Compile the application:

```bash
npm run build
```

Start the compiled application:

```bash
npm start
```
For production deployments, ensure `NODE_ENV=production` is configured before starting the service.
This builds the project into the `dist/` directory and runs it using Node.js.

## Building a Docker Image

If you want to build your own Docker image instead of using the published image, run:

```bash
docker build -t webmail-bimi-api .
```

The repository includes a multi-stage Dockerfile that compiles the application before creating a lightweight production image.

## Next Steps

After the service is running:

* Configure a reverse proxy such as Nginx or Caddy for HTTPS.
* Configure the required environment variables for your deployment.
* Point Webmail UI to the BIMI API using the appropriate API URL and API key.

See the following guides for more information:

* [Configuration](./configuration.md)
* [Architecture](./architecture.md)
