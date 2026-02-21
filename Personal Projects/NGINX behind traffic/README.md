# NGINX behind Traefik

A simple Docker Compose example showing Traefik as an edge reverse proxy
terminating TLS and routing to an internal NGINX service.

## Overview

- Traefik handles HTTPS (TLS termination) and exposes only ports 80/443.
- NGINX serves a static site on an internal Docker network (no host ports).
- Designed for local, production-style testing with self-signed TLS.

## Architecture

Internet → Traefik (TLS + routing) → Internal Docker Network → NGINX

Key principles:
- Traefik is the only public-facing service.
- Services are discovered via Docker provider and labels.
- Containers run with least privilege and service isolation.

## Project Layout

```
NGINX behind traffic/
├── docker-compose.yml
├── traefik/
│   ├── traefik.yml
   └── dynamic/
       └── tls.yml
├── site/
│   └── index.html
└── README.md
```

## Quick Start

1. Generate a self-signed certificate (example):

```bash
mkdir -p certs
openssl req -x509 -newkey rsa:4096 -nodes -days 365 \
  -keyout certs/key.pem -out certs/cert.pem -subj "/CN=localhost"
```

2. Launch the stack:

```bash
docker compose up -d
```

3. Visit https://localhost (browser will warn for the self-signed cert).

## Security & Routing

- Traefik uses the Docker provider with `exposedByDefault: false` so only
  labeled containers are routable.
- Example router label used for the NGINX service:

```yaml
traefik.http.routers.nginx.rule: "Host(`localhost`)"
```

- Recommended container hardening in `docker-compose.yml`:

```yaml
read_only: true
security_opt:
  - no-new-privileges:true
```

## TLS

- TLS is handled by Traefik using the self-signed certificate placed under
  `certs/` (or configured in `traefik/dynamic/tls.yml`). For local testing,
  generating a certificate for `localhost` is sufficient.

## Scaling

To scale the NGINX service locally:

```bash
docker compose up -d --scale nginx=3
```

Traefik will load balance across the replicas automatically.

## Notes & Recommendations

- This setup is for local/testing and learning—replace self-signed certs with
  proper CA-signed certificates for production (e.g., Let's Encrypt).
- Keep `exposedByDefault: false` to avoid accidentally exposing services.

## Author

Temiloluwa
