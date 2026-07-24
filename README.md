# yangyu-strava-api 🚴

> A Hermes Agent skill for Strava API integration — OAuth token management, activity fetching, athlete stats, and cycling data analysis.

[中文版](#中文版)

---

## English

### Features

- **🔑 Auto Token Refresh** — Strava OAuth tokens expire every 6 hours. This skill handles auto-refresh seamlessly.
- **📊 Activity Fetching** — Pull recent rides, runs, and other activities with full data (distance, time, heart rate, power, elevation).
- **🏆 Athlete Stats** — Get your YTD and all-time totals.
- **🚴 Cycling Analysis Guide** — Heart rate zones, power zones, cadence, and intensity comparison templates.

### Installation

```bash
# Clone into your Hermes skills directory
git clone https://github.com/techysy/yangyu-strava-skill.git ~/.hermes/skills/social-media/yangyu-strava-api
```

### Setup — Step by Step

#### 1. Create a Strava API Application

Go to https://www.strava.com/settings/api and register a new application.

You'll get:
- **Client ID** — e.g. `123456`
- **Client Secret** — e.g. `a1b2c3d4e5f6...`

> ⚠️ **Keep your Client Secret private.** Never commit it to public repositories.

#### 2. Configure Credentials

Create `scripts/strava_credentials.py` from the template:

```python
"""Strava API credentials - auto-refresh token store"""
import json, urllib.request, urllib.parse, os

CONFIG = {
    "client_id": "YOUR_CLIENT_ID",       # replace with your client ID
    "client_secret": "YOUR_CLIENT_SECRET",  # replace with your client secret
    "athlete_id": "YOUR_ATHLETE_ID",     # replace with your athlete ID
}
# ... rest of the code stays the same
```

> This file is already in `.gitignore` — it will not be committed.

#### 3. Authorize OAuth

Open this URL in your browser:

```
https://www.strava.com/oauth/authorize?client_id=YOUR_CLIENT_ID&response_type=code&redirect_uri=http://localhost&approval_prompt=force&scope=read,activity:read_all
```

After authorizing, you'll be redirected to `http://localhost/?state=&code=XXXXXXXX...`. Copy the `code` value.

#### 4. Exchange Code for Tokens

Run the credential setup:

```bash
# One-time setup
python3 scripts/setup_credentials.py
# (Or manually exchange the code via curl/Python)
```

The skill will auto-refresh tokens after this. Tokens are stored in `references/strava_tokens.json` (also gitignored).

#### 5. Verify It Works

```python
from scripts.strava_credentials import get_recent_activities

activities = get_recent_activities(per_page=5)
for a in activities:
    print(f"{a['name']} — {a['distance']/1000:.1f}km / {a['moving_time']//60}min")
```

### Usage

```python
from scripts.strava_credentials import get_recent_activities, get_athlete_stats

# Auto-refreshes token if needed
activities = get_recent_activities(per_page=5)
for a in activities:
    print(f"{a['name']} — {a['distance']/1000:.1f}km / {a['moving_time']//60}min")

# Get athlete totals
stats = get_athlete_stats()
rt = stats['all_ride_totals']
print(f"Total: {rt['distance']/1000:.0f}km / {rt['count']} rides")
```

Or load in Hermes Agent:

```bash
hermes -s yangyu-strava-api
```

### Security Notes

- **All credential files are gitignored** — `scripts/strava_credentials.py`, `scripts/strava_creds.py`, and `references/strava_tokens.json` will not be committed.
- **Tokens expire every 6 hours** — refresh is automatic via `strava_credentials.py`.
- **Client Secret rotation** — If you suspect your secret is compromised, reset it at https://www.strava.com/settings/api.

### Requirements

- Python 3.8+
- Strava API Application (free, sign up at https://www.strava.com/settings/api)
- Hermes Agent (optional — the scripts work standalone)

---

## 中文版

### 功能

- **🔑 自动刷新 Token** — Strava OAuth token 每 6 小时过期，自动无缝刷新
- **📊 获取活动数据** — 拉取骑行、跑步等活动（距离、时长、心率、功率、爬升）
- **🏆 运动员统计** — 年度和总累计数据
- **🚴 骑行分析指南** — 心率区间、功率区间、踏频等分析维度

### 安装

```bash
git clone https://github.com/techysy/yangyu-strava-skill.git ~/.hermes/skills/social-media/yangyu-strava-api
```

### 配置步骤

#### 1. 创建 Strava API 应用

访问 https://www.strava.com/settings/api 注册应用。

你会得到：
- **Client ID** — 例如 `123456`
- **Client Secret** — 例如 `a1b2c3d4e5f6...`

> ⚠️ **Client Secret 是你的 API 密钥，不要提交到公开仓库。**

#### 2. 填入凭证

创建 `scripts/strava_credentials.py`（参照模板）：

```python
CONFIG = {
    "client_id": "YOUR_CLIENT_ID",         # 填你的 Client ID
    "client_secret": "YOUR_CLIENT_SECRET",  # 填你的 Client Secret
    "athlete_id": "YOUR_ATHLETE_ID",       # 填你的 Athlete ID
}
```

> 此文件已在 `.gitignore` 中，不会提交到仓库。

#### 3. OAuth 授权

在浏览器中打开：

```
https://www.strava.com/oauth/authorize?client_id=你的CLIENT_ID&response_type=code&redirect_uri=http://localhost&approval_prompt=force&scope=read,activity:read_all
```

授权后浏览器跳转到 `http://localhost/?state=&code=XXXXXXXX...`，复制 `code` 值。

#### 4. 用 Code 换取 Access Token

```bash
python3 scripts/setup_credentials.py
```

或者手动用 curl 交换 token。之后脚本会自动刷新，token 存储在 `references/strava_tokens.json`（已 gitignore）。

#### 5. 验证

```python
from scripts.strava_credentials import get_recent_activities
activities = get_recent_activities(per_page=5)
for a in activities:
    print(f"{a['name']} — {a['distance']/1000:.1f}km")
```

### 使用

```python
from scripts.strava_credentials import get_recent_activities, get_athlete_stats

# 自动刷新 token
activities = get_recent_activities(per_page=5)
for a in activities:
    print(f"{a['name']} — {a['distance']/1000:.1f}km / {a['moving_time']//60}min")

# 获取累计统计
stats = get_athlete_stats()
rt = stats['all_ride_totals']
print(f"总计: {rt['distance']/1000:.0f}km / {rt['count']} 次骑行")
```

在 Hermes Agent 中加载：

```bash
hermes -s yangyu-strava-api
```

### 安全说明

- **凭证文件已全部 gitignore** — `scripts/strava_credentials.py`、`scripts/strava_creds.py`、`references/strava_tokens.json` 不会被提交
- **Token 每 6 小时过期** — `strava_credentials.py` 自动刷新
- **Client Secret 泄露?** — 立即去 https://www.strava.com/settings/api 重置

### 依赖

- Python 3.8+
- Strava API 应用（免费，注册于 https://www.strava.com/settings/api）
- Hermes Agent（可选——脚本可独立运行）

---

## License

MIT
