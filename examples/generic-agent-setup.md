# 在任意 AI Agent 中使用 Day 1 Health API

没有为你的 Agent 写专门指南？没关系。Day 1 Health API 本质上就是
**一个带 Bearer token 的 HTTPS GET 端点**——任何能发起 HTTP 请求、能读环境变量的
Agent（或脚本、自动化流程）都能用。

## 核心概念（30 秒看懂）

1. 你有一个 API Key，形如 `d1_sk_xxx`，在 [Developer Portal](https://othersstudio.tech/me/developer) 生成。
2. 把它放进请求头：`Authorization: Bearer d1_sk_xxx`
3. 对端点发 GET 请求，拿回 JSON：`{ status, data, meta }`

就这么简单。下面是所有端点：

| 端点 | 说明 | 主要参数 |
|------|------|---------|
| `/functions/v1/health-snapshot` | 全维度总览（最常用） | `days` (1-30, 默认7) |
| `/functions/v1/workouts` | 逐次训练 + 逐动作组数/重量/次数 | `days` (1-90), `limit` (1-50) |
| `/functions/v1/nutrition` | 逐日营养 + 目标对比 | `days` (1-60) |
| `/functions/v1/body` | 体重/体脂时间序列 | `days` (1-365) |
| `/functions/v1/recovery` | 每日状态/签到/训练前后回顾 | `days` (1-60) |
| `/functions/v1/insights` | 频率/容量趋势/streak | 无 |

基址：`https://ywliqhbjyiydlnahvwal.supabase.co`

## 接入方式

### 1. 把 Skill 说明喂给你的 Agent

大多数 Agent 支持加载一段长指令（system prompt / 规则文件 / 知识库）。把这个
raw 链接的内容提供给它即可：

```
https://raw.githubusercontent.com/wei666-china/day1-health-skill/main/SKILL.md
```

SKILL.md 里写明了端点、参数、如何解读数据、以及安全规则，Agent 读完就会用。

### 2. 设置 API Key

```bash
export DAY1_API_KEY="d1_sk_your_key_here"
```

### 3. 直接测试（任何语言/工具都同理）

```bash
curl -s "https://ywliqhbjyiydlnahvwal.supabase.co/functions/v1/health-snapshot?days=7" \
  -H "Authorization: Bearer $DAY1_API_KEY"
```

Python 示例：

```python
import os, requests

r = requests.get(
    "https://ywliqhbjyiydlnahvwal.supabase.co/functions/v1/health-snapshot",
    params={"days": 7},
    headers={"Authorization": f"Bearer {os.environ['DAY1_API_KEY']}"},
)
print(r.json())
```

## 响应结构

```json
{
  "status": "ok",
  "data": { "...": "因端点而异" },
  "meta": { "requests_remaining": 994 }
}
```

错误时 `status` 为 `"error"`，并带 `error` 字段（如 401 鉴权失败、429 超额）。

## 安全提醒

- API Key 等同密码：不要提交到 git、不要分享、不要让 Agent 在回复里打印它。
- 只在向官方端点请求的 `Authorization` 头里使用 Key，别放进 URL 或日志。
- 怀疑泄露：去 [Developer Portal](https://othersstudio.tech/me/developer) 立即吊销重建。

## 前提

本 API 仅服务 Day 1 App 用户。你必须先在 Day 1 iOS App 用 Apple/Google 登录、
记录数据，并用同一账号在 Developer Portal 生成 Key。没有数据时 API 返回空结果。
