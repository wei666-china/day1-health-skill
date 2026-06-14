# Day 1 Health Skill

让你的 AI Agent 访问你在 Day 1 健身 App 中追踪的真实健康数据。

## 这是什么？

这是一个 **AI Agent Skill 文件** — 一份教 AI 如何使用你的个人健康 API 的说明书。

把它放进你的 AI Agent 目录后，Agent 就能：
- 查看你的训练记录和负荷趋势
- 分析你的饮食摄入和蛋白质达标率
- 判断你今天应该训练还是休息
- 基于真实数据给出个性化建议

## 快速开始

### 1. 获取 API Key

登录 [Day 1 Developer Portal](https://othersstudio.tech/me/developer) 并生成你的个人 API Key。

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

| 平台 | Skill 目录 |
|------|-----------|
| Cursor IDE | `~/.cursor/skills/day1-health/` |
| Claude Code | `~/.claude/skills/day1-health/` |
| Codex CLI | `~/.codex/skills/day1-health/` |
| 自定义 Agent | 将 SKILL.md 内容加入 system prompt |

## API 端点

| 端点 | 说明 |
|------|------|
| `GET /health-snapshot?days=7` | 完整健康快照（训练/营养/身体/恢复/睡眠/HRV） |

更多端点即将推出。

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
