<div align="center">
  <img src="https://raw.githubusercontent.com/docker-library/docs/master/docker/logo.png" width="100" />
  <h1>🐳 Easy Compose Collection</h1>
  <p>
    主流开源应用的 Docker Compose 一键部署集合。
  </p>
  <p>
    <a href="./README.md">繁體中文</a> | 
    <b>简体中文</b> | 
    <a href="./README.en.md">English</a>
  </p>
</div>

---

## ✨ 应用列表

| 应用名称          | 类别         | 简介                                                         | 快速传送门            |
| :---------------- | :----------- | :----------------------------------------------------------- | :-------------------- |
| **WordPress**     | 内容管理系统 | 全球最流行的博客与网站内容管理系统。                         | [📂 进入](./wordpress) |
| **n8n**           | 自动化工作流 | 强大的低代码流程引擎，实现异步任务处理与多系统间的数据整合。 | [📂 进入](./n8n)       |
| **Evolution API** | 通讯桥接器   | 高性能 WhatsApp API 解决方案，支持多实例管理与高并发 Webhook 推送。 | [📂 进入](./evo-api)   |

---

## ✨ 核心特性 (Key Features)

为什么选择本仓库的配置？我们遵循 **DevOps 最佳实践**：

- **🛡️ 数据隔离 (Data Isolation)**: 所有应用数据挂载于当前目录的 `./*_data`，方便备份与迁移。
- **🔐 安全优先 (Security First)**: 敏感信息（密码、密钥）建议使用 `.env` 环境变量管理。
- **⚡ 开箱即用 (Production Ready)**: 用简单的指令立刻开始，拒绝繁杂的环境配置步骤。
- **🐳 纯净依赖 (Zero Dependency)**: 仅依赖 Docker 引擎，无需单独安装 PHP, Python, Node.js 等本地环境。

## 🛠️ 环境要求 (Prerequisites)

在开始之前，请确保你的服务器已安装：

- **Docker Engine** `20.10+`
- **Docker Compose** `v2.0+` (新版 Docker 已内置 `docker compose` 命令)

> 💡 **提示**: 如果你是 Windows 用户，推荐使用 WSL2 后端以获得最佳 I/O 性能。

## 🚀 通用部署流程 (Workflow)

所有应用均遵循标准化的操作流程：

1. **进入目录**: 选择你需要的应用，例如 `cd wordpress`
2. **生成配置**: 复制环境变量模板 `cp .env.example .env`
3. **修改参数**: 使用编辑器修改 `.env` 中的配置信息（如密码或端口）
4. **启动服务**: 执行 `docker-compose up -d`
5. **浏览访问**: 根据配置的端口（如 `http://localhost:8080`）访问

## 📂 目录结构说明

为了方便维护，本仓库采用 **Monorepo** 结构：

```text
.
├── wordpress/              # 应用 A
│   ├── docker-compose.yml  # 核心编排文件
│   ├── .env.example        # 环境变量模板 (脱敏)
│   └── README.md           # 该应用的专属说明书
├── nginx-proxy/            # 应用 B
└── ...
