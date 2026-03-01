# Twitter/X 内容归档工具 / Twitter/X Content Archiver

本工具通过浏览器自动化技术将 Twitter/X 上的推文和媒体文件保存到本地存储，无需 Twitter API 密钥。支持单条推文和推文串的完整归档，包含文本、图片和视频，并提供 Web 图形界面与命令行两种操作方式。

This tool archives tweets and media from Twitter/X to local storage using browser automation — no Twitter API key required. It supports saving single tweets and full threads, including text, images, and videos, through both a web interface and a command-line interface.

![Python](https://img.shields.io/badge/Python-3.7%2B-blue) ![License](https://img.shields.io/badge/License-MIT-green) ![No API Key](https://img.shields.io/badge/Twitter%20API-Not%20Required-brightgreen)

---

## ✨ 功能特性 / Features

- 无需 Twitter API 密钥，使用 Playwright 浏览器自动化抓取 / No Twitter API key required — uses Playwright browser automation
- 支持单条推文和完整推文串 / Supports single tweets and full threads
- 自动下载图片和视频 / Automatically downloads images and videos
- 生成多格式内容文件：纯文本、Markdown、Reader-mode HTML / Saves content in multiple formats: plain text, Markdown, Reader-mode HTML
- 保存完整元数据（作者信息、发布时间等）为 JSON / Saves complete metadata (author, timestamp, etc.) as JSON
- 内置任务队列与失败重试机制（指数退避） / Built-in task queue with automatic retry on failure (exponential backoff)
- Web UI 支持实时日志流、任务监控和内容浏览 / Web UI with real-time log streaming, task monitoring, and content browsing
- 每条归档内容可生成唯一分享链接 / Each archived tweet gets a unique public share link
- 可选：基于 Gemini 或 Claude API 的 AI 智能标签生成 / Optional: AI-powered tag generation via Gemini or Claude API
- 可选：通过 FFmpeg 生成视频缩略图 / Optional: video thumbnails via FFmpeg

---

## 🚀 快速开始 / Quick Start

### 安装 / Installation

**前提条件 / Prerequisites:** Python 3.7+，以及可选的 FFmpeg（用于视频缩略图）。

```bash
# 1. 克隆仓库 / Clone the repository
git clone https://github.com/your-username/twitter_collector.git
cd twitter_collector

# 2. 安装 Python 依赖 / Install Python dependencies
pip install -r requirements.txt

# 3. 安装 Playwright 浏览器（必需）/ Install Playwright browser (required)
python -m playwright install chromium

# 4. 复制并编辑配置文件 / Copy and edit the config file
cp config.ini.example config.ini
```

### 启动 Web 界面 / Start Web UI

```bash
python run_web.py
```

浏览器访问 `http://localhost:6201`，默认登录账号：`admin` / 密码：`admin`。

Open `http://localhost:6201` in your browser. Default login: username `admin`, password `admin`.

> 首次登录后请立即修改密码：`python tools/change_password.py`
>
> Change the default password immediately after first login: `python tools/change_password.py`

---

## ⚙️ 配置说明 / Configuration

将 `config.ini.example` 复制为 `config.ini` 并按需修改。

Copy `config.ini.example` to `config.ini` and edit as needed.

| 配置项 / Option | 说明 / Description | 默认值 / Default |
|---|---|---|
| `[storage] base_path` | 推文保存路径 / Save directory for archived tweets | `./saved_tweets` |
| `[storage] create_date_folders` | 按日期创建子文件夹 / Create date-based subfolders | `true` |
| `[download] max_retries` | 下载失败最大重试次数 / Max download retry attempts | `3` |
| `[download] timeout_seconds` | 请求超时时间（秒）/ Request timeout in seconds | `30` |
| `[scraper] use_playwright` | 使用 Playwright 浏览器自动化（推荐）/ Use Playwright browser automation (recommended) | `true` |
| `[scraper] headless` | 无头模式运行浏览器 / Run browser in headless mode | `true` |
| `[scraper] debug_mode` | 调试模式，出错时保存截图 / Debug mode, saves screenshots on errors | `false` |
| `[ai] gemini_api_key` | Gemini API 密钥（可选，用于 AI 标签）/ Gemini API key (optional, for AI tags) | _(unset)_ |
| `[ai] claude_api_key` | Claude API 密钥（可选，备用 AI 标签）/ Claude API key (optional, alternative AI tags) | _(unset)_ |

**环境变量覆盖 / Environment variable overrides:**

| 环境变量 / Variable | 说明 / Description |
|---|---|
| `SAVE_PATH` | 覆盖保存路径 / Override save path |
| `USE_PLAYWRIGHT` | 覆盖 Playwright 开关 / Override Playwright toggle |
| `PLAYWRIGHT_HEADLESS` | 覆盖无头模式 / Override headless mode |
| `PLAYWRIGHT_DEBUG` | 设为 `true` 启用调试截图 / Set to `true` to enable debug screenshots |
| `SSL_CERT_PATH` / `SSL_KEY_PATH` | 启用 HTTPS / Enable HTTPS |

---

## 📖 使用方法 / Usage

### Web 界面 / Web Interface

启动后访问 `http://localhost:6201`，Web 界面提供以下功能页面：

After starting, visit `http://localhost:6201`. The web interface provides the following pages:

| 路由 / Route | 功能 / Purpose |
|---|---|
| `/` | 提交 Twitter URL 开始归档 / Submit a Twitter URL to start archiving |
| `/tasks` | 查看任务队列状态 / View task queue status |
| `/saved` | 浏览和搜索已归档推文 / Browse and search archived tweets |
| `/tags` | 管理 AI 生成的标签 / Manage AI-generated tags |
| `/retries` | 查看失败任务并手动重试 / View failed tasks and retry manually |
| `/view/<slug>` | 通过分享链接查看归档内容 / View archived content via share link |
| `/debug` | 系统状态和卡死任务重置 / System status and stuck task reset |

### 命令行 / CLI

```bash
# 基本用法：归档单条推文 / Basic usage: archive a single tweet
python main.py https://x.com/username/status/1234567890

# 归档推文串 / Archive a thread
python main.py https://twitter.com/username/status/1234567890

# 跳过媒体下载 / Skip media downloads
python main.py https://x.com/username/status/1234567890 --no-media

# 仅保存当前推文，不展开推文串 / Save only this tweet, do not expand thread
python main.py https://x.com/username/status/1234567890 --single-only

# 强制作为推文串处理 / Force treatment as a thread
python main.py https://x.com/username/status/1234567890 --thread-only

# 指定输出目录 / Specify output directory
python main.py https://x.com/username/status/1234567890 --output /path/to/save

# 显示详细输出 / Show verbose output
python main.py https://x.com/username/status/1234567890 --verbose
```

**支持的 URL 格式 / Supported URL formats:**

```
https://twitter.com/username/status/1234567890
https://x.com/username/status/1234567890
https://mobile.twitter.com/username/status/1234567890
https://m.twitter.com/username/status/1234567890
```

---

## 📁 输出结构 / Output Structure

每条归档推文保存在以日期和推文 ID 命名的文件夹中。

Each archived tweet is saved in a folder named by date and tweet ID.

```
saved_tweets/
└── 2024-01-15_1234567890123456789/
    ├── content.txt        # 推文纯文本 / Plain text content
    ├── content.html       # Reader-mode HTML
    ├── content.md         # Markdown 格式 / Markdown format
    ├── metadata.json      # 完整元数据（作者、时间等）/ Full metadata (author, timestamp, etc.)
    ├── avatar.jpg         # 作者头像 / Author avatar
    ├── images/            # 推文图片 / Tweet images
    │   ├── image_01.jpg
    │   └── image_02.png
    ├── videos/            # 推文视频 / Tweet videos
    │   └── video_01.mp4
    └── thumbnails/        # 视频缩略图（需 FFmpeg）/ Video thumbnails (requires FFmpeg)
        └── video_01.jpg
```

---

## 🏷️ AI 标签功能 / AI Tag Generation (Optional)

本工具支持在每次归档成功后自动为推文生成语义标签，帮助分类和检索。

The tool can automatically generate semantic tags for each archived tweet to aid categorization and search.

生成方法按优先级排列 / Generation methods in priority order:

1. **Gemini API** — 在 `config.ini` 中设置 `gemini_api_key`（推荐，免费额度充足）。提示词模板位于 `prompts.ini`。/ Set `gemini_api_key` in `config.ini` (recommended, generous free tier). Prompt template is in `prompts.ini`.
2. **Claude API** — 在 `config.ini` 中设置 `claude_api_key` 作为备选方案。/ Set `claude_api_key` in `config.ini` as an alternative.
3. **规则匹配 / Rule-based** — 无需 API 密钥，基于内置关键词规则自动生成基础标签。/ No API key required; uses built-in keyword rules for basic tagging.

通过 Web 界面的 `/tags` 页面可管理所有标签，也可以在 `/saved` 页面对单条内容手动触发标签生成。

Manage all tags on the `/tags` page, or trigger tag generation for individual items on the `/saved` page.

---

## 🔧 故障排除 / Troubleshooting

**推文无法找到 / Tweet not found**

中文：推文可能已被删除或设为私有，请检查 URL 是否正确，并确认该推文在浏览器中可以正常访问。

English: The tweet may have been deleted or made private. Verify the URL is correct and that the tweet is accessible in a regular browser.

---

**Playwright 浏览器未安装 / Playwright browser not installed**

```bash
python -m playwright install chromium
```

---

**网络连接问题 / Network connectivity issues**

中文：检查网络连接和防火墙设置。如果所在地区访问 Twitter/X 受限，需要配置代理后再使用本工具。

English: Check your network connection and firewall settings. If Twitter/X is blocked in your region, configure a proxy before using this tool.

---

**文件权限错误 / File permission errors**

中文：确认对 `base_path` 所指向的目录有写入权限，并检查磁盘剩余空间。

English: Ensure the user running the tool has write permission to the `base_path` directory and that sufficient disk space is available.

---

**任务卡死不动 / Tasks stuck in processing**

中文：访问 Web 界面的 `/debug` 页面，使用"重置卡死任务"功能。

English: Visit the `/debug` page in the web UI and use the "Reset Stuck Tasks" function.

---

**启用调试模式 / Enabling debug mode**

设置 `debug_mode = true` 或 `PLAYWRIGHT_DEBUG=true`，错误截图将保存在项目根目录。

Set `debug_mode = true` in `config.ini` or set `PLAYWRIGHT_DEBUG=true`. Error screenshots will be saved in the project root directory.

---

## ⚠️ 免责声明 / Disclaimer

**请在使用本工具前仔细阅读以下声明。/ Please read the following carefully before using this tool.**

- **仅供个人存档使用。** 本工具设计用途为个人保存公开内容，供离线阅读和个人研究。/ **For personal archival use only.** This tool is designed for saving publicly available content for offline reading and personal research.

- **请遵守 Twitter/X 服务条款。** 使用本工具须符合 [Twitter/X 服务条款](https://twitter.com/en/tos)。用户对自身的使用行为及其法律合规性承担全部责任。/ **You must comply with Twitter/X Terms of Service.** Use of this tool must conform to the [Twitter/X Terms of Service](https://twitter.com/en/tos). Users are solely responsible for their usage and its legal compliance.

- **禁止商业用途和大规模抓取。** 本工具不得用于商业目的、批量数据采集、训练机器学习模型或任何形式的大规模抓取行为。/ **Do not use for commercial purposes or mass scraping.** This tool must not be used for commercial purposes, bulk data collection, training machine learning models, or any form of mass scraping.

- **尊重他人版权。** 推文内容的版权归原作者所有。请勿在未经授权的情况下转载、分发或二次利用他人内容。/ **Respect copyright.** Tweet content is owned by its original authors. Do not republish, distribute, or repurpose others' content without authorization.

- **作者不对滥用行为负责。** 本项目作者及贡献者不对任何因滥用本工具而产生的法律问题、服务封禁或其他后果承担责任。/ **The author is not responsible for misuse.** The author and contributors of this project accept no liability for any legal issues, account suspensions, or other consequences arising from misuse of this tool.

---

## 📄 License

本项目采用 MIT 许可证。详见 [LICENSE](LICENSE) 文件。

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

## 🤝 贡献 / Contributing

欢迎通过 GitHub Issues 报告问题或提出功能建议，也欢迎提交 Pull Request。提交前请确保现有测试通过：

Bug reports, feature requests, and pull requests are welcome via GitHub Issues and PRs. Please ensure existing tests pass before submitting:

```bash
python -m pytest tests/
```
