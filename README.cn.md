<div align="center">
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

| 应用名称 | 类别 | 简介 | 快速传送门 |
| :--- | :--- | :--- | :--- |
| **Nginx** | 反向代理网关 | 统一入口，为所有服务提供 HTTPS 反代与 SSL 终结。 | [📂 进入](./nginx) |
| **WordPress** | 内容管理系统 | 全球最流行的博客与网站内容管理系统。 | [📂 进入](./wordpress) |
| **Uptime Kuma** | 监控面板 | 自架的服务监控工具，支持多种通知管道。 | [📂 进入](./uptime-kuma) |
| **n8n** | 自动化工作流 | 强大的低代码流程引擎，实现异步任务处理与多系统间的数据整合。 | [📂 进入](./n8n) |
| **Evolution API** | 通讯桥接器 | 高性能 WhatsApp API 解决方案，支持多实例管理与高并发 Webhook 推送。 | [📂 进入](./evo-api) |
| **SFTPGo** | 档案传输 | 支持 SFTP / WebDAV 的档案管理服务，附带 Web 管理界面。 | [📂 进入](./sftpgo) |

---

# 快速开始

> 本文档是一份从零到上线的快速指南，适用于全新 VPS 部署。

---

## 部署前置清单

在开始之前，请准备以下资源：

- [ ] 一台已安装 Docker Engine `20.10+` 与 Docker Compose `v2.0+` 的 VPS
- [ ] 域名已解析（A 记录指向 VPS IP）
- [ ] SSL 证书文件（`.pem` + `.key`）💡 **推荐**：前往 Cloudflare 后台申请 15年有效期 **Origin CA 证书**。

---

## 快速部署流程

### 第一步：启动 Nginx 网关（必须最先启动）

Nginx 的 `docker-compose.yml` 会创建共享网络 `nginx_net`，所有其他服务都依赖这个网络。

```bash
cd nginx

# 放置 SSL 证书到 certs/ 目录
# 你的证书文件名需要与 conf.d/*.conf 中的路径对应
ls certs/
# → your-domain.pem  your-domain.key （或其他名称，需修改 conf 中的路径）

docker compose up -d
```

### 第二步：启用需要的 Nginx 反代配置

所有反代配置以 `.conf.example` 形式存放，Nginx **不会自动载入**。
你只需为 **实际使用的服务** 启用对应的配置：

```bash
# 例如：只启用 WordPress 和 Uptime Kuma
cp nginx/conf.d/wordpress.conf.example nginx/conf.d/wordpress.conf
cp nginx/conf.d/uptime-kuma.conf.example nginx/conf.d/uptime-kuma.conf
```

可用的配置模板：

| 模板文件 | 对应服务 |
|---------|----------|
| `wordpress.conf.example` | WordPress |
| `uptime-kuma.conf.example` | Uptime Kuma |
| `n8n.conf.example` | n8n |
| `evolution_api.conf.example` | Evolution API |
| `sftpgo.conf.example` | SFTPGo |

启用后，修改 `.conf` 中标有 `⚠️` 的行（域名 + 证书路径）：

```bash
# 快速定位所有需要修改的位置
grep -n "⚠️" nginx/conf.d/*.conf
```

修改完成后重载 Nginx：

```bash
docker exec nginx_gateway nginx -s reload
```

### 第三步：启动各应用服务

每个应用的部署流程一致：

```bash
# 以 WordPress 为例
cd wordpress
cp .env.example .env
nano .env           # 修改所有 CHANGE_ME_ 开头的值
docker compose up -d
```

> 💡 建议使用 `openssl rand -hex 24` 生成强随机密码。

> ⚠️ **启动顺序**：Nginx 必须第一个启动（创建 `nginx_net` 网络），其余服务顺序不限。

---

## ❓ 常见问题 (FAQ)

### Q: 启动服务时报 `network nginx_net not found`？

**A:** 你还没启动 Nginx。网络 `nginx_net` 由 Nginx 的 `docker-compose.yml` 创建，必须先执行：

```bash
cd nginx && docker compose up -d
```

### Q: Nginx 启动就报错 `host not found in upstream`？

**A:** 你启用了某个服务的 `.conf`，但该服务的容器还没启动。解决方法：
1. 启动对应的服务容器，或
2. 删除不需要的 `.conf` 文件（保留 `.conf.example` 即可）

```bash
# 删除不需要的配置
rm nginx/conf.d/n8n.conf
docker exec nginx_gateway nginx -s reload
```

### Q: SSL 证书文件名跟 conf 里的不一样怎么办？

**A:** 两种方法：

1. 重命名证书文件，使其与 conf 中的路径一致
2. 修改 `nginx/conf.d/*.conf` 中的 `ssl_certificate` 和 `ssl_certificate_key` 路径

### Q: 修改了 Nginx 配置后需要重启吗？

**A:** 不需要重启整个容器，只需重载配置：

```bash
docker exec nginx_gateway nginx -s reload
```

### Q: 如何查看某个服务的日志？

**A:** 使用 Docker 日志命令：

```bash
docker logs -f <容器名>
# 例如：docker logs -f n8n
```

### Q: 如何更新某个服务的镜像？

**A:**

```bash
cd <服务目录>
docker compose pull
docker compose up -d
```
