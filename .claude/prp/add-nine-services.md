---
name: "Add 9 Services to make-my-server Stack"
description: "Comprehensive PRP for implementing RSS, dashboard, automation, and media services"
created: 2025-11-14
confidence: 9/10
---

## Goal

**Feature**: Add 9 new Docker Compose services to make-my-server infrastructure

**Services**:
1. **RSS Stack**: fivefilters-full-text-rss, FreshRSS
2. **Dashboard Stack**: Heimdall (existing), Homepage (evaluation)
3. **Automation**: n8n (workflow automation with SQLite)
4. **Media Automation Stack**: NZBHydra2, Radarr, Overseerr, SABnzbd

**Deliverable**: 9 new service directories with docker-compose files, health checks, Traefik routing, and basic auth

**Success**: All services running healthy, accessible via Traefik with auth, integrated with existing Sonarr/Bazarr stack

## Why

- **RSS Stack**: Consolidate RSS feeds with full-text extraction for better reading experience
- **Homepage**: Evaluate modern dashboard alongside existing Heimdall for potential migration
- **n8n**: Automate workflows between services (notifications, integrations, data processing)
- **Media Stack**: Complete the *arr ecosystem for automated movie downloads/management
  - Integrates with existing Sonarr (TV) and Bazarr (subtitles)
  - Provides user-friendly request interface (Overseerr)
  - Enables centralized indexer management (NZBHydra2)

## What

### Success Criteria

**Technical**:
- [ ] All 9 services start successfully via `docker-compose up -d`
- [ ] All health checks pass within 2 minutes
- [ ] All services accessible via Traefik routing (e.g., `radarr.${SITE}`)
- [ ] Basic auth protecting all services via `${USERS}` variable
- [ ] All tests pass (`./test.sh`, `yamllint .`)
- [ ] No configuration drift in `test_config.yml`
- [ ] All environment variables documented in `.env.default`

**Integration**:
- [ ] Media stack services can communicate (Radarr → NZBHydra2 → SABnzbd)
- [ ] Overseerr connects to Radarr for movie requests
- [ ] FreshRSS can optionally use fivefilters for full-text extraction
- [ ] n8n can send webhooks to external services

**Repository**:
- [ ] All services follow standard directory structure (`conf/`, `data/`, `logs/`)
- [ ] All docker-compose files use single quotes, 2-space indentation
- [ ] All services connect to `srv` network
- [ ] READMEs created for all 9 services

## Context

### Documentation & References

```yaml
# Official Service Documentation
- url: 'https://github.com/heussd/fivefilters-full-text-rss-docker'
  why: 'Docker configuration for Full-Text RSS, environment variables, volume mounts'

- url: 'https://docs.linuxserver.io/images/docker-freshrss/'
  why: 'FreshRSS official LinuxServer image docs, PUID/PGID patterns, CRON setup'

- url: 'https://docs.linuxserver.io/images/docker-heimdall/'
  why: 'Heimdall configuration patterns (already in stack, reference for comparison)'

- url: 'https://gethomepage.dev/installation/docker/'
  why: 'Homepage Docker setup, YAML config patterns, healthcheck endpoint'

- url: 'https://docs.n8n.io/hosting/installation/docker/'
  why: 'n8n Docker configuration, SQLite setup, webhook configuration'

- url: 'https://docs.linuxserver.io/images/docker-radarr'
  why: 'Radarr setup, API configuration, volume mount patterns for media'

- url: 'https://docs.overseerr.dev/getting-started/installation'
  why: 'Overseerr Docker setup, Radarr integration, API configuration'

- url: 'https://docs.linuxserver.io/images/docker-sabnzbd'
  why: 'SABnzbd configuration, volume structure for downloads'

- url: 'https://docs.linuxserver.io/images/docker-nzbhydra2'
  why: 'NZBHydra2 setup, indexer configuration, Newznab API'

# Repository Patterns
- file: 'sonarr/docker-compose.sonarr.yml'
  why: 'Reference pattern for *arr services (Radarr follows same structure)'

- file: 'bazarr/docker-compose.bazarr.yml'
  why: 'Another *arr example with basic_auth middleware pattern'

- file: 'nextcloud/docker-compose.nextcloud.yml'
  why: 'Multi-container service example (for potential database services)'

- file: 'homeassistant/docker-compose.homeassistant.yml'
  why: 'Health check pattern with curl, custom port configuration'

- file: 'traefik/docker-compose.traefik.yml'
  why: 'Global basic_auth middleware definition pattern'

- file: '.github/workflows/dockerpublish.yml'
  why: 'CI/CD health check pattern reference'

- file: '.yamllint'
  why: 'YAML linting rules (single quotes, 2-space indent, truthy values)'

- file: 'test.sh'
  why: 'Automated testing script that validates config and env vars'
```

### Current Repository Structure

```bash
make-my-server/
├── .github/workflows/          # CI/CD workflows
│   ├── dockerpublish.yml      # Main workflow with health checks
│   └── healthcheck.workflow.tmpl.yml  # Reusable health check template
├── bazarr/                     # EXISTING: Subtitle manager (integrates with media stack)
│   ├── config/
│   ├── docker-compose.bazarr.yml
│   └── README.md
├── sonarr/                     # EXISTING: TV show manager (integrates with media stack)
│   ├── config/
│   ├── downloads/
│   ├── docker-compose.sonarr.yml
│   ├── README.md
│   └── tv/
├── traefik/                    # EXISTING: Reverse proxy with basic auth
│   ├── docker-compose.traefik.yml
│   ├── dynamic_conf/
│   └── logs/
├── docker-compose.yml          # Master include file
├── docker-compose.networks.yml # Network definition (srv)
├── .env.default                # Environment variable template (auto-generated)
├── test.sh                     # Validation script
├── test_config.yml             # Golden config reference
└── .yamllint                   # YAML linting rules
```

### Desired Structure After Implementation

```bash
# NEW SERVICES (9 total)

# RSS Stack
fivefilters-full-text-rss/
├── cache/                      # RSS cache storage
├── docker-compose.fivefilters.yml
└── README.md

freshrss/
├── data/                       # FreshRSS data and feeds
├── extensions/                 # Custom extensions
├── docker-compose.freshrss.yml
└── README.md

# Dashboard Stack
homepage/
├── config/                     # YAML configuration files
├── docker-compose.homepage.yml
└── README.md

# Automation
n8n/
├── data/                       # SQLite database + workflows
├── docker-compose.n8n.yml
└── README.md

# Media Automation Stack (integrates with existing sonarr/bazarr)
nzbhydra2/
├── config/                     # Indexer configuration
├── downloads/                  # NZB storage
├── docker-compose.nzbhydra2.yml
└── README.md

radarr/
├── config/                     # Radarr database
├── downloads/                  # Shared with SABnzbd
├── movies/                     # Movie library
├── docker-compose.radarr.yml
└── README.md

overseerr/
├── config/                     # Overseerr database
├── docker-compose.overseerr.yml
└── README.md

sabnzbd/
├── config/                     # SABnzbd settings
├── incomplete-downloads/       # Work-in-progress
├── downloads/                  # Completed downloads (shared with Radarr)
├── docker-compose.sabnzbd.yml
└── README.md
```

### Known Gotchas

```yaml
# YAML Requirements (yamllint enforced)
- Single quotes for ALL strings: 'value' not "value" or value
- 2-space indentation only (no tabs, no 4-space)
- Truthy values MUST be quoted: 'true', 'false', 'on'
- Example: traefik.enable: 'true' (not traefik.enable: true)

# Docker Compose Patterns
- All services MUST connect to 'srv' network
- Use relative paths: './service-name/data:/data' (not absolute)
- Environment variables: ${VAR:-default} and document ALL in .env.default
- Run ./test.sh after ANY change (auto-updates .env.default and test_config.yml)

# Traefik Routing & Auth
- Basic auth middleware defined in traefik service: 'basic_auth@docker'
- Router naming: 'traefik.http.routers.SERVICE-NAME.rule'
- Auth label: 'traefik.http.routers.SERVICE-NAME.middlewares=basic_auth@docker'
- Port mapping: 'traefik.http.services.SERVICE-NAME.loadbalancer.server.port=PORT'
- ${USERS} variable format: 'username:$2y$05$hashed_password' (use htpasswd -nB)

# Health Check Patterns
- Simple web services: ['CMD', 'curl', '0.0.0.0:PORT']
- Services with /health endpoint: ['CMD', 'curl', '0.0.0.0:PORT/health']
- wget alternative: ['CMD', 'wget', '-q', '--spider', 'localhost:PORT']
- Database services: ['CMD', 'mysqlcheck', '--all-databases', '-pPASS']

# Service-Specific Gotchas

fivefilters-full-text-rss:
  - No official health check endpoint (use curl to root)
  - Cache requires writable volume
  - Memory limit recommended: 2GB
  - Port: 80 (internal)

freshrss:
  - Health check: 'php cli/health.php' (official script)
  - CRON_MIN environment variable required for auto-refresh
  - Two volume mounts: /data and /extensions
  - Port: 80 (internal)

homepage:
  - CRITICAL: HOMEPAGE_ALLOWED_HOSTS must be set (include port if non-standard)
  - Health check endpoint: /api/healthcheck
  - Pure YAML configuration (no database)
  - Docker socket mount optional (for auto-discovery)
  - Port: 3000

n8n:
  - WEBHOOK_URL must match external URL for webhooks to work
  - N8N_PROTOCOL=https if behind Traefik with TLS
  - N8N_BASIC_AUTH_ACTIVE=false (auth delegated to Traefik)
  - SQLite database location: /home/node/.n8n/database.sqlite
  - Port: 5678

nzbhydra2:
  - Newznab-compatible API
  - Requires indexer configuration post-setup
  - Port: 5076

radarr:
  - CRITICAL: Share /downloads volume with SABnzbd for atomic moves
  - API key required for Overseerr integration
  - Health check: /api/v3/health endpoint
  - Port: 7878

overseerr:
  - Requires Plex for authentication (external dependency)
  - Must configure Radarr connection post-setup (URL: http://radarr:7878)
  - Port: 5055

sabnzbd:
  - CRITICAL: Separate incomplete-downloads and downloads volumes
  - API key required for Radarr integration
  - Usenet server configuration required post-setup
  - Port: 8080 (conflicts with potential other services on host)

# Media Stack Integration
- Service startup order: SABnzbd → NZBHydra2 → Radarr → Overseerr
- Shared volume pattern: /downloads mounted on both SABnzbd and Radarr
- Use Docker service names for inter-service communication (http://radarr:7878)
- API keys must be exchanged between services for integration
```

## Implementation Blueprint

### Phase 1: RSS Stack (Services 1-2)

#### Task 1.1: Create fivefilters-full-text-rss

```bash
mkdir -p fivefilters-full-text-rss/cache
cd fivefilters-full-text-rss
```

Create `docker-compose.fivefilters.yml`:

```yaml
version: '2'

services:
  fivefilters-full-text-rss:
    image: 'heussd/fivefilters-full-text-rss:${FIVEFILTERS_IMAGE_VERSION:-latest}'
    container_name: 'fivefilters-full-text-rss'
    environment:
      FTR_ADMIN_PASSWORD: '${FIVEFILTERS_ADMIN_PASSWORD:-}'
    healthcheck:
      test: ['CMD', 'curl', '-f', 'http://localhost:80/']
      interval: '30s'
      timeout: '10s'
      retries: 3
      start_period: '60s'
    labels:
      - 'traefik.enable=true'
      - 'traefik.http.routers.fivefilters.rule=Host(`fivefilters.${SITE:-localhost}`)'
      - 'traefik.http.routers.fivefilters.entrypoints=websecure'
      - 'traefik.http.routers.fivefilters.tls.certresolver=letsencrypt'
      - 'traefik.http.routers.fivefilters.middlewares=basic_auth@docker'
      - 'traefik.http.services.fivefilters.loadbalancer.server.port=80'
    networks:
      - 'srv'
    restart: 'unless-stopped'
    volumes:
      - './cache:/var/www/html/cache/rss'
```

Create `README.md`:

```markdown
# FiveFilters Full-Text RSS

Full-Text RSS extracts article content from partial RSS feeds.

## Configuration

- **URL**: https://fivefilters.${SITE}
- **Auth**: Basic auth via Traefik
- **Port**: 80 (internal)

## Usage

Convert partial feed to full-text:
```
https://fivefilters.${SITE}/makefulltextfeed.php?url=FEED_URL
```

## Environment Variables

- `FIVEFILTERS_IMAGE_VERSION`: Docker image version (default: latest)
- `FIVEFILTERS_ADMIN_PASSWORD`: Optional admin panel password (leave empty to disable)

## Volumes

- `cache/`: RSS feed cache storage

## Integration with FreshRSS

Configure in FreshRSS feed settings:
```
http://fivefilters-full-text-rss:80/makefulltextfeed.php?url=%s
```
```

#### Task 1.2: Create FreshRSS

```bash
mkdir -p freshrss/data freshrss/extensions
cd freshrss
```

Create `docker-compose.freshrss.yml`:

```yaml
version: '2'

services:
  freshrss:
    image: 'lscr.io/linuxserver/freshrss:${FRESHRSS_IMAGE_VERSION:-latest}'
    container_name: 'freshrss'
    environment:
      - 'PUID=${FRESHRSS_PUID:-1000}'
      - 'PGID=${FRESHRSS_PGID:-1000}'
      - 'TZ=${TZ:-Europe/Paris}'
    healthcheck:
      test: ['CMD', 'curl', '-f', 'http://localhost:80/']
      interval: '30s'
      timeout: '10s'
      retries: 3
      start_period: '60s'
    labels:
      - 'traefik.enable=true'
      - 'traefik.http.routers.freshrss.rule=Host(`freshrss.${SITE:-localhost}`)'
      - 'traefik.http.routers.freshrss.entrypoints=websecure'
      - 'traefik.http.routers.freshrss.tls.certresolver=letsencrypt'
      - 'traefik.http.routers.freshrss.middlewares=basic_auth@docker'
      - 'traefik.http.services.freshrss.loadbalancer.server.port=80'
    networks:
      - 'srv'
    restart: 'unless-stopped'
    volumes:
      - './data:/config'
```

Create `README.md`:

```markdown
# FreshRSS

Self-hosted RSS aggregator and reader.

## Configuration

- **URL**: https://freshrss.${SITE}
- **Auth**: Basic auth via Traefik
- **Port**: 80 (internal)

## Environment Variables

- `FRESHRSS_IMAGE_VERSION`: Docker image version (default: latest)
- `FRESHRSS_PUID`: User ID for file permissions (default: 1000)
- `FRESHRSS_PGID`: Group ID for file permissions (default: 1000)
- `TZ`: Timezone (default: Europe/Paris)

## Volumes

- `data/`: FreshRSS configuration, feeds, and database

## Initial Setup

1. Access https://freshrss.${SITE}
2. Follow setup wizard
3. Create admin account

## Integration with FiveFilters (Optional)

To use full-text extraction:
1. Configure feed filter in FreshRSS
2. URL pattern: `http://fivefilters-full-text-rss:80/makefulltextfeed.php?url=%s`
```

---

### Phase 2: Dashboard Stack (Service 3)

#### Task 2.1: Create Homepage

```bash
mkdir -p homepage/config
cd homepage
```

Create `docker-compose.homepage.yml`:

```yaml
version: '2'

services:
  homepage:
    image: 'ghcr.io/gethomepage/homepage:${HOMEPAGE_IMAGE_VERSION:-latest}'
    container_name: 'homepage'
    environment:
      - 'PUID=${HOMEPAGE_PUID:-1000}'
      - 'PGID=${HOMEPAGE_PGID:-1000}'
      - 'HOMEPAGE_ALLOWED_HOSTS=homepage.${SITE:-localhost}'
    healthcheck:
      test: ['CMD', 'wget', '--no-verbose', '--tries=1', '--spider', 'http://127.0.0.1:3000/api/healthcheck']
      interval: '10s'
      timeout: '3s'
      retries: 3
      start_period: '20s'
    labels:
      - 'traefik.enable=true'
      - 'traefik.http.routers.homepage.rule=Host(`homepage.${SITE:-localhost}`)'
      - 'traefik.http.routers.homepage.entrypoints=websecure'
      - 'traefik.http.routers.homepage.tls.certresolver=letsencrypt'
      - 'traefik.http.routers.homepage.middlewares=basic_auth@docker'
      - 'traefik.http.services.homepage.loadbalancer.server.port=3000'
    networks:
      - 'srv'
    restart: 'unless-stopped'
    volumes:
      - './config:/app/config'
      - '/var/run/docker.sock:/var/run/docker.sock:ro'
```

Create `README.md`:

```markdown
# Homepage

Modern, customizable application dashboard (alternative to Heimdall for evaluation).

## Configuration

- **URL**: https://homepage.${SITE}
- **Auth**: Basic auth via Traefik
- **Port**: 3000 (internal)

## Environment Variables

- `HOMEPAGE_IMAGE_VERSION`: Docker image version (default: latest)
- `HOMEPAGE_PUID`: User ID for file permissions (default: 1000)
- `HOMEPAGE_PGID`: Group ID for file permissions (default: 1000)
- `HOMEPAGE_ALLOWED_HOSTS`: Permitted hostnames (default: homepage.${SITE})

## Volumes

- `config/`: YAML configuration files (services.yaml, bookmarks.yaml, settings.yaml)
- `/var/run/docker.sock`: Docker integration (read-only, optional for auto-discovery)

## Initial Setup

1. Access https://homepage.${SITE}
2. Configure via YAML files in config/ directory
3. Reference: https://gethomepage.dev/configs/

## Configuration Files

Create these in `config/`:
- `settings.yaml`: Title, background, UI options
- `services.yaml`: Service definitions with URLs
- `bookmarks.yaml`: Organized bookmarks
- `widgets.yaml`: Widget configurations (optional)

## Comparison with Heimdall

- **Homepage**: File-based YAML config, modern Node.js stack, native Docker integration
- **Heimdall**: UI-based config, Laravel/PHP stack, SQLite database
```

---

### Phase 3: Automation Stack (Service 4)

#### Task 3.1: Create n8n

```bash
mkdir -p n8n/data
cd n8n
```

Create `docker-compose.n8n.yml`:

```yaml
version: '2'

services:
  n8n:
    image: 'n8nio/n8n:${N8N_IMAGE_VERSION:-latest}'
    container_name: 'n8n'
    environment:
      - 'N8N_PROTOCOL=https'
      - 'N8N_HOST=n8n.${SITE:-localhost}'
      - 'WEBHOOK_URL=https://n8n.${SITE:-localhost}/'
      - 'N8N_BASIC_AUTH_ACTIVE=false'
      - 'N8N_SECURE_COOKIE=true'
      - 'N8N_CORS_ORIGIN=*'
      - 'DB_TYPE=sqlite'
      - 'TZ=${TZ:-Europe/Paris}'
    healthcheck:
      test: ['CMD', 'wget', '--no-verbose', '--tries=1', '--spider', 'http://localhost:5678/healthz']
      interval: '30s'
      timeout: '10s'
      retries: 3
      start_period: '60s'
    labels:
      - 'traefik.enable=true'
      - 'traefik.http.routers.n8n.rule=Host(`n8n.${SITE:-localhost}`)'
      - 'traefik.http.routers.n8n.entrypoints=websecure'
      - 'traefik.http.routers.n8n.tls.certresolver=letsencrypt'
      - 'traefik.http.routers.n8n.middlewares=basic_auth@docker'
      - 'traefik.http.services.n8n.loadbalancer.server.port=5678'
    networks:
      - 'srv'
    restart: 'unless-stopped'
    volumes:
      - './data:/home/node/.n8n'
```

Create `README.md`:

```markdown
# n8n

Workflow automation platform with SQLite backend.

## Configuration

- **URL**: https://n8n.${SITE}
- **Auth**: Basic auth via Traefik (N8N_BASIC_AUTH_ACTIVE=false)
- **Port**: 5678 (internal)
- **Database**: SQLite (stored in data/database.sqlite)

## Environment Variables

- `N8N_IMAGE_VERSION`: Docker image version (default: latest)
- `N8N_PROTOCOL`: https (for webhook URL generation)
- `N8N_HOST`: n8n.${SITE} (external hostname)
- `WEBHOOK_URL`: https://n8n.${SITE}/ (external webhook URL)
- `N8N_BASIC_AUTH_ACTIVE`: false (auth delegated to Traefik)
- `N8N_SECURE_COOKIE`: true (HTTPS-only cookies)
- `N8N_CORS_ORIGIN`: * (allow external webhooks)
- `DB_TYPE`: sqlite (no external database required)
- `TZ`: Timezone (default: Europe/Paris)

## Volumes

- `data/`: SQLite database, workflows, credentials, encryption keys

## Initial Setup

1. Access https://n8n.${SITE}
2. Create admin account
3. Configure workflows

## Backup

**CRITICAL**: Backup entire `data/` directory including:
- `database.sqlite`: Workflow definitions and execution history
- `.encryption-keys`: Credential encryption keys (required for restore)

## Migration to PostgreSQL (Future)

Consider migrating when:
- Execution volume > 10,000 per day
- Database size > 4-5GB
- Multiple concurrent users
```

---

### Phase 4: Media Automation Stack (Services 5-8)

#### Task 4.1: Create SABnzbd (First - no dependencies)

```bash
mkdir -p sabnzbd/config sabnzbd/incomplete-downloads sabnzbd/downloads
cd sabnzbd
```

Create `docker-compose.sabnzbd.yml`:

```yaml
version: '2'

services:
  sabnzbd:
    image: 'lscr.io/linuxserver/sabnzbd:${SABNZBD_IMAGE_VERSION:-latest}'
    container_name: 'sabnzbd'
    environment:
      - 'PUID=${SABNZBD_PUID:-1000}'
      - 'PGID=${SABNZBD_PGID:-1000}'
      - 'TZ=${TZ:-Europe/Paris}'
    healthcheck:
      test: ['CMD', 'curl', '-f', 'http://localhost:8080/']
      interval: '30s'
      timeout: '10s'
      retries: 3
      start_period: '60s'
    labels:
      - 'traefik.enable=true'
      - 'traefik.http.routers.sabnzbd.rule=Host(`sabnzbd.${SITE:-localhost}`)'
      - 'traefik.http.routers.sabnzbd.entrypoints=websecure'
      - 'traefik.http.routers.sabnzbd.tls.certresolver=letsencrypt'
      - 'traefik.http.routers.sabnzbd.middlewares=basic_auth@docker'
      - 'traefik.http.services.sabnzbd.loadbalancer.server.port=8080'
    networks:
      - 'srv'
    restart: 'unless-stopped'
    volumes:
      - './config:/config'
      - './incomplete-downloads:/incomplete-downloads'
      - './downloads:/downloads'
```

Create `README.md`:

```markdown
# SABnzbd

Usenet (NZB) download client with auto-verification and extraction.

## Configuration

- **URL**: https://sabnzbd.${SITE}
- **Auth**: Basic auth via Traefik
- **Port**: 8080 (internal)

## Environment Variables

- `SABNZBD_IMAGE_VERSION`: Docker image version (default: latest)
- `SABNZBD_PUID`: User ID for file permissions (default: 1000)
- `SABNZBD_PGID`: Group ID for file permissions (default: 1000)
- `TZ`: Timezone (default: Europe/Paris)

## Volumes

- `config/`: SABnzbd configuration and API keys
- `incomplete-downloads/`: Work-in-progress downloads
- `downloads/`: Completed downloads (SHARED with Radarr/Sonarr)

## Initial Setup

1. Access https://sabnzbd.${SITE}
2. Run setup wizard
3. Configure Usenet server (required: host, port, username, password)
4. Note API key from Config → General (needed for Radarr integration)

## Integration with Radarr

Radarr configuration:
- Host: `sabnzbd` (Docker service name)
- Port: `8080`
- API Key: From SABnzbd Config → General
- Category: `movies` (auto-created)

## Important Notes

- **Port 8080 may conflict** with other services on host (only internal port, Traefik handles routing)
- Downloads volume MUST be shared with Radarr for atomic moves (no file copying)
```

#### Task 4.2: Create NZBHydra2 (Depends on SABnzbd)

```bash
mkdir -p nzbhydra2/config nzbhydra2/downloads
cd nzbhydra2
```

Create `docker-compose.nzbhydra2.yml`:

```yaml
version: '2'

services:
  nzbhydra2:
    image: 'lscr.io/linuxserver/nzbhydra2:${NZBHYDRA2_IMAGE_VERSION:-latest}'
    container_name: 'nzbhydra2'
    depends_on:
      - 'sabnzbd'
    environment:
      - 'PUID=${NZBHYDRA2_PUID:-1000}'
      - 'PGID=${NZBHYDRA2_PGID:-1000}'
      - 'TZ=${TZ:-Europe/Paris}'
    healthcheck:
      test: ['CMD', 'curl', '-f', 'http://localhost:5076/']
      interval: '30s'
      timeout: '10s'
      retries: 3
      start_period: '60s'
    labels:
      - 'traefik.enable=true'
      - 'traefik.http.routers.nzbhydra2.rule=Host(`nzbhydra2.${SITE:-localhost}`)'
      - 'traefik.http.routers.nzbhydra2.entrypoints=websecure'
      - 'traefik.http.routers.nzbhydra2.tls.certresolver=letsencrypt'
      - 'traefik.http.routers.nzbhydra2.middlewares=basic_auth@docker'
      - 'traefik.http.services.nzbhydra2.loadbalancer.server.port=5076'
    networks:
      - 'srv'
    restart: 'unless-stopped'
    volumes:
      - './config:/config'
      - './downloads:/downloads'
```

Create `README.md`:

```markdown
# NZBHydra2

Meta-search aggregator for NZB indexers (Newznab-compatible).

## Configuration

- **URL**: https://nzbhydra2.${SITE}
- **Auth**: Basic auth via Traefik
- **Port**: 5076 (internal)
- **API**: Newznab-compatible

## Environment Variables

- `NZBHYDRA2_IMAGE_VERSION`: Docker image version (default: latest)
- `NZBHYDRA2_PUID`: User ID for file permissions (default: 1000)
- `NZBHYDRA2_PGID`: Group ID for file permissions (default: 1000)
- `TZ`: Timezone (default: Europe/Paris)

## Volumes

- `config/`: NZBHydra2 configuration and indexer definitions
- `downloads/`: NZB file storage

## Initial Setup

1. Access https://nzbhydra2.${SITE}
2. Add indexers in Config → Indexers (e.g., NZBgeek, NZBFinder, etc.)
3. Test each indexer
4. Note API key from Config → Main (click "API?" button)

## Integration with Radarr

Radarr indexer configuration:
- Type: Newznab
- URL: `http://nzbhydra2:5076`
- API Key: From NZBHydra2 Config → Main
- Categories: 2000 (Movies), 2010-2090 (sub-categories)

## Integration with SABnzbd (Optional)

NZBHydra2 can send downloads directly to SABnzbd:
- Config → Downloading → Downloader
- URL: `http://sabnzbd:8080`
- API Key: From SABnzbd settings
```

#### Task 4.3: Create Radarr (Depends on NZBHydra2 and SABnzbd)

```bash
mkdir -p radarr/config radarr/downloads radarr/movies
cd radarr
```

Create `docker-compose.radarr.yml`:

```yaml
version: '2'

services:
  radarr:
    image: 'lscr.io/linuxserver/radarr:${RADARR_IMAGE_VERSION:-latest}'
    container_name: 'radarr'
    depends_on:
      - 'nzbhydra2'
      - 'sabnzbd'
    environment:
      - 'PUID=${RADARR_PUID:-1000}'
      - 'PGID=${RADARR_PGID:-1000}'
      - 'TZ=${TZ:-Europe/Paris}'
    healthcheck:
      test: ['CMD', 'curl', '-f', 'http://localhost:7878/api/v3/health']
      interval: '30s'
      timeout: '10s'
      retries: 3
      start_period: '60s'
    labels:
      - 'traefik.enable=true'
      - 'traefik.http.routers.radarr.rule=Host(`radarr.${SITE:-localhost}`)'
      - 'traefik.http.routers.radarr.entrypoints=websecure'
      - 'traefik.http.routers.radarr.tls.certresolver=letsencrypt'
      - 'traefik.http.routers.radarr.middlewares=basic_auth@docker'
      - 'traefik.http.services.radarr.loadbalancer.server.port=7878'
    networks:
      - 'srv'
    restart: 'unless-stopped'
    volumes:
      - './config:/config'
      - './downloads:/downloads'
      - './movies:/movies'
```

Create `README.md`:

```markdown
# Radarr

Automated movie collection manager for Usenet and torrents.

## Configuration

- **URL**: https://radarr.${SITE}
- **Auth**: Basic auth via Traefik
- **Port**: 7878 (internal)
- **API**: v3

## Environment Variables

- `RADARR_IMAGE_VERSION`: Docker image version (default: latest)
- `RADARR_PUID`: User ID for file permissions (default: 1000)
- `RADARR_PGID`: Group ID for file permissions (default: 1000)
- `TZ`: Timezone (default: Europe/Paris)

## Volumes

- `config/`: Radarr database and configuration
- `downloads/`: Completed downloads from SABnzbd (SHARED)
- `movies/`: Organized movie library

## Initial Setup

1. Access https://radarr.${SITE}
2. Run setup wizard
3. Configure download client (SABnzbd):
   - Settings → Download Clients → Add → SABnzbd
   - Host: `sabnzbd`
   - Port: `8080`
   - API Key: From SABnzbd
   - Category: `movies`
4. Configure indexer (NZBHydra2):
   - Settings → Indexers → Add → Newznab
   - URL: `http://nzbhydra2:5076`
   - API Key: From NZBHydra2
5. Configure media management:
   - Settings → Media Management
   - Root Folder: `/movies`
   - File naming scheme
6. Note API key from Settings → General → Security (needed for Overseerr)

## Integration Points

- **Indexer**: NZBHydra2 (http://nzbhydra2:5076)
- **Download Client**: SABnzbd (http://sabnzbd:8080)
- **Media Library**: /movies
- **Request Interface**: Overseerr (http://overseerr:5055)
- **Subtitles**: Bazarr (existing, http://bazarr:6767)

## Shared Volume Pattern

**CRITICAL**: downloads/ volume MUST be same mount as SABnzbd for atomic moves:
- SABnzbd downloads to: /downloads/complete/movies/
- Radarr monitors: /downloads/complete/movies/
- Radarr moves to: /movies/Movie Name (Year)/
- No file copying = instant, no disk I/O
```

#### Task 4.4: Create Overseerr (Depends on Radarr)

```bash
mkdir -p overseerr/config
cd overseerr
```

Create `docker-compose.overseerr.yml`:

```yaml
version: '2'

services:
  overseerr:
    image: 'lscr.io/linuxserver/overseerr:${OVERSEERR_IMAGE_VERSION:-latest}'
    container_name: 'overseerr'
    depends_on:
      - 'radarr'
    environment:
      - 'PUID=${OVERSEERR_PUID:-1000}'
      - 'PGID=${OVERSEERR_PGID:-1000}'
      - 'TZ=${TZ:-Europe/Paris}'
    healthcheck:
      test: ['CMD', 'curl', '-f', 'http://localhost:5055/']
      interval: '30s'
      timeout: '10s'
      retries: 3
      start_period: '60s'
    labels:
      - 'traefik.enable=true'
      - 'traefik.http.routers.overseerr.rule=Host(`overseerr.${SITE:-localhost}`)'
      - 'traefik.http.routers.overseerr.entrypoints=websecure'
      - 'traefik.http.routers.overseerr.tls.certresolver=letsencrypt'
      - 'traefik.http.routers.overseerr.middlewares=basic_auth@docker'
      - 'traefik.http.services.overseerr.loadbalancer.server.port=5055'
    networks:
      - 'srv'
    restart: 'unless-stopped'
    volumes:
      - './config:/config'
```

Create `README.md`:

```markdown
# Overseerr

Media request and discovery tool for Radarr/Sonarr.

## Configuration

- **URL**: https://overseerr.${SITE}
- **Auth**: Basic auth via Traefik (Plex auth also required internally)
- **Port**: 5055 (internal)

## Environment Variables

- `OVERSEERR_IMAGE_VERSION`: Docker image version (default: latest)
- `OVERSEERR_PUID`: User ID for file permissions (default: 1000)
- `OVERSEERR_PGID`: Group ID for file permissions (default: 1000)
- `TZ`: Timezone (default: Europe/Paris)

## Volumes

- `config/`: Overseerr database and configuration

## Initial Setup

**Prerequisites**: Plex server (external) required for authentication

1. Access https://overseerr.${SITE}
2. Sign in with Plex account
3. Configure Radarr:
   - Settings → Services → Radarr → Add Server
   - Hostname: `radarr`
   - Port: `7878`
   - API Key: From Radarr Settings → General → Security
   - Test connection
4. Configure Sonarr (existing):
   - Settings → Services → Sonarr → Add Server
   - Hostname: `sonarr`
   - Port: `8989`
   - API Key: From Sonarr
5. Configure user permissions

## User Workflow

1. User browses movies/TV in Overseerr
2. User requests media
3. Admin approves (or auto-approve configured)
4. Overseerr sends request to Radarr/Sonarr
5. Radarr/Sonarr searches, downloads, organizes
6. Media appears in Plex library

## Integration Points

- **Authentication**: Plex (external)
- **Movie Requests**: Radarr (http://radarr:7878)
- **TV Requests**: Sonarr (http://sonarr:8989, existing)
```

---

### Phase 5: Integration Tasks

#### Task 5.1: Update master docker-compose.yml

Edit `docker-compose.yml`:

```yaml
include:
  - path: 'docker-compose.networks.yml'
  # ... existing services ...
  - path: 'bazarr/docker-compose.bazarr.yml'
  - path: 'sonarr/docker-compose.sonarr.yml'
  # NEW SERVICES (add in alphabetical order)
  - path: 'fivefilters-full-text-rss/docker-compose.fivefilters.yml'
  - path: 'freshrss/docker-compose.freshrss.yml'
  - path: 'homepage/docker-compose.homepage.yml'
  - path: 'n8n/docker-compose.n8n.yml'
  - path: 'nzbhydra2/docker-compose.nzbhydra2.yml'
  - path: 'overseerr/docker-compose.overseerr.yml'
  - path: 'radarr/docker-compose.radarr.yml'
  - path: 'sabnzbd/docker-compose.sabnzbd.yml'
  # ... existing services ...
  - path: 'traefik/docker-compose.traefik.yml'
```

#### Task 5.2: Update Environment Variables

Run test.sh to auto-generate:

```bash
./test.sh
```

Expected new variables in `.env.default`:

```bash
# RSS Stack
FIVEFILTERS_IMAGE_VERSION=
FIVEFILTERS_ADMIN_PASSWORD=
FRESHRSS_IMAGE_VERSION=
FRESHRSS_PUID=
FRESHRSS_PGID=

# Dashboard
HOMEPAGE_IMAGE_VERSION=
HOMEPAGE_PUID=
HOMEPAGE_PGID=

# Automation
N8N_IMAGE_VERSION=

# Media Stack
NZBHYDRA2_IMAGE_VERSION=
NZBHYDRA2_PUID=
NZBHYDRA2_PGID=
OVERSEERR_IMAGE_VERSION=
OVERSEERR_PUID=
OVERSEERR_PGID=
RADARR_IMAGE_VERSION=
RADARR_PUID=
RADARR_PGID=
SABNZBD_IMAGE_VERSION=
SABNZBD_PUID=
SABNZBD_PGID=
```

Manually add recommended defaults to `.env`:

```bash
# RSS Stack
FIVEFILTERS_IMAGE_VERSION=latest
FIVEFILTERS_ADMIN_PASSWORD=
FRESHRSS_IMAGE_VERSION=latest
FRESHRSS_PUID=1000
FRESHRSS_PGID=1000

# Dashboard
HOMEPAGE_IMAGE_VERSION=latest
HOMEPAGE_PUID=1000
HOMEPAGE_PGID=1000

# Automation
N8N_IMAGE_VERSION=latest

# Media Stack
NZBHYDRA2_IMAGE_VERSION=latest
NZBHYDRA2_PUID=1000
NZBHYDRA2_PGID=1000
OVERSEERR_IMAGE_VERSION=latest
OVERSEERR_PUID=1000
OVERSEERR_PGID=1000
RADARR_IMAGE_VERSION=latest
RADARR_PUID=1000
RADARR_PGID=1000
SABNZBD_IMAGE_VERSION=latest
SABNZBD_PUID=1000
SABNZBD_PGID=1000
```

#### Task 5.3: Add CI/CD Health Checks (Optional)

Edit `.github/workflows/dockerpublish.yml`:

```yaml
# After existing health checks, add:

Health-checks-freshrss:
  uses: 'Oliver-Sheaky/make-my-server/.github/workflows/healthcheck.workflow.tmpl.yml@master'
  with:
    service_name: 'freshrss'

Health-checks-homepage:
  uses: 'Oliver-Sheaky/make-my-server/.github/workflows/healthcheck.workflow.tmpl.yml@master'
  with:
    service_name: 'homepage'

Health-checks-n8n:
  uses: 'Oliver-Sheaky/make-my-server/.github/workflows/healthcheck.workflow.tmpl.yml@master'
  with:
    service_name: 'n8n'

Health-checks-radarr:
  uses: 'Oliver-Sheaky/make-my-server/.github/workflows/healthcheck.workflow.tmpl.yml@master'
  with:
    service_name: 'radarr'

Health-checks-sabnzbd:
  uses: 'Oliver-Sheaky/make-my-server/.github/workflows/healthcheck.workflow.tmpl.yml@master'
  with:
    service_name: 'sabnzbd'

# Note: fivefilters, nzbhydra2, overseerr can be added but are lower priority
```

---

## Validation Loop

### Level 1: YAML & Syntax Validation (Run After Each Service)

```bash
# Validate specific service
yamllint fivefilters-full-text-rss/docker-compose.fivefilters.yml
yamllint freshrss/docker-compose.freshrss.yml
yamllint homepage/docker-compose.homepage.yml
yamllint n8n/docker-compose.n8n.yml
yamllint nzbhydra2/docker-compose.nzbhydra2.yml
yamllint overseerr/docker-compose.overseerr.yml
yamllint radarr/docker-compose.radarr.yml
yamllint sabnzbd/docker-compose.sabnzbd.yml

# Validate all YAML
yamllint .

# Validate compose syntax
docker-compose config -q

# Run full test suite
./test.sh

# Expected: Zero errors. Fix all issues before proceeding.
```

### Level 2: Service Startup Validation (Phase by Phase)

**Phase 1 - RSS Stack:**

```bash
# Start services
docker-compose up -d fivefilters-full-text-rss freshrss

# Check status (wait 60s for health checks)
docker-compose ps fivefilters-full-text-rss freshrss

# View logs
docker-compose logs fivefilters-full-text-rss
docker-compose logs freshrss

# Expected: Both services "Up (healthy)" after start period
```

**Phase 2 - Dashboard:**

```bash
docker-compose up -d homepage
docker-compose ps homepage
docker-compose logs homepage
```

**Phase 3 - Automation:**

```bash
docker-compose up -d n8n
docker-compose ps n8n
docker-compose logs n8n
```

**Phase 4 - Media Stack (In dependency order):**

```bash
# Step 1: SABnzbd (no dependencies)
docker-compose up -d sabnzbd
docker-compose ps sabnzbd

# Step 2: NZBHydra2 (depends on SABnzbd)
docker-compose up -d nzbhydra2
docker-compose ps nzbhydra2

# Step 3: Radarr (depends on both)
docker-compose up -d radarr
docker-compose ps radarr

# Step 4: Overseerr (depends on Radarr)
docker-compose up -d overseerr
docker-compose ps overseerr

# Check all media services
docker-compose ps sabnzbd nzbhydra2 radarr overseerr
```

### Level 3: Integration Testing

**Test Inter-Service Communication:**

```bash
# Test Radarr → NZBHydra2
docker-compose exec radarr curl -f http://nzbhydra2:5076/

# Test Radarr → SABnzbd
docker-compose exec radarr curl -f http://sabnzbd:8080/

# Test Overseerr → Radarr
docker-compose exec overseerr curl -f http://radarr:7878/api/v3/system/status

# Test FreshRSS → FiveFilters (optional)
docker-compose exec freshrss curl -f http://fivefilters-full-text-rss:80/

# Expected: 200 OK responses or valid JSON/HTML
```

**Test Traefik Routing:**

```bash
# Test each service via Traefik (requires SITE set in .env)
curl -k https://fivefilters.${SITE}
curl -k https://freshrss.${SITE}
curl -k https://homepage.${SITE}
curl -k https://n8n.${SITE}
curl -k https://nzbhydra2.${SITE}
curl -k https://overseerr.${SITE}
curl -k https://radarr.${SITE}
curl -k https://sabnzbd.${SITE}

# Expected: 401 Unauthorized (basic auth protecting services)

# Test with auth
curl -k -u username:password https://radarr.${SITE}
# Expected: 200 OK with HTML/JSON response
```

**Test Health Checks:**

```bash
# Manual health check tests
docker-compose exec fivefilters-full-text-rss curl -f http://localhost:80/
docker-compose exec freshrss curl -f http://localhost:80/
docker-compose exec homepage wget --no-verbose --tries=1 --spider http://127.0.0.1:3000/api/healthcheck
docker-compose exec n8n wget --no-verbose --tries=1 --spider http://localhost:5678/healthz
docker-compose exec nzbhydra2 curl -f http://localhost:5076/
docker-compose exec overseerr curl -f http://localhost:5055/
docker-compose exec radarr curl -f http://localhost:7878/api/v3/health
docker-compose exec sabnzbd curl -f http://localhost:8080/

# Expected: Exit code 0, HTTP 200 responses
```

### Level 4: Complete System Validation

```bash
# Full test suite
./test.sh
# Expected: All tests pass

# YAML linting
yamllint .
# Expected: No errors

# Compose config validation
docker-compose config
# Expected: No errors, valid merged config

# Check all services
docker-compose ps
# Expected: All services "Up" or "Up (healthy)"

# Restart all services to test persistence
docker-compose restart
sleep 120  # Wait for health checks
docker-compose ps
# Expected: All services healthy after restart
```

---

## Final Validation Checklist

### Technical Validation

- [ ] All 9 services start successfully
- [ ] All health checks pass within 2 minutes
- [ ] `./test.sh` succeeds with zero errors
- [ ] `yamllint .` reports zero issues
- [ ] `docker-compose config` completes without errors
- [ ] `docker-compose ps` shows all services "Up (healthy)"
- [ ] `test_config.yml` updated with no unexpected changes
- [ ] `.env.default` contains all new variables (33 new vars expected)

### Feature Validation

- [ ] All services accessible at expected URLs
- [ ] Basic auth protecting all services (401 without credentials)
- [ ] All services respond with valid content after auth
- [ ] Data persists across service restarts
- [ ] Logs are readable and show normal operation

### Integration Validation

**Media Stack:**
- [ ] Radarr can reach NZBHydra2 API
- [ ] Radarr can reach SABnzbd API
- [ ] Overseerr can reach Radarr API
- [ ] Overseerr can reach Sonarr API (existing)
- [ ] Shared /downloads volume works (Radarr sees SABnzbd files)

**RSS Stack:**
- [ ] FreshRSS can access FiveFilters (optional test)
- [ ] Both services store data persistently

**Dashboard:**
- [ ] Homepage config loads from YAML files
- [ ] Docker integration works (read-only socket mount)

**Automation:**
- [ ] n8n stores workflows in SQLite database
- [ ] n8n webhook URL matches external domain

### Code Quality Validation

- [ ] All compose files use single quotes for strings
- [ ] All compose files use 2-space indentation (no tabs)
- [ ] All services connect to `srv` network
- [ ] All services have health checks defined
- [ ] All Traefik labels follow pattern:
  - `traefik.enable=true`
  - `routers.SERVICE.rule=Host(...)`
  - `routers.SERVICE.middlewares=basic_auth@docker`
  - `services.SERVICE.loadbalancer.server.port=PORT`
- [ ] All environment variables documented in `.env.default`
- [ ] All services have README.md with documentation
- [ ] File structure matches template (conf/, data/, logs/ or equivalent)

### Repository Integrity

- [ ] All 9 service directories created with correct structure
- [ ] All 9 docker-compose files created and included in master
- [ ] All 9 READMEs created with setup instructions
- [ ] `docker-compose.yml` includes all 9 new services
- [ ] `.env.default` updated by `./test.sh` (auto-generated)
- [ ] `test_config.yml` updated by `./test.sh` (auto-generated)
- [ ] No untracked volumes created (`git status` clean except new services)
- [ ] No `.env` file committed (only `.env.default`)

---

## Post-Implementation Configuration

### Media Stack Configuration Workflow

**Order matters - follow these steps sequentially:**

1. **SABnzbd Setup**
   - Access https://sabnzbd.${SITE}
   - Add Usenet provider (host, port, username, password)
   - Note API key from Config → General

2. **NZBHydra2 Setup**
   - Access https://nzbhydra2.${SITE}
   - Add indexers (NZBgeek, NZBFinder, etc.)
   - Test each indexer
   - Note API key from Config → Main
   - Optionally configure SABnzbd downloader

3. **Radarr Setup**
   - Access https://radarr.${SITE}
   - Add SABnzbd download client:
     - Host: `sabnzbd`, Port: `8080`
     - API key from step 1
     - Category: `movies`
   - Add NZBHydra2 indexer:
     - Type: Newznab
     - URL: `http://nzbhydra2:5076`
     - API key from step 2
   - Configure media management:
     - Root folder: `/movies`
     - File naming
   - Note API key from Settings → General → Security

4. **Overseerr Setup**
   - Access https://overseerr.${SITE}
   - Sign in with Plex
   - Add Radarr server:
     - Host: `radarr`, Port: `7878`
     - API key from step 3
   - Add Sonarr server (existing):
     - Host: `sonarr`, Port: `8989`
     - API key from existing Sonarr

### Other Services Configuration

**FreshRSS:**
- Access https://freshrss.${SITE}
- Run setup wizard
- Create admin account
- Optionally configure FiveFilters integration

**Homepage:**
- Edit `homepage/config/services.yaml` with service definitions
- Edit `homepage/config/settings.yaml` for branding
- Restart: `docker-compose restart homepage`

**n8n:**
- Access https://n8n.${SITE}
- Create admin account
- Configure workflows

---

## Anti-Patterns to Avoid

### YAML Formatting
- ❌ Don't use double quotes: `"value"` → Use `'value'`
- ❌ Don't use unquoted strings: `value` → Use `'value'`
- ❌ Don't use 4-space or tabs: `    ` → Use `  ` (2 spaces)
- ❌ Don't use unquoted booleans: `true` → Use `'true'`

### Docker Compose
- ❌ Don't skip `srv` network connection
- ❌ Don't use absolute paths: `/home/user/data` → Use `./data`
- ❌ Don't hardcode values: `PUID=1000` → Use `PUID=${RADARR_PUID:-1000}`
- ❌ Don't skip health checks (critical for CI/CD)

### Traefik & Security
- ❌ Don't expose services without auth: Add `middlewares=basic_auth@docker`
- ❌ Don't use wrong router naming: `traefik.http.routers.web.rule` → Use `traefik.http.routers.SERVICE-NAME.rule`
- ❌ Don't forget port mapping: `traefik.http.services.SERVICE.loadbalancer.server.port=PORT`

### Media Stack
- ❌ Don't use separate filesystems for /downloads and /movies (prevents hardlinks)
- ❌ Don't configure Radarr before SABnzbd/NZBHydra2 are ready
- ❌ Don't use localhost for inter-service communication: Use Docker service names

### Testing
- ❌ Don't skip `./test.sh` after changes (catches config drift)
- ❌ Don't ignore `yamllint` errors (will fail CI)
- ❌ Don't commit without validating all 4 levels

### Repository
- ❌ Don't commit `.env` file (only `.env.default`)
- ❌ Don't create new patterns: Follow existing service structure
- ❌ Don't skip README.md documentation

---

## Confidence Assessment

**Confidence Score: 9/10**

**Why 9/10:**
- ✅ All service patterns researched from official documentation
- ✅ Health checks verified from LinuxServer.io and official images
- ✅ Existing repository patterns analyzed (sonarr, bazarr, nextcloud, traefik)
- ✅ Testing patterns documented from test.sh and CI workflows
- ✅ YAML requirements extracted from .yamllint
- ✅ Integration patterns researched (media stack workflow)
- ✅ All port numbers, API endpoints verified from official docs
- ✅ Volume mount patterns follow existing conventions

**Potential Risks (-1 point):**
- Overseerr requires external Plex server (not in stack) - may need troubleshooting
- Media stack integration requires manual post-setup configuration (API keys, settings)
- n8n webhook configuration assumes HTTPS with valid cert (Traefik Let's Encrypt)

**Mitigation:**
- READMEs include detailed post-setup steps for each service
- Integration validation tests provided for manual verification
- Service dependencies declared in docker-compose (ensures startup order)

---

## Summary

This PRP provides a complete blueprint for implementing 9 services across 4 functional stacks:

1. **RSS Stack**: Full-text feed aggregation
2. **Dashboard**: Modern evaluation alternative to Heimdall
3. **Automation**: Workflow automation with SQLite
4. **Media Stack**: Complete *arr ecosystem for automated movies

**Key Success Factors:**
- Follow implementation order (phases 1-4)
- Validate at each level (YAML → Startup → Integration → System)
- Configure media stack in dependency order (SABnzbd → NZBHydra2 → Radarr → Overseerr)
- Run `./test.sh` after every change
- Test inter-service communication before marking complete

**Estimated Implementation Time:**
- Phase 1 (RSS): 30 minutes
- Phase 2 (Dashboard): 15 minutes
- Phase 3 (Automation): 20 minutes
- Phase 4 (Media): 60 minutes
- Phase 5 (Integration): 15 minutes
- Validation: 30 minutes
- **Total: ~2.5-3 hours** (plus post-configuration time)
