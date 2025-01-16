# 部署指南

启动 Bot 之前, 需要为 Bot **设置头像**, 并关闭隐私模式

## 使用 docker-compose
### 最简配置

一般情况下仅需要 Bot Token 和你的 ID 即可启动一个 kmua 实例.

下载 [docker-compose.yml](https://github.com/krau/kmua-bot/blob/v2/docker-compose.yml)

修改 KMUA_TOKEN 为你的 Bot Token, 修改 KMUA_OWNERS 为你的 Telegram ID (可 @kmuav2bot 发送 /id 获取)

```bash
docker compose up -d
```

### 详细设置

如果你有更多需求, 请使用以下 `docker-compose.yml`.

```yaml
services:
  kmua:
    image: ghcr.io/krau/kmua-bot:v2
    container_name: kmua-bot
    restart: always
    volumes:
      - ./data:/kmua/data
      - ./logs:/kmua/logs
    env_file:
      - .env
    network_mode: host
```

在同目录下新建 `.env` 文件, 参照下方的环境变量说明进行配置.

#### 常规

- `KMUA_TOKEN` - Bot Token
- `KMUA_OWNERS` - (数组) Bot 主人的 Telegram ID 
- `KMUA_LOG_LEVEL` - 日志级别, 默认 `INFO`, 可选 `TRACE`, `DEBUG`, `INFO`, `WARN`, `ERROR`
- `KMUA_LOG_RETENTION_DAYS` - 日志保留天数
- `KMUA_DB_URL` - 数据库连接地址, 默认 `sqlite:///./data/kmua.db`
- `KMUA_MAX_DB_SIZE` - sqlite 数据库文件最大大小, 到达后自动清理头像缓存, 默认 100MB
- `KMUA_AVATAR_EXPIRE` - 头像缓存过期时间, 默认 1 天
- `TZ` - 时区, 默认 `Asia/Shanghai`
- `KMUA_HEALTH_CHECK_ENABLE` - 是否启用健康检查, 默认 `False`
- `KMUA_HEALTH_CHECK_PORT` - 健康检查API监听端口, 默认 `39848`
- `KMUA_HEALTH_CHECK_HOST` - 健康检查API监听地址, 默认 `0.0.0.0`

#### Bot API

- `KMUA_BASE_URL` - Bot API 地址, 默认 `https://api.telegram.org/bot` (官方)
- `KMUA_BASE_FILE_URL` - Bot 文件 API 地址, 默认 `https://api.telegram.org/file/bot`
- `KMUA_DROP_PENDING_UPDATES` - 是否丢弃未处理的更新, 默认 `false`

#### Webhook

- `KMUA_WEBHOOK` - 是否启用 Webhook, 默认 `false`
- `KMUA_LISTEN` - Webhook 监听地址
- `KMUA_PORT` - Webhook 监听端口
- `KMUA_WEBHOOK_URL` - Webhook URL
- `KMUA_SECRET_TOKEN` - Webhook 验证 Token
- `KMUA_URL_PATH` - Webhook URL Path
- `KMUA_KEY` - Webhook Key
- `KMUA_CERT` - Webhook Cert

#### 扩展功能

##### Redis

Redis 可能是其他扩展功能的依赖

- `KMUA_REDIS_URL` - Redis 连接地址

##### ManyACG (随机涩图/解析插画)

- `KMUA_MANYACG_API` - ManyACG API 地址, 默认 https://api.manyacg.top/v1
- `KMUA_MANYACG_API_KEY` - ManyACG API Key, 配置后可解析插画链接

##### NSFW 图像分类

- `KMUA_NSFWJS_API` - NSFWJS API 地址
- `KMUA_NSFWJS_TOKEN` - NSFWJS API Token

##### Meilisearch (消息搜索)

该功能需要 Redis

- `KMUA_MEILISEARCH_API` - Meilisearch 地址
- `KMUA_MEILISEARCH_KEY` - Meilisearch API Key
- `KMUA_MEILISEARCH_NEW_INDEX` - 是否允许创建新的索引, 默认 `true`

##### 图像超分辨率

该功能可选 Redis (缓存下载结果 id)

- `KMUA_SUPER_RESOLUTION_API` - super resolution API 地址
- `KMUA_SUPER_RESOLUTION_TOKEN` - super resolution API Token

##### 图像 Caption

- `KMUA_JOY_CAPTION_API` - [JoyCaption](https://huggingface.co/spaces/fancyfeast/joy-caption-pre-alpha) API 地址

### 完整 .env 示例

```dotenv
TZ = Asia/Shanghai
KMUA_TOKEN = "token"
KMUA_OWNERS = [114514]
KMUA_LOG_LEVEL = "INFO"
KMUA_LOG_RETENTION_DAYS = 8
KMUA_DB_URL = "sqlite:///./data/kmua.db"
KMUA_DROP_PENDING_UPDATES = false
KMUA_WEBHOOK = false
KMUA_URL_PATH = "/webhook"
KMUA_KEY = "/kmua/.secrets/key.pem"
KMUA_CERT = "/kmua/.secrets/cert.pem"
KMUA_LISTEN = "127.0.0.1"
KMUA_PORT = 39391
KMUA_SECRET_TOKEN = "qwqowo"
KMUA_WEBHOOK_URL = "https://kmua.kmua.com"
KMUA_MAX_DB_SIZE=200
KMUA_BASE_URL = "https://api.telegram.org/bot"
KMUA_BASE_FILE_URL = "https://api.telegram.org/file/bot"
KMUA_MANYACG_API = "http://1.0.1.0:39120"
KMUA_BILILINK_CONVERT_API = "http://1.2.3.4:39080"
KMUA_VERTEX_SYSTEM= "你是一只名字叫kmua的可爱的猫娘."
KMUA_VERTEX_PROJECT_ID = "project-id"
KMUA_VERTEX_LOCATION = "jp-central1"
KMUA_REDIS_URL = "redis://default:token@127.0.0.1:6379/"
KMUA_VERTEX_MODEL = "gemini-1.0-pro"
GOOGLE_APPLICATION_CREDENTIALS=/kmua/.secrets/.secret.json
KMUA_VERTEX_PRESET = ["你好","喵~ 您好呀~ 今天天气真好呢~"]
KMUA_MEILISEARCH_API = "localhost:7700"
KMUA_MEILISEARCH_KEY = "112233"
KMUA_MEILISEARCH_NEW_INDEX = true
KMUA_NSFWJS_API = "http://nsfwjs.api.com"
KMUA_NSFWJS_TOKEN = "token"
KMUA_SUPER_RESOLUTION_API = "http://sr.api.com"
KMUA_SUPER_RESOLUTION_TOKEN = "token"
KMUA_JOY_CAPTION_API = "https://fancyfeast-joy-caption-pre-alpha.hf.space"
```

## 源码运行

Python 版本: 3.12+, 在系统中需安装 `graphviz` 用于绘制关系图.

1. git clone https://github.com/krau/kmua-bot.git
2. 修改 `settings.toml` (参考上述环境变量设置, 但不需要 `KMUA_` 前缀, 且不必大写)
3. 使用你喜欢的工具创建虚拟环境 (建议 Poetry or venv), 安装依赖
4. `python -m kmua`