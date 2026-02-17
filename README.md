<div align="center">
  <h1>🐳 Easy Compose Collection</h1>
  <p>
    主流開源應用的 Docker Compose 一鍵部署集合。
  </p>
  <p>
    <b>繁體中文</b> | <a href="./README.cn.md">简体中文</a> | <a href="./README.en.md">English</a>
  </p>
</div>



---

## ✨ 應用列表

| 應用名稱 | 類別 | 簡介 | 快速傳送門 |
| :--- | :--- | :--- | :--- |
| **Nginx** | 反向代理網關 | 統一入口，為所有服務提供 HTTPS 反代與 SSL 終結。 | [📂 進入](./nginx) |
| **WordPress** | 內容管理系統 | 全球最流行的博客與網站內容管理系統。 | [📂 進入](./wordpress) |
| **Uptime Kuma** | 監控面板 | 自架的服務監控工具，支持多種通知管道。 | [📂 進入](./uptime-kuma) |
| **n8n** | 自動化工作流 | 強大的低代碼流程引擎，實現異步任務處理與多系統間的數據整合。 | [📂 進入](./n8n) |
| **Evolution API** | 通訊橋接器 | 高性能 WhatsApp API 解決方案，支持多實例管理與高併發 Webhook 推送。 | [📂 進入](./evo-api) |
| **SFTPGo** | 檔案傳輸 | 支持 SFTP / WebDAV 的檔案管理服務，附帶 Web 管理界面。 | [📂 進入](./sftpgo) |

---

# 快速開始

> 本文檔是一份從零到上線的快速指南，適用於全新 VPS 部署。

---

## 部署前置清單

在開始之前，請準備以下資源：

- [ ] 一台已安裝 Docker Engine `20.10+` 與 Docker Compose `v2.0+` 的 VPS
- [ ] 域名已解析（A 記錄指向 VPS IP）
- [ ] SSL 證書文件（`.pem` + `.key`）💡 **推薦**：前往 Cloudflare 後台申請 15年有效期 **Origin CA 證書**。

---

## 快速部署流程

### 第一步：啟動 Nginx 網關（必須最先啟動）

Nginx 的 `docker-compose.yml` 會創建共享網絡 `nginx_net`，所有其他服務都依賴這個網絡。

```bash
cd nginx

# 放置 SSL 證書到 certs/ 目錄
# 你的證書文件名需要與 conf.d/*.conf 中的路徑對應
ls certs/
# → your-domain.pem  your-domain.key （或其他名稱，需修改 conf 中的路徑）

docker compose up -d
```

### 第二步：啟用需要的 Nginx 反代配置

所有反代配置以 `.conf.example` 形式存放，Nginx **不會自動載入**。
你只需為 **實際使用的服務** 啟用對應的配置：

```bash
# 例如：只啟用 WordPress 和 Uptime Kuma
cp nginx/conf.d/wordpress.conf.example nginx/conf.d/wordpress.conf
cp nginx/conf.d/uptime-kuma.conf.example nginx/conf.d/uptime-kuma.conf
```

可用的配置模板：

| 模板文件 | 對應服務 |
|---------|----------|
| `wordpress.conf.example` | WordPress |
| `uptime-kuma.conf.example` | Uptime Kuma |
| `n8n.conf.example` | n8n |
| `evolution_api.conf.example` | Evolution API |
| `sftpgo.conf.example` | SFTPGo |

啟用後，修改 `.conf` 中標有 `⚠️` 的行（域名 + 證書路徑）：

```bash
# 快速定位所有需要修改的位置
grep -n "⚠️" nginx/conf.d/*.conf
```

修改完成後重載 Nginx：

```bash
docker exec nginx_gateway nginx -s reload
```

### 第三步：啟動各應用服務

每個應用的部署流程一致：

```bash
# 以 WordPress 為例
cd wordpress
cp .env.example .env
nano .env           # 修改所有 CHANGE_ME_ 開頭的值
docker compose up -d
```

> 💡 建議使用 `openssl rand -hex 24` 生成強隨機密碼。

> ⚠️ **啓動順序**：（無強制依賴，但推薦）：Nginx 必須第一個啟動（建立 `nginx_net` 網路），其餘服務順序不限。

---

## ❓ 常見問題 (FAQ)

### Q: 啟動服務時報 `network nginx_net not found`？

**A:** 你還沒啟動 Nginx。網絡 `nginx_net` 由 Nginx 的 `docker-compose.yml` 創建，必須先執行：

```bash
cd nginx && docker compose up -d
```

### Q: Nginx 啟動就報錯 `host not found in upstream`？

**A:** 你啟用了某個服務的 `.conf`，但該服務的容器還沒啟動。解決方法：
1. 啟動對應的服務容器，或
2. 刪除不需要的 `.conf` 文件（保留 `.conf.example` 即可）

```bash
# 刪除不需要的配置
rm nginx/conf.d/n8n.conf
docker exec nginx_gateway nginx -s reload
```

### Q: SSL 證書文件名跟 conf 裡的不一樣怎麼辦？

**A:** 兩種方法：

1. 重命名證書文件，使其與 conf 中的路徑一致
2. 修改 `nginx/conf.d/*.conf` 中的 `ssl_certificate` 和 `ssl_certificate_key` 路徑

### Q: 修改了 Nginx 配置後需要重啟嗎？

**A:** 不需要重啟整個容器，只需重載配置：

```bash
docker exec nginx_gateway nginx -s reload
```

### Q: 如何查看某個服務的日誌？

**A:** 使用 Docker 日誌命令：

```bash
docker logs -f <容器名>
# 例如：docker logs -f n8n
```

### Q: 如何更新某個服務的鏡像？

**A:**

```bash
cd <服務目錄>
docker compose pull
docker compose up -d
```
