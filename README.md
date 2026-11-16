<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/icon-dark.svg">
    <img src="assets/icon.svg" alt="NetBird logo" width="128">
  </picture>
</p>

<h1 align="center">aac-netbird-proxy</h1>

<p align="center">Self-hosted deployment definition for the NetBird reverse proxy, the edge proxy for NetBird routing endpoints.</p>

<p align="center">
  <a href="https://github.com/netbirdio/netbird"><img alt="Upstream" src="https://img.shields.io/badge/upstream-netbirdio%2Fnetbird-blue?logo=github"></a>
  <img alt="Image" src="https://img.shields.io/badge/image-netbirdio%2Freverse--proxy%3A0.74.2-2496ED?logo=docker&logoColor=white">
  <a href="https://github.com/netbirdio/netbird/blob/main/LICENSE"><img alt="License" src="https://img.shields.io/badge/license-BSD--3--Clause%20%2F%20AGPL--3.0-green"></a>
</p>

## About

The NetBird reverse proxy is a standalone edge proxy for the self-hosted routing endpoints
of a NetBird mesh. It terminates TLS itself, obtains its certificates via ACME, and listens
directly on the host's HTTP and HTTPS ports. It connects to a NetBird management server
and is configured entirely through environment variables.

Key features (as provided by upstream and as configured in this definition):

- Standalone edge proxy that serves its own TLS (no reverse proxy in front of it)
- Automatic certificates via ACME, stored in a persistent certificate volume
- Connection to a NetBird management endpoint, configured through an access token
- Configuration entirely through environment variables

## Upstream

- Project: <https://github.com/netbirdio/netbird>
- Documentation: <https://docs.netbird.io>
- Container image: `netbirdio/reverse-proxy:0.74.2`
- License: the NetBird repository is BSD-3-Clause, except for the `management/`, `signal/`,
  `relay/` and `combined/` directories, which are AGPL-3.0 (see upstream LICENSE files)

## Requirements

- Host ports `80` and `443` must be free, because the proxy binds them directly.
  It does not run behind Traefik in this definition.
- The routing endpoints must be configured on the NetBird management server before the
  proxy is started (see the NetBird self-hosted proxy documentation).

## Quick start (Docker Compose)

The Compose file is rendered from `service.yml` by the App-as-Code template engine. For a
manual run, provide the values listed under "Configuration" (for example in a `.env` file)
and start the stack:

```bash
# Provide the values listed under "Configuration" in .env / stack.env
docker compose up -d
```

Secrets are not stored in this repository. Provide them through the deployment pipeline
or create the `.env` file yourself (see below). Do not commit them.

## Configuration

Environment variables (defaults from `service.yml`; `CHANGE_ME` must be set per deployment):

| Variable | Default | Description |
|---|---|---|
| `NB_PROXY_TOKEN` | `CHANGE_ME` (secret) | Access token of the proxy, issued by the NetBird management server |
| `NB_PROXY_MANAGEMENT_ADDRESS` | `https://netbird.example.com` | URL of the NetBird management endpoint |
| `NB_PROXY_DOMAIN` | `example.com` | Public base domain used for ACME certificates |
| `NB_PROXY_ADDRESS` | `:443` | Listen address of the proxy |
| `NB_PROXY_CERTIFICATE_DIRECTORY` | `/certs` | Directory for certificates inside the container |
| `NB_PROXY_ACME_CERTIFICATES` | `true` | Obtain certificates via ACME |
| `NB_PROXY_ALLOW_INSECURE` | `true` | Option passed through unchanged from `service.yml` |
| `NB_PROXY_PRIVATE` | `true` | Option passed through unchanged from `service.yml` |
| `NB_PROXY_LOG_LEVEL` | `info` | Log level |

The management address and base domain are placeholders in this repository. The real values
are delivered by the deployment configuration for the target host.

Ports:

| Port | Protocol | Purpose |
|---|---|---|
| `80` | TCP | HTTP |
| `443` | TCP | HTTPS |

Volumes:

| Mount | Description |
|---|---|
| `proxy_certs` -> `/certs` | ACME / proxy certificates (`NB_PROXY_CERTIFICATE_DIRECTORY`) |

## Deploy with App-as-Code

1. Edit `service.yml`. It is the single source of truth for image, ports, environment,
   volumes, dependencies and per-stage overrides. Do not edit the rendered output.
2. Pushing to `main` (or a configured stage branch) runs the service pipeline, which renders
   the manifests with `aac-template-engine` and triggers the deployment.
3. Releases of this definition are published under <https://github.com/app-as-code/aac-netbird-proxy>.

## Credits and license

NetBird is developed by the NetBird project. This repository only contains the deployment
definition for the NetBird reverse proxy and is not a fork of the application. The logo is
provided by the NetBird project; see [`assets/ICON_SOURCE.md`](assets/ICON_SOURCE.md) for
icon sources and licenses.
