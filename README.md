# Day 1 Health Skill

让你的 AI Agent 访问你在 Day 1 健身 App 中追踪的真实健康数据。

> ⚠️ **使用前提：你必须是 Day 1 App 的用户。**
>
> 这个 API 读取的是**你自己在 Day 1 App 里记录并同步到云端的数据**。
> 所以使用流程是：
> 1. 先下载 Day 1App，用 Apple / Google 账号登录并记录你的训练、饮食、身体等数据；
> 2. 用**同一个账号**登录 [Developer Portal](https://othersstudio.tech/me/developer) 申请 API Key；
> 3. 把 Key 配置给你的 AI Agent。
>
> 如果你不是 Day 1 用户、或账号里没有数据，即使下载了这个 Skill，API 也只会返回空数据。

## 这是什么？

这是一个 **AI Agent Skill 文件** — 一份教 AI 如何使用你的个人健康 API 的说明书。

把它放进你的 AI Agent 目录后，Agent 就能：
- 查看你的训练记录和负荷趋势
- 分析你的饮食摄入和蛋白质达标率
- 判断你今天应该训练还是休息
- 基于真实数据给出个性化建议

## 快速开始

### 1. 获取 API Key

用你在 **Day 1 App 中登录的同一个 Apple / Google 账号**，登录 [Day 1 Developer Portal](https://othersstudio.tech/me/developer) 并生成你的个人 API Key。

> 必须是同一个账号，Key 才能读到你在 App 里的数据。

### 2. 安装 Skill

**Cursor IDE:**
```bash
cp SKILL.md ~/.cursor/skills/day1-health/SKILL.md
```

**Claude Code:**
```bash
cp SKILL.md ~/.claude/skills/day1-health/SKILL.md
```

### 3. 设置 API Key

在你的环境变量中添加：
```bash
export DAY1_API_KEY="d1_sk_your_key_here"
```

### 4. 开始使用

直接问你的 AI Agent：
- "我这周训练了几次？"
- "我的蛋白质摄入够吗？"
- "根据我的恢复状态，今天应该练什么？"
- "分析一下我最近的睡眠质量"

## 支持的 AI Agent 平台

每个平台都有专门的分步接入指南，点击查看：

| 平台 | 接入方式 | 指南 |
|------|---------|------|
| Cursor IDE | `.cursor/skills/` + 环境变量 | [cursor-setup.md](examples/cursor-setup.md) |
| Claude Code | `.claude/skills/` 或 `CLAUDE.md` | [claude-code-setup.md](examples/claude-code-setup.md) |
| Codex CLI | `AGENTS.md` + 环境变量 | [codex-setup.md](examples/codex-setup.md) |
| OpenClaw | `openclaw skills install git:...` | [openclaw-setup.md](examples/openclaw-setup.md) |
| 其他任意 Agent | 带 Bearer token 的 HTTP GET | [generic-agent-setup.md](examples/generic-agent-setup.md) |

## API 端点

| 端点 | 说明 | 参数 |
|------|------|------|
| `GET /health-snapshot` | 全维度总览（训练/营养/身体/恢复/睡眠/HRV） | `days` (1-30) |
| `GET /workouts` | 逐次训练 + 逐动作组数/重量/次数 | `days` (1-90), `limit` (1-50) |
| `GET /nutrition` | 逐日营养 + 目标对比 + 达标率 | `days` (1-60) |
| `GET /body` | 体重/体脂时间序列 + 目标计划 | `days` (1-365) |
| `GET /recovery` | 每日状态/签到/训练前后回顾 | `days` (1-60) |
| `GET /insights` | 训练频率/容量趋势/连续训练周 streak | 无 |

完整参数、响应结构与解读说明见 [SKILL.md](SKILL.md)。

## 数据来源

所有数据来自你在 Day 1 iOS App 中的追踪：
- 训练计划和记录
- 饮食和营养
- 体重/体脂
- Apple Watch（睡眠、心率、HRV、活动量）
- 每日恢复自评

## 隐私

- API Key 只能访问**你自己的**数据
- 敏感数据（月经周期等）不会通过 API 暴露
- 你随时可以在 Developer Portal 吊销 Key
- 数据存储在 Supabase（AWS 新加坡区域）

## License

MIT
