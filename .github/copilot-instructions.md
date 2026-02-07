# InBrief Status - AI Coding Agent Instructions

## Project Overview

This is an **Upptime-powered status monitoring repository** for InBrief services. It's a template-based, automated uptime monitoring system that runs entirely on GitHub Actions and GitHub Pages. The repository monitors external services (InBrief Web, API, MiniStudio App) and automatically generates a status website.

## Core Architecture

**Key Principle**: This is a **declarative, template-driven system**—do not manually edit generated files.

### Configuration Flow
```
.upptimerc.yml (source of truth)
    ↓ (processed by Upptime template engine)
.github/workflows/*.yml (auto-generated, DO NOT EDIT)
```

### Main Components

1. **Configuration**: [.upptimerc.yml](.upptimerc.yml) - Single source of truth for all settings
2. **Workflows**: [.github/workflows/](.github/workflows/) - Auto-generated GitHub Actions (regenerated weekly)
3. **Template Control**: [.templaterc.json](.templaterc.json) - Defines which files Upptime can overwrite
4. **Assets**: [assets/](.assets/) - Static resources (logos, icons)

## Critical Workflows (Auto-Generated)

All workflows in [.github/workflows/](.github/workflows/) are auto-generated from [.upptimerc.yml](.upptimerc.yml):

- **uptime.yml**: Checks endpoint health every 5 minutes
- **site.yml**: Builds and deploys the status website daily (1 AM UTC)
- **response-time.yml**: Tracks and commits response time metrics
- **graphs.yml**: Generates historical uptime graphs
- **summary.yml**: Updates README with current status
- **update-template.yml**: Refreshes workflow files from Upptime upstream (daily)

## Making Changes

### ✅ DO Edit These Files:
- [.upptimerc.yml](.upptimerc.yml) - Add/modify monitored sites, notifications, status page config
- [README.md](README.md) - Update project description (between `<!--start-->` and `<!--end-->` markers)
- [.templaterc.json](.templaterc.json) - Control which files Upptime can overwrite

### ❌ NEVER Directly Edit:
- [.github/workflows/*.yml](.github/workflows/) - Auto-regenerated from `.upptimerc.yml`
- Generated status website files
- Automated README sections (between `<!--start: xxx-->` and `<!--end: xxx-->`)

### Workflow for Configuration Changes:
1. Edit [.upptimerc.yml](.upptimerc.yml)
2. Commit changes
3. Workflows automatically regenerate via `update-template.yml` (or trigger manually via workflow_dispatch)

## Monitored Services Configuration

Services are defined in [.upptimerc.yml](.upptimerc.yml) under `sites:`:

```yaml
sites:
  - name: InBrief Web
    url: https://inbrief.sh
  - name: InBrief API
    url: https://api.inbrief.sh/api/health  # Must be a health check endpoint
```

Each entry creates:
- Automated uptime checks
- Response time tracking
- Auto-opened GitHub issues on downtime
- Historical graphs and reports

## Secrets & Environment Variables

Required repository secrets:
- `GH_PAT`: Personal access token for GitHub API operations (workflows, issues, pages deployment)
- `UPPTIME_NOTIFICATION_EMAIL`: Email recipient for downtime alerts

Access via workflow: `${{ secrets.SECRET_NAME }}`

## Status Website Deployment

- **Hosting**: GitHub Pages
- **Domain**: Custom domain at `status.inbrief.sh` (configured via `cname` in [.upptimerc.yml](.upptimerc.yml))
- **Build**: Automated daily via [site.yml](.github/workflows/site.yml)
- **Content Source**: Auto-generated from historical data commits

## Data Storage Pattern

Upptime commits operational data directly to the repository:
- `./history/` directory: Time-series uptime/response data (Open Database License)
- README auto-updates: Current status badges and summaries
- Git history serves as the database

## Debugging & Maintenance

### Check Service Status:
- View live status: https://status.inbrief.sh
- Check workflow runs: GitHub Actions tab
- Review auto-generated issues for incidents

### Common Issues:
- **Workflows not updating**: Verify `GH_PAT` has correct permissions (repo, workflow scopes)
- **Site not deploying**: Check [site.yml](.github/workflows/site.yml) logs and GitHub Pages settings
- **Wrong configuration**: Only edit [.upptimerc.yml](.upptimerc.yml), then wait for or trigger `update-template.yml`

### Manual Triggers:
All workflows support `workflow_dispatch` for manual execution via GitHub UI.

## Project-Specific Conventions

1. **Template Immutability**: Files matching `.templaterc.json` patterns are regenerated automatically
2. **Data as Code**: All metrics are stored as git commits (enables git-based time travel)
3. **Issue-Driven Incidents**: Downtime automatically opens/closes GitHub issues (no manual incident management)
4. **No Custom Code**: This is a pure configuration repository; business logic lives in `upptime/uptime-monitor@v1.41.0` action

## External Dependencies

- **Upptime Framework**: `upptime/uptime-monitor@v1.41.0` (GitHub Action)
- **Deployment**: `peaceiris/actions-gh-pages@v4` (GitHub Pages deployment)
- **Monitored Services**: InBrief web app, API, MiniStudio (external, read-only monitoring)

## Quick Reference

- Add new service: Edit `sites:` in [.upptimerc.yml](.upptimerc.yml)
- Change check frequency: Modify `schedule.cron` in [.upptimerc.yml](.upptimerc.yml) (then wait for template regeneration)
- Update branding: Edit `logoUrl`, `name`, `introTitle` in [.upptimerc.yml](.upptimerc.yml) under `status-website:`
- Documentation: https://upptime.js.org/docs
