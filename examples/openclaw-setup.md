# 在 OpenClaw 中使用 Day 1 Health Skill

OpenClaw 用 `SKILL.md` + `openclaw.json` 管理技能，并且支持直接从 Git 仓库安装。
本仓库根目录就有 `SKILL.md`，可以一行命令装好。

## 安装步骤

### 1. 从 Git 安装 Skill

```bash
openclaw skills install git:wei666-china/day1-health-skill
```

> 想让所有本地 agent 都能用，加 `--global`：
> ```bash
> openclaw skills install git:wei666-china/day1-health-skill --global
> ```
> 也可以先 `git clone` 后用 `openclaw skills install ./day1-health-skill --as day1-health` 本地安装。

### 2. 在 openclaw.json 配置 API Key

编辑 `~/.openclaw/openclaw.json`，在 `skills.entries` 下加入 Key（通过环境变量引用）：

```json5
{
  skills: {
    entries: {
      "day1-health": {
        enabled: true,
        env: { DAY1_API_KEY: "DAY1_API_KEY" },
      },
    },
  },
}
```

并在 shell 里导出真实 Key：

```bash
export DAY1_API_KEY="d1_sk_your_key_here"
```

### 3. 确认技能已加载

```bash
openclaw skills list
```

应能看到 `day1-health` 处于 enabled 状态。

### 4. 验证

向你的 OpenClaw agent 提问：「根据我的 Day 1 数据，我今天适合练吗？」
它应当调用 API 并基于你的真实恢复数据回答。

## 安全提醒

- 不要在 `openclaw.json` 里写死真实 Key，用 `env` 引用环境变量。
- 不要把含 Key 的文件提交到 git，也不要让 agent 打印 Key。
- 怀疑泄露：去 [Developer Portal](https://othersstudio.tech/me/developer) 立即吊销重建。

## 常见问题

**Q: 安装后 agent 看不到这个技能？**
A: 检查 agent allowlist（`agents.defaults.skills` / `agents.list[].skills`）是否限制了可见技能；以及 `skills.entries.day1-health.enabled` 是否为 true。

**Q: 401 错误？**
A: `echo $DAY1_API_KEY` 确认 Key 已导出，且 `openclaw.json` 的 env 名称对应正确。

**Q: 数据为空？**
A: 确认你在 Day 1 App 用同一账号记录过数据。
