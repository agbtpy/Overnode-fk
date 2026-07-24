环境变量（运行前必须配置）
必要变量
变量名
格式
说明
DISCORD_TOKEN
字符串
Discord 用户 Token，用于 OAuth 授权登录
非必要变量
变量名
格式
说明
默认值
TG_BOT
TG_CHAT_ID,TG_TOKEN
Telegram 推送配置，用逗号分隔：聊天 ID 和 Bot Token
空（不推送）
CRON_JOB
API_KEY,JOB_ID
cron-job.org 调度配置，用逗号分隔：API Key 和 Job ID
空（不写回调度）
GOST_PROXY
任意非空字符串
启用 HTTP/HTTPS 代理（指向 127.0.0.1:8080）
空（不使用代理）
内置常量（代码中硬编码，无需配置）
变量名
值
说明
DISCORD_API
"https://discord.com/api/v9"
Discord API 地址
CLIENT_ID
"972921155205877860"
Discord OAuth 客户端 ID
REDIRECT_URI
"https://console.overnode.fr/auth/discord/callback"
OAuth 回调地址
GUILD_ID
"1515897528011329657"
Discord 服务器 ID
SITE_URL
"https://console.overnode.fr"
目标网站地址
UA
User-Agent 字符串
浏览器标识
SERVERS
列表
要续期的服务器列表（含 name、id、code）
自动更新变量（脚本自修改）
变量名
格式
说明
LAST_RENEWED_US
"YYYY-MM-DD HH:MM:SS"
美国服务器上次续期时间（北京时间）
LAST_RENEWED_FR
"YYYY-MM-DD HH:MM:SS"
法国服务器上次续期时间（北京时间）
⚠️ 这两个变量由脚本自动更新，无需手动配置。


Overnode 免费服务器自动续期脚本，通过 Discord OAuth 登录面板并触发续期，支持 Telegram 通知与 cron-job.org 自动调度。

## 🔧 环境变量

| 变量 | 说明 | 示例 |
|---|---|---|
| 🔑 `DISCORD_TOKEN` | Discord 账号 Token（用于授权登录面板） | `MTM1...` |
| 📨 `TG_BOT` | Telegram 推送配置，格式 `chat_id,bot_token` | `123456,789:ABC...` |
| 🔁 `CRON_JOB` | cron-job.org 写回配置，格式 `api_key,job_id` | `cjk_xxx,123456` |
| 🛡️ `GOST_PROXY` | 可选，GOST 代理转发地址（用于切换出口IP） | `socks5://1.2.3.4:1080` |
| 🔐 `PRIVATE_REPO_TOKEN` | GitHub Token，用于拉取私库脚本及推回更新 | `ghp_xxx` |

## 🚀 运行

```bash
pip install requests
python over_renew.py
```

## ⚙️ 工作流程

1. 🌐 验证出口 IP
2. 🔑 Discord OAuth 登录获取 Session
3. 🔄 依次对 `SERVERS` 列表中的服务器执行续期
4. 📅 计算下次续期时间，就近写回 cron-job.org（两台相差 ≤5分钟则一并续期，否则分批触发）
5. 💾 续期成功的服务器自动更新脚本内 `LAST_RENEWED_XX` 时间戳
6. 📨 推送续期结果到 Telegram

## 📊 续期结果

- ✅ 续期成功
- ⌛️ 期限未至
- ❌ 续期失败

## 🖥 服务器配置

在 `SERVERS` 列表中增删服务器，`code` 字段需包含 `US` 或 `FR` 字样以匹配对应的 `LAST_RENEWED_XX` 字段。

```python
SERVERS = [
    {"name": "SERVERS1", "id": "12345678", "code": "Over-US 🇺🇸"},
    {"name": "SERVERS2",  "id": "12345678", "code": "Over-FR 🇫🇷"},
]
```
