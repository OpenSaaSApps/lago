# lago

> Click To Deploy Lago — Open Source Metering & Usage-Based Billing

[![Sync](https://github.com/opensaasapps/lago/actions/workflows/sync.yml/badge.svg)](https://github.com/opensaasapps/lago/actions/workflows/sync.yml) [![Docker](https://github.com/opensaasapps/lago/actions/workflows/docker.yml/badge.svg)](https://github.com/opensaasapps/lago/actions/workflows/docker.yml) [![Docker Pulls](https://img.shields.io/docker/pulls/thefractionalpm/lago)](https://hub.docker.com/r/thefractionalpm/lago)

Upstream: [getlago/lago](https://github.com/getlago/lago) · Auto-synced daily

---

## One-Command Deploy

```bash
cp .env.example .env && nano .env
docker compose up -d
```

## Coolify / Dokploy

1. New service → **Docker Compose**
2. Paste `docker-compose.yml`
3. Set env vars in UI
4. Deploy

## Environment Variables

| Variable | Required | Description |
|---|---|---|
| `DATABASE_URL` | ⚪ | |
| `REDIS_URL` | ⚪ | |
| `SECRET_KEY_BASE` | ✅ | |
| `LAGO_RSA_PRIVATE_KEY` | ✅ | |
| `LAGO_ENCRYPTION_PRIMARY_KEY` | ✅ | |
| `LAGO_ENCRYPTION_DETERMINISTIC_KEY` | ✅ | |
| `LAGO_ENCRYPTION_KEY_DERIVATION_SALT` | ✅ | |
| `LAGO_API_URL` | ⚪ | |
| `LAGO_FRONT_URL` | ⚪ | |
| `LAGO_FROM_EMAIL` | ⚪ | |

## Image

```
docker pull getlago/api:latest
docker pull thefractionalpm/lago:latest
```

## Ports

| Port | Service |
|---|---|
| `3000` | Main app |

---

*Part of the [OpenSaaSApps](https://github.com/opensaasapps) Click-To-Deploy collection.*
