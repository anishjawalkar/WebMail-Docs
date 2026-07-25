---
id: alternate-deployment
title: Alternative Deployment
sidebar_position: 8
---
# Alternative Deployment

This guide explains how to run **Webmail UI** directly from source without using containers.

For most deployments, the recommended approach is to deploy the published container image as described in **Getting Started**. Running from source is primarily intended for development, debugging, or contributing to the project.

## Prerequisites

Before running Webmail UI from source, ensure the following are available:

- Node.js 20 or later (Node.js 24 is recommended)
- npm
- A running instance of the [Webmail API](/docs/api)
- A running instance of the [BIMI API](/docs/bimi) if BIMI functionality is required

## Clone the Repository

```bash
git clone https://github.com/Yukthi-Systems/WebMail-UI.git
cd WebMail-UI
```

## Install Dependencies

```bash
npm install
```

## Configure Environment Variables

Copy the example environment file.

```bash
cp .env.example .env
```

Update the variables as required for your environment.

See the [Configuration](./configuration.md) page for a complete description of every available environment variable.

## Start the Development Server

```bash
npm run dev
```

This starts the Vite development server with hot module replacement (HMR).

## Build for Production

```bash
npm run build
```

The production build performs a complete TypeScript type check before generating the optimized production assets.

The generated files are written to:

```text
dist/
```

## Linting

Before committing any changes, verify the project passes linting.

```bash
npm run lint
```

See the project's Contributing guide for the complete development workflow.