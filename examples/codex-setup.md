# 在 OpenAI Codex CLI 中使用 Day 1 Health Skill

Codex CLI 通过项目根目录的 `AGENTS.md` 来加载长期指令。把 Day 1 Skill 的内容
（或一个指向它的引用）放进 `AGENTS.md`，Codex 就会在相关问题里自动调用你的健康 API。

## 安装步骤

### 1. 准备 Skill 内容

下载 SKILL.md：

```bash
curl -o day1-health.md \
  https://raw.githubusercontent.com/wei666-china/day1-health-skill/main/SKILL.md
```

### 2. 接入 AGENTS.md

Codex 会读取当前项目（或 `~/.codex/AGENTS.md` 全局）里的 `AGENTS.md`。在其中加入一段引用：

```markdown
## Day 1 Health API

When the user asks about their fitness, training, nutrition, body, recovery,
sleep, HRV, or readiness, use the Day 1 Health API. Full instructions:
@day1-health.md

Auth: read the key from the DAY1_API_KEY environment variable. Never print it.
```

> `@day1-health.md` 是把同目录的文件内容引入上下文。你也可以直接把整份
> SKILL.md 的正文粘进 `AGENTS.md`。

### 3. 设置 API Key

在 shell 配置文件（`~/.zshrc` 或 `~/.bashrc`）中加入：

```bash
export DAY1_API_KEY="d1_sk_your_key_here"
```

然后 `source ~/.zshrc` 或重启终端。

> 安全提醒：API Key 等同于密码。
> - 不要把含 Key 的文件提交到 git。
> - 不要把 `AGENTS.md` 里写死真实 Key（用环境变量引用）。
> - 怀疑泄露就去 [Developer Portal](https://othersstudio.tech/me/developer) 立即吊销重建。

### 4. 验证

在该项目目录启动 Codex：

```bash
codex
```

然后问：「查看我最近一周的 Day 1 训练数据」。Codex 应当用你的 Key 调用 API 并返回真实数据。

## 常见问题

**Q: Codex 没调用 API？**
A: 确认 `AGENTS.md` 在当前工作目录或 `~/.codex/`，且问题包含健身/健康关键词。

**Q: 401 错误？**
A: `echo $DAY1_API_KEY` 确认环境变量已设置且为当前会话可见。

**Q: 数据是空的？**
A: 确认你在 Day 1 iOS App 用同一账号记录过数据（数据会自动同步到云端）。
