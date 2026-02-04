# Vaultwarden Server

A production-ready deployment of Vaultwarden (unofficial Bitwarden server) using Docker Compose with PostgreSQL and Caddy for HTTPS.

## 🚀 Production URL

[https://vault.dreamit.software](https://vault.dreamit.software/)

## 📋 Features

- Self-hosted password manager compatible with Bitwarden clients
- Automatic HTTPS with SSL certificates (Caddy)
- PostgreSQL database with persistent storage
- WebSocket notifications enabled
- Email notifications via SMTP
- Admin panel for management
- Security hardening (IP blocking, rate limiting)
- GitHub Actions deployment to Hetzner server

## 🛠 Prerequisites

- Docker and Docker Compose
- GitHub repository with Actions enabled
- Hetzner server (or similar hosting)
- Domain name pointing to your server

## 🔧 Local Development

1. Clone the repository:
   ```bash
   git clone https://github.com/DreamIT-VE/vault-server.git
   cd vault-server
   ```

2. Configure environment:
   - Copy `.env` and update values as needed
   - For local development, you may need to adjust DOMAIN and disable some security features

3. Start services:
   ```bash
   docker compose up -d --build
   ```

4. Access Vaultwarden:
   - Open [http://localhost:80](http://localhost:80/) (or your configured port)
   - Create your first account

## 🚀 Production Deployment

### Initial Setup

1. Fork this repository to your GitHub account

2. Upload secrets to GitHub:
   ```bash
   ./upload-github-secrets.sh
   ```

3. Update configuration:
   - Modify `.env` with your domain and settings
   - Update `Caddyfile` with your domain
   - Adjust `docker-compose.yml` if needed

4. Commit and push:
   ```bash
   git add .
   git commit -m "Configure Vaultwarden deployment"
   git push origin main
   ```

### Automated Deployment

The deployment is handled automatically via GitHub Actions:

1. Go to Actions tab in your GitHub repository
2. Select "Deploy Vaultwarden to Hetzner (Production)" workflow
3. Click "Run workflow" to trigger deployment

The workflow will:
- Create `.env` file from GitHub secrets
- Copy files to your Hetzner server via rsync
- Run `docker compose pull && docker compose up -d --build`

### Manual Deployment (if needed)

If you need to deploy manually:

```bash
# On your Hetzner server
cd /path/to/vault-server
docker compose pull
docker compose up -d --build
```

Note: This preserves existing volumes and data. Vaultwarden handles database migrations automatically.

## ⚙️ Configuration

### Environment Variables

Key configuration in `.env`:

```bash
# Hetzner deployment (for GitHub Actions)
HETZNER_PASSWORD=your-server-password
HETZNER_USER=root
HETZNER_HOST=your-server-ip
HETZNER_PROJECT_PATH=vaultwarden

# Database
POSTGRES_USER=vaultwarden_admin
POSTGRES_PASSWORD=secure-password
POSTGRES_DB=vaultwarden_db
DATABASE_URL=postgresql://vaultwarden_admin:password@postgres:5432/vaultwarden_db

# Email
SMTP_HOST=your-smtp-host
SMTP_PORT=587
SMTP_USERNAME=your-smtp-username
SMTP_PASSWORD=your-smtp-password
SMTP_FROM=noreply@yourdomain.com
SMTP_SECURITY=starttls

# Vaultwarden
DOMAIN=https://yourdomain.com
ADMIN_TOKEN=your-argon2-phc-hash
SIGNUPS_ALLOWED=false
```

### File Structure

```
vault-server/
├── docker-compose.yml    # Service definitions
├── Caddyfile            # Reverse proxy configuration
├── .env                 # Environment variables (gitignored)
├── .github/
│   └── workflows/
│       └── deploy-production.yml  # GitHub Actions deployment
├── upload-github-secrets.sh     # Secret upload script
├── upload-github-secrets.ps1    # PowerShell version
└── README.md           # This file
```

### Reverse Proxy Layout

- Caddy terminates HTTPS for Vaultwarden (`vault.dreamit.software`)
- Ensure DNS records for your hostname point to the server for certificate issuance

## 🔍 Troubleshooting

### Common Issues

- **Email not sending**: Check SMTP configuration in `.env`, verify credentials, and review `docker compose logs vaultwarden` for SMTP errors

- **Database connection failed**:
  - Ensure PostgreSQL container is healthy: `docker compose ps postgres`
  - Check `DATABASE_URL` format
  - Verify network connectivity between containers

- **HTTPS not working**: Verify DNS records, confirm Caddy obtained certificates, and check `docker compose logs caddy`

- **Admin panel not accessible**: Ensure `ADMIN_TOKEN` is set to a valid Argon2 PHC hash. Generate one with `vaultwarden hash` or use online tools

### Logs

View service logs:

```bash
# All services
docker compose logs

# Specific service
docker compose logs vaultwarden
docker compose logs postgres
docker compose logs caddy
```

### Database

Access PostgreSQL directly:

```bash
docker compose exec postgres psql -U vaultwarden_admin -d vaultwarden_db
```

## 🔒 Security Notes

- All secrets are stored in GitHub repository environment (not in code)
- Database passwords should be strong and unique
- SSL certificates are managed automatically by Caddy
- IP blocking and rate limiting are enabled by default
- Admin token uses Argon2 PHC hashing for security

## 📝 Notes

- Access the admin panel at `https://yourdomain.com/admin` using the `ADMIN_TOKEN`
- To create users, temporarily enable `SIGNUPS_ALLOWED=true` in the admin panel or invite users via email
- Data persists in Docker volumes
- Automatic updates are enabled in production
- WebSocket notifications require HTTPS
- Mobile clients require proper DOMAIN configuration

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test locally
5. Submit a pull request

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details