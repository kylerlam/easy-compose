<div align="center">
  <img src="https://raw.githubusercontent.com/docker-library/docs/master/docker/logo.png" width="100" />
  <h1>🐳 Easy Compose Collection</h1>
  <p>
    One-click Docker Compose deployment collection for popular open-source applications.
  </p>
  <p>
    <a href="./README.tw.md">繁體中文</a> | 
    <a href="./README.cn.md">简体中文</a> | 
    <b>English</b>
  </p>
</div>

---

## ✨ Application List

| App Name      | Category | Description                            | Quick Start                   |
| :------------ | :------- | :------------------------------------- | :---------------------------- |
| **WordPress** | CMS      | World's most popular blogging platform | [📂 Go](./wordpress)           |
| **Nginx PM**  | Network  | Visual management interface for Nginx  | [📂 Go](./nginx-proxy-manager) |

*(More applications to be added)*

---

## ✨ Key Features

Why choose this collection? We follow **DevOps best practices**:

- **🛡️ Data Isolation**: All application data is mounted to `./*_data` in the current directory for easy backup and migration.
- **🔐 Security First**: Sensitive information (passwords, keys) is managed via `.env` environment variables, not hardcoded.
- **⚡ Production Ready**: Start immediately with simple commands. No complex environment configuration required.
- **🐳 Zero Dependency**: Only depends on Docker Engine. No need to install PHP, Python, Node.js, etc., on the host.

## 🛠️ Prerequisites

Before you begin, ensure your server has the following installed:

- **Docker Engine** `20.10+`
- **Docker Compose** `v2.0+` (Built-in `docker compose` command in newer versions)

> 💡 **Tip**: For Windows users, it is highly recommended to use the **WSL2** backend for optimal I/O performance.

## 🚀 Workflow

All applications follow a standardized deployment process:

1. **Enter Directory**: Select the application you need, e.g., `cd wordpress`
2. **Initialize Config**: Copy the environment template `cp .env.example .env`
3. **Configure Env**: Copy the template and modify the `.env` file (e.g., passwords or ports).
4. **Start Service**: Run `docker-compose up -d`
5. **Access**: Open your browser and visit the configured port (e.g., `http://localhost:8080`).

## 📂 Directory Structure

For easier maintenance, this repository adopts a **Monorepo** structure:

```text
.
├── wordpress/              # Application A
│   ├── docker-compose.yml  # Core orchestration file
│   ├── .env.example        # Env variable template (Sanitized)
│   └── README.md           # App-specific documentation
├── nginx-proxy/            # Application B
└── ...
