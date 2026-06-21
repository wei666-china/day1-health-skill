# 在 Claude Code 中使用 Day 1 Health Skill

Claude Code 支持两种加载方式：放进 `.claude/skills/` 作为正式 Skill，或在
`CLAUDE.md` 里引用。推荐用 Skill 目录，识别更稳定。

## 方式 A：作为 Skill 安装（推荐）

### 1. 创建 Skill 目录并下载文件

```bash
mkdir -p .claude/skills/day1-health
curl -o .claude/skills/day1-health/SKILL.md \
  https://raw.githubusercontent.com/wei666-china/day1-health-skill/main/SKILL.md
```

> 想全局可用就放到 `~/.claude/skills/day1-health/SKILL.md`。

### 2. 设置 API Key

```bash
export DAY1_API_KEY="d1_sk_your_key_here"
```

写进 `~/.zshrc` / `~/.bashrc` 后 `source` 一下。

### 3. 验证

在项目里启动 Claude Code，问：「我这周恢复得怎么样？参考我的 Day 1 数据」。
Claude 会自动加载 Skill 并调用 API。

## 方式 B：通过 CLAUDE.md 引用

如果你不想用 Skill 目录，可在项目根 `CLAUDE.md` 加入：

```markdown
## Day 1 Health API

For any question about the user's fitness/nutrition/recovery, fetch real data
from the Day 1 Health API. Read the full spec from:
https://raw.githubusercontent.com/wei666-china/day1-health-skill/main/SKILL.md

Authenticate with the DAY1_API_KEY environment variable. Never print the key.
```

## 安全提醒

- API Key 等同密码，不要提交到 git、不要分享、不要让 AI 打印出来。
- `.claude/` 若纳入版本控制，确保 Key 只来自环境变量，不要写死。
- 怀疑泄露：去 [Developer Portal](https://othersstudio.tech/me/developer) 立即吊销重建。

## 常见问题

**Q: Claude 没调用 API？**
A: 确认 SKILL.md 在 `.claude/skills/day1-health/`，问题里包含健身/健康关键词。

**Q: 401 错误？**
A: `echo $DAY1_API_KEY` 确认 Key 已设置。

**Q: 数据为空？**
A: 确认你在 Day 1 App 用同一账号记录过数据。
