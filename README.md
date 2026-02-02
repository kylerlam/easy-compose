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
| **WordPress** | CMS | 全球最流行的博客系統 | [📂 進入](./wordpress) |
|  |  |  |  |

---

## ✨ 核心特性 (Key Features)

為什麼選擇本倉庫的配置？我們遵循 **DevOps 最佳實踐**：

- **🛡️ 數據隔離 (Data Isolation)**: 所有應用數據掛載於當前目錄的 `./*_data`，方便備份與遷移。
- **🔐 安全優先 (Security First)**: 敏感信息（密碼、密鑰）建議使用 `.env` 環境變量管理。
- **⚡ 開箱即用 (Production Ready)**: 用簡單的指令開始立馬開始，拒絕繁雜的環境配置步驟。
- **🐳 純淨依賴 (Zero Dependency)**: 僅依賴 Docker 引擎，無需單獨安裝 PHP, Python, Node.js 等本地環境。

## 🛠️ 環境要求 (Prerequisites)

在開始之前，請確保你的服務器已安裝：

- **Docker Engine** `20.10+`
- **Docker Compose** `v2.0+` (新版 Docker 已內置 `docker compose` 命令)

> 💡 **提示**: 如果你是 Windows 用戶，推薦使用 WSL2 後端以獲得最佳 I/O 性能。

## 🚀 通用部署流程 (Workflow)

所有應用均遵循標準化的操作流程：

1. **進入目錄**: 選擇你需要的應用，例如 `cd wordpress`
2. **生成配置**: 复制环境变量模板 `cp .env.example .env`
3. **修改參數**: 使用編輯器修改 `.env` 中的配置信息（如密碼或端口）
4. **啟動服務**: 執行 `docker-compose up -d`
5. **瀏覽訪問**: 根據配置的端口（如 `http://localhost:8080`）訪問

## 📂 目錄結構說明

為了方便維護，本倉庫採用 **Monorepo** 結構：

```text
.
├── wordpress/              # 應用 A
│   ├── docker-compose.yml  # 核心編排文件
│   ├── .env.example        # 環境變量模板 (脫敏)
│   └── README.md           # 該應用的專屬說明書
├── nginx-proxy/            # 應用 B
└── ...
```

## 🤝 致謝 (Acknowledgements)

本項目的架構設計與靈感來源於開源社區的先驅們。我們站在巨人的肩膀上，致力於讓部署變得更簡單。特別感謝：

- **[Docker Awesome Compose](https://github.com/docker/awesome-compose)**
  
  <br> 提供了 Docker Compose 的官方最佳實踐標準與基礎模板，是本項目的基石。
  
- **[Kejilion](https://github.com/kejilion)**
  <br> 在 Linux 運維腳本與用戶交互體驗（UX）上提供了寶貴的思路與靈感，推動了中文社區的便利化部署。
  
- **[Google Gemini](https://deepmind.google/technologies/gemini/)**
  <br> 提供了代碼重構建議與文檔生成的 AI 輔助，加速了本項目的製作流程。
