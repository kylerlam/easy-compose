<div align="center">
  <h1>🐳 Easy Compose Collection</h1>
  <p>
    One-click Docker Compose deployment collection for popular open-source applications.
  </p>
  <p>
    <a href="./README.md">繁體中文</a> | 
    <a href="./README.cn.md">简体中文</a> | 
    <b>English</b>
  </p>
</div>



---

## ✨ Application List

| Application | Category | Description | Quick Link |
| :--- | :--- | :--- | :--- |
| **Nginx** | Reverse Proxy Gateway | Unified entry point providing HTTPS reverse proxy and SSL termination for all services. | [📂 Enter](./nginx) |
| **WordPress** | CMS | The world's most popular blogging and content management system. | [📂 Enter](./wordpress) |
| **Uptime Kuma** | Monitoring Dashboard | Self-hosted monitoring tool with multi-channel notification support. | [📂 Enter](./uptime-kuma) |
| **n8n** | Workflow Automation | A powerful low-code engine for asynchronous task processing and multi-system data integration. | [📂 Enter](./n8n) |
| **Evolution API** | Communication Gateway | High-performance WhatsApp API solution supporting multi-instance management and high-concurrency webhooks. | [📂 Enter](./evo-api) |
| **SFTPGo** | File Transfer | SFTP / WebDAV file management service with a built-in web admin panel. | [📂 Enter](./sftpgo) |

---

# Quick Start

> A zero-to-production quick guide for deploying on a fresh VPS.

---

## Prerequisites

Before you begin, prepare the following:

- [ ] A VPS with Docker Engine `20.10+` and Docker Compose `v2.0+` installed
- [ ] Domain name resolved (A record pointing to your VPS IP)
- [ ] SSL certificate files (`.pem` + `.key`) 💡 **Recommended**: Apply for a 15-year **Origin CA Certificate** from the Cloudflare dashboard.

---

## Deployment Workflow

### Step 1: Start the Nginx Gateway (Must Start First)

Nginx's `docker-compose.yml` creates the shared network `nginx_net`, which all other services depend on.

```bash
cd nginx

# Place SSL certificates in the certs/ directory
# Certificate filenames must match the paths in conf.d/*.conf
ls certs/
# → your-domain.pem  your-domain.key (or other names — update paths in conf accordingly)

docker compose up -d
```

### Step 2: Enable the Nginx Reverse Proxy Configs You Need

All reverse proxy configs are stored as `.conf.example` files — Nginx **will not load them automatically**.
You only need to enable configs for the **services you actually use**:

```bash
# Example: enable only WordPress and Uptime Kuma
cp nginx/conf.d/wordpress.conf.example nginx/conf.d/wordpress.conf
cp nginx/conf.d/uptime-kuma.conf.example nginx/conf.d/uptime-kuma.conf
```

Available config templates:

| Template File | Service |
|---------|----------|
| `wordpress.conf.example` | WordPress |
| `uptime-kuma.conf.example` | Uptime Kuma |
| `n8n.conf.example` | n8n |
| `evolution_api.conf.example` | Evolution API |
| `sftpgo.conf.example` | SFTPGo |

After enabling, edit the lines marked with `⚠️` in each `.conf` (domain + certificate path):

```bash
# Quickly locate all lines that need modification
grep -n "⚠️" nginx/conf.d/*.conf
```

Reload Nginx after making changes:

```bash
docker exec nginx_gateway nginx -s reload
```

### Step 3: Start Application Services

Each application follows the same deployment process:

```bash
# Example: WordPress
cd wordpress
cp .env.example .env
nano .env           # Change all values starting with CHANGE_ME_
docker compose up -d
```

> 💡 Use `openssl rand -hex 24` to generate strong random passwords.

> ⚠️ **Startup order**: Nginx must start first (it creates the `nginx_net` network). All other services can start in any order.

---

## ❓ FAQ

### Q: Getting `network nginx_net not found` when starting a service?

**A:** You haven't started Nginx yet. The `nginx_net` network is created by Nginx's `docker-compose.yml` — you must start it first:

```bash
cd nginx && docker compose up -d
```

### Q: Nginx fails to start with `host not found in upstream`?

**A:** You enabled a `.conf` for a service whose container isn't running yet. Solutions:
1. Start the corresponding service container, or
2. Remove the unneeded `.conf` file (keep the `.conf.example`)

```bash
# Remove unneeded config
rm nginx/conf.d/n8n.conf
docker exec nginx_gateway nginx -s reload
```

### Q: SSL certificate filenames don't match the conf?

**A:** Two options:

1. Rename your certificate files to match the paths in the conf
2. Edit `ssl_certificate` and `ssl_certificate_key` paths in `nginx/conf.d/*.conf`

### Q: Do I need to restart after changing Nginx config?

**A:** No need to restart the entire container — just reload the config:

```bash
docker exec nginx_gateway nginx -s reload
```

### Q: How to view logs for a service?

**A:** Use the Docker logs command:

```bash
docker logs -f <container_name>
# Example: docker logs -f n8n
```

### Q: How to update a service's image?

**A:**

```bash
cd <service_directory>
docker compose pull
docker compose up -d
```
