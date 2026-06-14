# 在 Cursor IDE 中使用 Day 1 Health Skill

## 安装步骤

### 1. 复制 Skill 文件

```bash
mkdir -p ~/.cursor/skills/day1-health
cp SKILL.md ~/.cursor/skills/day1-health/SKILL.md
```

### 2. 设置 API Key

在你的 shell 配置文件中加入（`~/.zshrc` 或 `~/.bashrc`）：

```bash
export DAY1_API_KEY="d1_sk_your_key_here"
```

然后重启终端或执行 `source ~/.zshrc`。

### 3. 验证

在 Cursor 中开启新对话，问：

> "查看我的 Day 1 健康数据"

如果 Agent 调用了 API 并返回你的真实数据，就说明配置成功了。

## 工作原理

1. Cursor Agent 识别到你的问题和 Day 1 health 相关
2. 自动加载 SKILL.md 中的说明
3. 使用你的 API Key 调用 Day 1 API
4. 解析返回的 JSON 数据
5. 用人话给你分析和建议

## 常见问题

**Q: Agent 没有调用 API？**
A: 确认 Skill 文件在正确位置（`~/.cursor/skills/day1-health/SKILL.md`），并且问题中包含了健身/健康相关的关键词。

**Q: 返回 401 错误？**
A: 检查 `DAY1_API_KEY` 环境变量是否正确设置。在终端中运行 `echo $DAY1_API_KEY` 确认。

**Q: 数据是空的？**
A: 确认你在 Day 1 iOS App 中有记录数据，并且已登录同一账号。App 会自动同步数据到云端。
