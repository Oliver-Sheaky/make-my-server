# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- AI assistant integration with Claude AI
  - Added `.claude/` directory with PRP (Prompt-Request-Process) commands and templates
  - Added comprehensive AI assistant documentation in `.github/AI_ASSISTANTS.md`
  - Added GitHub workflow templates for Claude-based code review and fixes
  - Added prompt templates for Docker configuration review and fixes
- New services:
  - **FiveFilters Full-Text RSS** - RSS feed enhancement service
  - **FreshRSS** - Self-hosted RSS feed aggregator
  - **Heimdall** - Application dashboard and launcher
  - **Homepage** - Modern application dashboard
  - **n8n** - Workflow automation platform
  - **NZBHydra2** - Meta search for NZB indexers
  - **Overseerr** - Media request and discovery tool
  - **Radarr** - Movie collection manager
  - **SABnzbd** - Usenet binary newsreader
- Enhanced documentation:
  - Added `CLAUDE.md` with AI assistant usage guidelines
  - Added pull request template with structured sections
  - Added comprehensive prompts for implementation and corrections
- Environment configuration:
  - Updated `.env.default` with new service variables
  - Added `.gitignore` entries for service-specific data

### Changed
- Simplified Docker Compose architecture
  - Removed deprecated Docker Compose v2 bash function workaround
  - Now requires Docker Compose v2.20+ with native `include` support
  - Updated `docker-compose.yml` to use modern include-based composition
- Service updates:
  - Updated Bazarr configuration and documentation
  - Updated Sonarr configuration and documentation
  - Streamlined service compose files for better maintainability
- Documentation improvements:
  - Enhanced README with clearer setup instructions
  - Removed references to deprecated Docker Compose v1 patterns
  - Updated service READMEs with more detailed configuration examples
- CI/CD improvements:
  - Enhanced `dockerpublish.yml` workflow
  - Updated health check workflow template

### Removed
- Deprecated services:
  - **Arachni** - Web application security scanner
  - **Bitwarden** - Password manager (removed in favor of upstream solutions)
  - **Ciao** - HTTP monitoring tool
  - **CodiMD** - Collaborative markdown editor
  - **ELK Stack** - Elasticsearch, Logstash, Kibana
  - **Factorio** - Game server
  - **Framadate** - Scheduling tool
  - **GitLab** - Git repository manager (including runner configuration)
  - **Hits** - Hit counter service
  - **Jackett** - Indexer proxy (replaced by NZBHydra2)
  - **Jellyfin** - Media server
  - **Kavita** - Digital library and comic reader
  - **Mastodon** - Federated social network
  - **Minecraft** - Game server (both vanilla and FTB)
  - **Mumble** - Voice chat server
  - **MusicBot** - Discord music bot
  - **Pastebin** - Text sharing service
  - **PeerTube** - Federated video platform
  - **Remotely** - Remote control tool
  - **RocketChat** - Team communication platform
  - **SearXNG** - Privacy-respecting metasearch engine
  - **ShareLaTeX** - Collaborative LaTeX editor
  - **Streama** - Media streaming server
  - **Theia** - Cloud IDE
  - **Tor Relay** - Tor network relay
  - **Transmission** - BitTorrent client
  - **VPN** - VPN server
  - **WordPress** - Content management system
- Removed Docker Compose v1 compatibility bash function
- Removed `docker-compose.networks.yml` include (now in main compose file)

### Fixed
- Corrected typos and references to deprecated Docker Compose version
- Improved service health checks and startup dependencies
- Enhanced service isolation and network configuration

### Technical Details
This release represents a major refactoring focused on:
1. **Modernization**: Adopting Docker Compose v2.20+ features
2. **Simplification**: Removing unused services to reduce maintenance burden
3. **AI-Enhanced Development**: Integrating AI assistants for code review and improvements
4. **Service Consolidation**: Focusing on core media management and self-hosted productivity tools

---

## Previous Releases

For changes prior to this fork, please refer to the [upstream repository](https://github.com/tomMoulard/make-my-server).

### Notable Upstream Changes
- **v2.0.0** (2024-05-01): Added yamllint, updated CI/CD workflows
- Docker Compose v2 `include` directive support (PR #41)
- Traefik v3.0 update
- Home Assistant support
- Dependabot integration

[Unreleased]: https://github.com/Oliver-Sheaky/make-my-server/compare/9a634e4...HEAD
