# Server configuration
[![Discord](https://img.shields.io/discord/861623516142501898)](https://discord.gg/zQV6m9Jk6Z)

Your (my) own server configuration, managed by docker-compose, with
comprehensive default configuration.

## Setup
IF you are using [docker compose version <2.20](https://docs.docker.com/compose/multiple-compose-files/include/),
you need to use the following bash command to use this project:
```bash
docker-compose ()
{
    docker-compose $(find -name 'docker-compose.*.yml' -type f -printf '%p\t%d\n'  2>/dev/null | sort -n -k2 | cut -f 1 | awk '{print "-f "$0}') $@
}
```

### Run
```bash
SITE=tom.moulard.org docker-compose up -d
```

Now you have your own server configuration.

To be a little more consistent with the management, you can use a `.env` file
and do:
```bash
cp .env.default .env
```

And edit the `.env` file to use the correct configuration.

The `docker-compose` function gather all docker-compose files in order to have
the whole configuration in one place (see `docker-compose config`).

### Tear down
```bash
docker-compose down
```

### Services list
There **should** be only one service by folder:
For example, le folder `traefik/` contains all the necessary configuration to
run the `traefik` service.

Thus each folder represent an available service.

The directory must follow the following architecture:
```
service/
├── conf
│   └── ...
├── data
│   └── ...
├── docker-compose.servicename.yml
├── logs
│   ├── access.log
│   └── error.log
└── README.md
```

If the service you are adding can use volumes:
 - `data/`, is where to store to service data
 - `conf/`, is where to store to service configuration
 - `logs/`, is where to store to service logs (others than Docker logs)

Feel free to do a Pull Request to add your ideas.

[more ideas](https://github.com/awesome-selfhosted/awesome-selfhosted)

## Configuration
Don't forget to change:

 - db passwords (might not be needed since they are beyond the reverse proxy)
 - VPN secrets (if none provided, they are generated directly).

Configuration files are: `docker-compose.yml`, `nginx.conf`

To set the password:
```bash
echo "USERS=$(htpasswd -nB $USER)" >> .env
```

You can add a new set of credentials by editing the .env file like
```env
USERS=toto:pass,tata:pass, ...
```

The `.env.default` is generated using this command:
```bash
grep '${' **/docker-compose.*.yml | sed "s/.*\${\(.*\)}.*/\1/g" | cut -d":" -f 1 | sort -u | sort | xargs -I % echo "%=" >> .env.default
```

### For local developments
Edit the file `/etc/hosts` to provide the reverse proxy with good URLs.

For example, adding this in your `/etc/hosts` will allow to run and debug the
Traefik service locally:
```bash
127.0.0.1   traefik.moulard.org
```

### Scaling up
```bash
docker-compose scale nginx=2
```

## Tests

### Lint

! Warning: This is enforced for all PRs.

We are using yamllint to lint our yaml files.
You can install it by looking at the [official
documentation](https://yamllint.readthedocs.io/en/stable/quickstart.html#installation).

Once installed, you can run the following command to lint all the yaml files:
```bash
yamllint .
```

### docker-compose config

! Warning: This is enforced for all PRs.

You can run the following command to check that the docker-compose files are
correctly written:
```bash
./test.sh
```

It tests that:

 - all docker-compose files are valid
 - all docker-compose files are parsable
 - all docker-compose files are consistent with the test_config.yml file
 - all environment variables are set inside the `.env.default` file

Once this shell scritp is run, if the tests failes, you can see a bunch of
modified files (e.g., `test_config.yml`) that indicates what is wrong.

Note that the GitHub Action will run this script for you, and provides a
`patch.patch` file that **should** solve most of your issues.

## Docker Image Version Management

**Last Updated**: November 19, 2025

All services use pinned Docker image versions in their compose files to ensure stability and reproducibility. Each service has a default version that can be overridden via `.env` file.

### Version Update Safety Categories

#### SAFE for 'latest' Override (10 services)
These services can safely use the `latest` tag in your `.env` file without risk of breaking changes:

- **sabnzbd**, **overseerr**, **nzbhydra2**, **freshrss**, **heimdall** - LinuxServer rolling releases, stable
- **nginx** - Uses `:stable-alpine` tag (conservative updates)
- **watchtower** - Designed for automatic updates
- **homeassistant** - Uses `:stable` tag for production
- **fivefilters** - Community image, stable rolling
- **cadvisor** - Can use latest after initial testing

**Override in .env:**
```bash
SABNZBD_IMAGE_VERSION=latest
OVERSEERR_IMAGE_VERSION=latest
# ... etc
```

#### CAUTION - Test Before Latest (2 services)
May work with latest but test carefully in staging first:

- **homepage** (pinned to v1.7.0) - Occasional breaking config changes
- **traefik** (within v3.x) - Middleware changes require testing

**Recommendation**: Pin in compose file, override to latest only after testing in staging environment.

#### MUST PIN - Never Use Latest (13 services)
Breaking changes common between versions. Always use pinned versions:

1. **bazarr** - Pinned: version-v1.5.3
2. **sonarr** - Pinned: 4.0.16 (v4 API)
3. **radarr** - Pinned: 6.0.4 (v6 breaking changes)
4. **prometheus** - Pinned: v2.55.0 (query language changes)
5. **alertmanager** - Pinned: v0.29.0 (config format changes)
6. **grafana** - Pinned: 11.3.2 (dashboard/plugin compatibility)
7. **node-exporter** - Pinned: v1.8.2 (metric name changes)
8. **portainer** - Pinned: 2.33.3-ce (migration scripts required)
9. **nextcloud** - Pinned: 30 (requires migration scripts)
10. **mariadb** - Pinned: 11.6 (SQL compatibility)
11. **n8n** - Pinned: 1.71.0 (workflow breaking changes)
12. **jupyter** - Pinned: 2024-11-25 (notebook compatibility)
13. **hugo** - Pinned: 0.139.4-alpine (template syntax)

**Never override these to latest in production!**

### Current Pinned Versions (as of Nov 19, 2025)

| Service | Image | Current Pin | Status |
|---------|-------|-------------|--------|
| bazarr | linuxserver/bazarr | version-v1.5.3 | ✅ Current |
| sonarr | linuxserver/sonarr | 4.0.16 | ✅ Current |
| radarr | lscr.io/linuxserver/radarr | 6.0.4 | ✅ Current |
| prometheus | prom/prometheus | v2.55.0 | ✅ Current |
| alertmanager | prom/alertmanager | v0.29.0 | ✅ Current |
| grafana | grafana/grafana | 11.3.2 | ⚠️ LTS chosen |
| node-exporter | prom/node-exporter | v1.8.2 | ✅ Current |
| cadvisor | gcr.io/cadvisor/cadvisor | v0.50.0 | ✅ Current |
| traefik | traefik | v3.6.2 | ✅ Current |
| portainer | portainer/portainer-ce | 2.33.3-ce | ✅ Current |
| homepage | ghcr.io/gethomepage/homepage | v1.7.0 | ✅ Current |
| homeassistant | ghcr.io/home-assistant/home-assistant | stable | ✅ Tag-based |
| nextcloud | nextcloud | 30 | ✅ Major pin |
| mariadb | mariadb | 11.6 | ✅ Major pin |
| n8n | n8nio/n8n | 1.71.0 | ✅ Current |
| jupyter | quay.io/jupyter/tensorflow-notebook | 2024-11-25 | ✅ Current |
| hugo | klakegg/hugo | 0.139.4-alpine | ✅ Current |
| nginx | nginx | stable-alpine | ✅ Tag-based |

### Update Procedures

#### For SAFE Category Services
```bash
# Override in .env to use latest
echo "SABNZBD_IMAGE_VERSION=latest" >> .env

# Pull new image
docker-compose pull sabnzbd

# Restart service
docker-compose up -d sabnzbd

# Monitor logs
docker-compose logs -f sabnzbd
```

#### For MUST PIN Category Services
```bash
# 1. Check for new version at service's releases page

# 2. Update .env with specific version
echo "SONARR_IMAGE_VERSION=4.0.17" >> .env

# 3. Test in staging first!
docker-compose up -d sonarr

# 4. Verify functionality
docker-compose exec sonarr curl -f http://localhost:8989/api/v3/health

# 5. Monitor for errors
docker-compose logs -f --tail=50 sonarr
```

### Version Research Links

**LinuxServer Images:**
- Fleet: https://fleet.linuxserver.io/
- GitHub Releases: https://github.com/linuxserver/docker-{service}/releases

**Prometheus Stack:**
- Prometheus: https://github.com/prometheus/prometheus/releases
- Alertmanager: https://github.com/prometheus/alertmanager/releases
- Grafana: https://github.com/grafana/grafana/releases

**Infrastructure:**
- Traefik: https://github.com/traefik/traefik/releases
- Portainer: https://github.com/portainer/portainer/releases
- Home Assistant: https://www.home-assistant.io/latest-release-notes/

**Applications:**
- n8n: https://github.com/n8n-io/n8n/releases
- Nextcloud: https://github.com/nextcloud/server/releases
- Homepage: https://github.com/gethomepage/homepage/releases

# Authors
Main author:
 - [Tom](http://tom.moulard.org)

Gitlab helper:
 - [michel_k](mailto:thomas.michelot@epita.fr)

Discord MusicBot/minecraft:
 - [huvell_m](mailto:martin.huvelle@epita.fr),
see PR [#6](https://github.com/tomMoulard/make-my-server/pull/6)

