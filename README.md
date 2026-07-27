# Custom Discourse - NodeByte Community

A pre-built, customized Discourse Docker image with China-optimized mirrors, Cloudflare integration, and curated plugins. Deploy in minutes with Docker Compose.

## Features

- **Pre-built image**: No need to compile Discourse from source locally - just pull and run
- **China-optimized**: Built with Chinese mirror sources for Ruby gems, npm packages, and GitHub proxy
- **Cloudflare-ready**: Built-in nginx configuration for Cloudflare real-ip header support
- **Curated plugins**: Includes 20+ community-selected Discourse plugins pre-installed
- **No sensitive data in image**: All credentials are injected at runtime via environment variables - the image is safe to share publicly

## Quick Start (Docker Compose)

The fastest way to deploy:

1. **Create a `.env` file** in the project root:

```bash
DISCOURSE_HOSTNAME=your-domain.com
DISCOURSE_DEVELOPER_EMAILS=admin@your-domain.com
DISCOURSE_SMTP_ADDRESS=smtp.your-provider.com
DISCOURSE_SMTP_PORT=587
DISCOURSE_SMTP_USER_NAME=smtp-user@your-domain.com
DISCOURSE_SMTP_PASSWORD=your-smtp-password
DISCOURSE_SMTP_ENABLE_START_TLS=true
DISCOURSE_SMTP_OPENSSL_VERIFY_MODE=none
UNICORN_WORKERS=4
DISCOURSE_PORT=80
```

2. **Pull and start**:

```bash
docker compose up -d
```

3. **Access Discourse** at `http://localhost:80` (or your configured port)

## Manual Build (Launcher)

If you prefer to build the image yourself from source:

1. **Edit the configuration**:

```bash
cp containers/app.yml containers/app.yml.local
# Edit containers/app.yml.local with your actual values
```

2. **Build the image**:

```bash
# Download the launcher binary
curl -s -L https://get.discourse.org/launcher/latest/launcher-linux-amd64.tar.gz | tar -zxf - -C bin/

# Bootstrap the container
./launcher bootstrap app

# Start the container
./launcher start app
```

## Configuration Reference

### Required Environment Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `DISCOURSE_HOSTNAME` | Your domain name | `forum.example.com` |
| `DISCOURSE_DEVELOPER_EMAILS` | Admin emails (comma-separated) | `admin@example.com` |
| `DISCOURSE_SMTP_ADDRESS` | SMTP server hostname | `smtp.mailgun.org` |
| `DISCOURSE_SMTP_USER_NAME` | SMTP username | `postmaster@mg.example.com` |
| `DISCOURSE_SMTP_PASSWORD` | SMTP password | `your-password` |

### Optional Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `DISCOURSE_SMTP_PORT` | `587` | SMTP port |
| `DISCOURSE_SMTP_ENABLE_START_TLS` | `true` | Enable STARTTLS |
| `DISCOURSE_SMTP_OPENSSL_VERIFY_MODE` | `none` | SSL verification mode |
| `UNICORN_WORKERS` | `4` | Number of web workers |
| `DISCOURSE_PORT` | `80` | Host port mapping |

## Included Plugins

The following plugins are pre-installed in the image:

- discourse/docker_manager - Web-based upgrade manager
- discourse/discourse-saved-searches - Saved search functionality
- discourse/discourse-category-experts - Category expert system
- discourse/discourse-bbcode - BBCode formatting support
- discourse/discourse-yearly-review - Annual review reports
- discourse/discourse-doc-categories - Documentation categories
- discourse/discourse-signatures - User signatures
- discourse/discourse-bilibili-onebox - Bilibili video embedding
- paviliondev/discourse-journal - Journal feature
- angusmcleod/discourse-events - Event management
- discourse/discourse-bcc - Bulk email with BCC
- paviliondev/discourse-tickets - Ticket system
- discourse/discourse-surveys - Survey functionality
- discourse/discourse-user-card-badges - Badge display on user cards
- discourse/discourse-video - Video embedding
- discourse/discourse-tag-by-group - Group-based tagging
- discourse/discourse-tag-topic-user-device - Device-specific tags
- discourse/discourse-solved-reminders-plugin - Solved topic reminders
- discourse/discourse-shared-edits - Collaborative editing
- discourse/discourse-newsletter-integration - Newsletter integration
- discourse/discourse-logster-transporter - Log transport
- discourse/discourse-group-category-banner-ads - Group banner ads
- discourse/discourse-deprecation-collector - Deprecation tracking
- discourse/discourse-docs - Documentation system
- discourse/discourse-code-review - Code review integration
- discourse/discourse-chart - Chart rendering
- discourse/discourse-browser-history - Browser history management
- discourse/discourse-bbcode-color - BBCode color support
- spirobel/discourse-matheditor - Math equation editor

## Cloudflare Setup

If using Cloudflare as your reverse proxy:

1. Enable the `cloudflare.template.yml` in your `app.yml` templates section
2. The nginx configuration will automatically trust Cloudflare IP ranges
3. Real IP headers (`cf-connecting-ip`) are configured by default

## China Deployment Notes

When deploying in China, enable the China template for faster downloads:

```yaml
templates:
  - "templates/web.china.template.yml"
```

This template configures:
- Tsinghua University mirror for Ruby gems
- NPM mirror (npmmirror.com) for JavaScript packages
- GitHub proxy for plugin downloads

## Architecture

This project is based on the official [discourse_docker](https://github.com/discourse/discourse_docker) project with the following modifications:

- Pre-configured `app.yml` with China and Cloudflare optimizations
- Extended plugin list for community features
- Custom DNS and nginx configurations
- Docker Compose support for simplified deployment

### Security Architecture

The pre-built Docker image **does not contain any sensitive information**. All configuration values (SMTP credentials, domain names, admin emails) are:

1. Passed as runtime environment variables via `docker run -e` flags or `docker compose` env section
2. Written to `/var/www/discourse/config/discourse.conf` at container startup by the `copy-env` script
3. Never baked into the image filesystem during the build process

This means the image can be safely shared and distributed publicly without exposing any credentials.

## CI/CD

The image is automatically built and pushed to GitHub Container Registry (GHCR) when changes are pushed to the `main` branch. The workflow uses the official Discourse launcher binary to ensure compatibility.

Image location: `ghcr.io/cshdotcom/custom-discourse:latest` (private GHCR package)

## Data Persistence

All persistent data is stored in Docker volumes:

| Volume | Container Path | Purpose |
|--------|---------------|---------|
| `discourse_shared` | `/shared` | PostgreSQL data, uploads, backups |
| `discourse_log` | `/var/log` | Application and nginx logs |
| `discourse_maxmind` | `/var/www/discourse/vendor/data` | GeoIP data |

## Upgrading

To upgrade to a new image version:

```bash
docker compose pull
docker compose up -d
```

Or with the launcher:

```bash
./launcher rebuild app
```

## License

This project follows the same license as the official Discourse Docker project. Discourse itself is open source under the GPL v2 license.

## Acknowledgments

- [Discourse](https://github.com/discourse/discourse) - The discussion platform
- [discourse_docker](https://github.com/discourse/discourse_docker) - Official Docker image builder
- [NodeByte Community](https://nodebyte.cn) - Community customization and testing
