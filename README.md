# Chatwoot Helm Charts

[![Artifact HUB](https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/artifact-hub)](https://artifacthub.io/packages/helm/chatwoot/chatwoot)

This repository contains helm charts for [Chatwoot](https://github.com/chatwoot-br/chatwoot).

## Installation

```bash
helm repo add chatwoot https://chatwoot-br.github.io/charts
helm install chatwoot chatwoot/chatwoot
```

## Configuration

Check the [README.md](./charts/chatwoot/README.md)

## Questions? Feedback?

[Join our discord server.](https://discord.gg/cJXdrwS)

## Postgres

```bash
# First build with --load
docker buildx build --platform linux/amd64,linux/arm64 -t ghcr.io/chatwoot-br/postgres:17 -f docker/postgres/Dockerfile --load .

# Then push the image to the registry
docker push ghcr.io/chatwoot-br/postgres:17

# Create multiple tags for the same image
# This allows referencing the image by specific version or general major version
docker buildx imagetools create \
  --tag ghcr.io/chatwoot-br/postgres:17.5.0 \
  --tag ghcr.io/chatwoot-br/postgres:17.5 \
  --tag ghcr.io/chatwoot-br/postgres:17 \
  ghcr.io/chatwoot-br/postgres:17

# Build and push multi-architecture PostgreSQL image with extensions
# - Builds for both AMD64 and ARM64 platforms
# - Includes pgvector extension for vector similarity search
# - Includes pgsql-http for making HTTP requests from PostgreSQL
# - Based on PostgreSQL version 17.5.0
docker buildx build --platform linux/amd64,linux/arm64 \
  --build-arg PGVECTOR_TAG=v0.8.0 \
  --build-arg PGSQL_HTTP_TAG=v1.6.3 \
  --build-arg PG_MAJOR=17.5.0 \
  -t ghcr.io/chatwoot-br/postgres:17 \
  -f docker/postgres/Dockerfile \
  --push .
```
