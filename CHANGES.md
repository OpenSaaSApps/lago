# CHANGES — Coolify-Compatible Lago Deployment

## What Changed

Added `docker-compose.yaml` (Coolify-ready) alongside the existing `docker-compose.yml`. Key differences from upstream:

| Change | Upstream | Coolify version |
|--------|----------|----------------|
| `container_name` | Hardcoded on all services | Removed (Coolify assigns its own) |
| Required secrets | Weak defaults (`your-secret-key-base-hex-64`) | Fail-fast with `:?` error messages |
| DB/Redis ports | Exposed to host (5432, 6379) | Internal only (no `ports:`) |
| S3 credentials | Dummy defaults (`azerty123456`) | Empty defaults (no misleading values) |
| Logging | None | JSON driver with size limits on all services |
| Env var docs | None | Full header with required/optional separation |
| Sentry (frontend) | Missing | Added `SENTRY_DSN_FRONT` to frontend env |

---

## Environment Variable Reference

### Required — deployment will fail without these

| Variable | How to generate |
|----------|----------------|
| `SECRET_KEY_BASE` | `openssl rand -hex 64` |
| `LAGO_RSA_PRIVATE_KEY` | `openssl genrsa 2048 \| base64` |
| `LAGO_ENCRYPTION_PRIMARY_KEY` | 32 char alphanumeric string |
| `LAGO_ENCRYPTION_DETERMINISTIC_KEY` | 32 char alphanumeric string |
| `LAGO_ENCRYPTION_KEY_DERIVATION_SALT` | 32 char alphanumeric string |

### Optional — URLs & Ports

| Variable | Default | Description |
|----------|---------|-------------|
| `LAGO_API_URL` | `http://localhost:3000` | Public URL for the API |
| `LAGO_FRONT_URL` | `http://localhost` | Public URL for the frontend |
| `API_PORT` | `3000` | Host port for API |
| `FRONT_PORT` | `80` | Host port for frontend |

### Optional — Database

| Variable | Default | Description |
|----------|---------|-------------|
| `POSTGRES_USER` | `lago` | Database user |
| `POSTGRES_PASSWORD` | `changeme` | Database password |
| `POSTGRES_DB` | `lago` | Database name |
| `POSTGRES_PORT` | `5432` | Internal DB port |
| `POSTGRES_HOST` | `db` | DB hostname (internal) |
| `POSTGRES_SCHEMA` | `public` | DB schema |

### Optional — Redis

| Variable | Default | Description |
|----------|---------|-------------|
| `REDIS_HOST` | `redis` | Redis hostname (internal) |
| `REDIS_PORT` | `6379` | Redis port |
| `REDIS_PASSWORD` | *(empty)* | Redis password |

### Optional — Email / SMTP

| Variable | Default | Description |
|----------|---------|-------------|
| `LAGO_FROM_EMAIL` | *(empty)* | Sender email address |
| `LAGO_SMTP_ADDRESS` | *(empty)* | SMTP server |
| `LAGO_SMTP_PORT` | `587` | SMTP port |
| `LAGO_SMTP_USERNAME` | *(empty)* | SMTP user |
| `LAGO_SMTP_PASSWORD` | *(empty)* | SMTP password |

### Optional — Storage (AWS S3)

| Variable | Default | Description |
|----------|---------|-------------|
| `LAGO_USE_AWS_S3` | `false` | Enable S3 storage |
| `LAGO_AWS_S3_ACCESS_KEY_ID` | *(empty)* | S3 access key |
| `LAGO_AWS_S3_SECRET_ACCESS_KEY` | *(empty)* | S3 secret key |
| `LAGO_AWS_S3_REGION` | `us-east-1` | S3 region |
| `LAGO_AWS_S3_BUCKET` | *(empty)* | S3 bucket name |
| `LAGO_AWS_S3_ENDPOINT` | *(empty)* | S3-compatible endpoint |

### Optional — Storage (Google Cloud)

| Variable | Default | Description |
|----------|---------|-------------|
| `LAGO_USE_GCS` | `false` | Enable GCS storage |
| `LAGO_GCS_PROJECT` | *(empty)* | GCS project |
| `LAGO_GCS_BUCKET` | *(empty)* | GCS bucket |

### Optional — Feature Flags

| Variable | Default | Description |
|----------|---------|-------------|
| `LAGO_DISABLE_SIGNUP` | `false` | Disable user sign-ups |
| `LAGO_DISABLE_PDF_GENERATION` | `false` | Disable PDF generation |
| `LAGO_DISABLE_SEGMENT` | *(empty)* | Disable analytics |
| `LAGO_SIDEKIQ_WEB` | `true` | Enable Sidekiq web UI |
| `LAGO_LICENSE` | *(empty)* | Premium license key |

### Optional — Initial Setup

| Variable | Default | Description |
|----------|---------|-------------|
| `LAGO_CREATE_ORG` | `false` | Auto-create org on first boot |
| `LAGO_ORG_USER_EMAIL` | *(empty)* | Admin email for seeded org |
| `LAGO_ORG_USER_PASSWORD` | *(empty)* | Admin password for seeded org |
| `LAGO_ORG_NAME` | *(empty)* | Organization name |
| `LAGO_ORG_API_KEY` | *(empty)* | API key for seeded org |

### Optional — OAuth & AI

| Variable | Default | Description |
|----------|---------|-------------|
| `GOOGLE_AUTH_CLIENT_ID` | *(empty)* | Google OAuth client ID |
| `GOOGLE_AUTH_CLIENT_SECRET` | *(empty)* | Google OAuth secret |
| `MISTRAL_API_KEY` | *(empty)* | Mistral AI API key |
| `MISTRAL_AGENT_ID` | *(empty)* | Mistral Agent ID |

### Optional — Monitoring

| Variable | Default | Description |
|----------|---------|-------------|
| `SENTRY_DSN` | *(empty)* | Sentry DSN for backend |
| `SENTRY_DSN_FRONT` | *(empty)* | Sentry DSN for frontend |

---

## Services

| Service | Image | Purpose |
|---------|-------|---------|
| `db` | `getlago/postgres-partman:15.0-alpine` | PostgreSQL with partman extension |
| `redis` | `redis:7-alpine` | Cache and Sidekiq job queue |
| `migrate` | `getlago/api:v1.44.0` | One-shot DB migration |
| `api` | `getlago/api:v1.44.0` | REST API and GraphQL |
| `front` | `getlago/front:v1.44.0` | Web dashboard |
| `api-worker` | `getlago/api:v1.44.0` | Sidekiq background worker |
| `api-clock` | `getlago/api:v1.44.0` | Cron job scheduler |
| `pdf` | `getlago/lago-gotenberg:7.8.2` | Invoice PDF generator |

---

## Post-Deployment Setup

1. Set the 5 required env vars in Coolify before deploying
2. Deploy — the `migrate` service runs automatically on first boot
3. Access the frontend at `http://<server>:<FRONT_PORT>`
4. Sign up for an account (or set `LAGO_CREATE_ORG=true` with credentials to auto-create)
5. Configure SMTP settings if you need invoice emails
