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
