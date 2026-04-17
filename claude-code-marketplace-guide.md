# Claude Code GitHub Marketplace 开发完整指南

这份指南面向想把 Claude Code 的扩展做成 GitHub marketplace 的开发者，重点覆盖两类常见插件：**skill 型插件**和 **command 型插件**。[cite:1][cite:2]
Claude Code 官方文档把扩展方式分成两种：独立配置放在 `.claude/` 目录，适合个人或单项目；插件放在带有 `.claude-plugin/plugin.json` 的目录，适合共享、版本化和通过 marketplace 分发。[cite:1]

## 1. 先理解三层结构

最重要的概念是：**一个 GitHub 仓库可以作为一个 marketplace，marketplace 里可以列出多个 plugin，而每个 plugin 里又可以包含 skills、commands、agents、hooks 等组件。**[cite:1][cite:2]
也就是说，用户不是“安装一个仓库”，而是先添加 marketplace，再从里面安装某个具体 plugin。[cite:2]

对应关系如下：

- GitHub 仓库 = marketplace 宿主仓库。[cite:2]
- `.claude-plugin/marketplace.json` = 这个仓库的插件目录清单。[cite:2]
- 一个 `plugins/<plugin-name>/` 目录 = 一个可安装 plugin。[cite:2]
- plugin 内可以放 `skills/`、`commands/`、`agents/`、`hooks/` 等内容。[cite:1]

## 2. 什么时候用独立配置，什么时候做插件

官方文档建议：如果只是个人工作流、项目私有定制、快速试验，使用 `.claude/` 独立配置更合适；如果要共享给团队、跨项目复用、版本化发布或放到 marketplace 里，就应该做成 plugin。[cite:1]
另外，独立 skill 的调用名通常更短，比如 `/hello`；而 plugin 内 skill 会自动带命名空间，例如 `/my-plugin:hello`，这样可以避免不同插件之间重名冲突。[cite:1]

## 3. 两种常见插件类型

这份指南重点讲两种最常见的 plugin 组织方式：

| 类型 | 目录 | 典型用途 | 调用方式 |
|---|---|---|---|
| Skill 型插件 | `skills/<skill-name>/SKILL.md` | 让 Claude 按任务上下文自动或半自动使用能力，例如代码审查、文档生成、发布检查。[cite:1] | 通常是 `/plugin-name:skill-name`。[cite:1] |
| Command 型插件 | `commands/*.md` 或 `commands/<dir>/*.md` | 显式命令式工作流，例如 `/deploy`、`/release-note`、`/jira-sync` 这类用户主动触发的命令。[cite:1] | 通过命令文件定义的 slash command 暴露。[cite:1] |

官方插件结构概览中明确列出了 `commands/` 和 `skills/` 两类目录，并说明 `commands/` 用于 Markdown 文件形式的 commands，`skills/` 用于带 `SKILL.md` 的 Agent Skills。[cite:1]

## 4. 一个完整 marketplace 的目录结构

下面是一个同时包含 skill 型插件和 command 型插件的完整示例：

```text
claude-marketplace/
├── .claude-plugin/
│   └── marketplace.json
└── plugins/
    ├── quality-review-plugin/
    │   ├── .claude-plugin/
    │   │   └── plugin.json
    │   └── skills/
    │       └── quality-review/
    │           └── SKILL.md
    └── release-tools-plugin/
        ├── .claude-plugin/
        │   └── plugin.json
        └── commands/
            ├── release-note.md
            └── changelog.md
```

这个结构符合官方文档：marketplace 根目录下放 `.claude-plugin/marketplace.json`，每个 plugin 自己有 `.claude-plugin/plugin.json`，然后再放自己的 `skills/` 或 `commands/`。[cite:1][cite:2]

## 5. 先做一个 skill 型插件

### 5.1 创建目录

```bash
mkdir -p claude-marketplace/plugins/quality-review-plugin/.claude-plugin
mkdir -p claude-marketplace/plugins/quality-review-plugin/skills/quality-review
```

每个 plugin 都应位于独立目录中，并带有 `.claude-plugin/plugin.json` 清单文件。[cite:1]
每个 skill 则必须放在 `skills/<skill-folder>/SKILL.md` 中，文件夹名会成为 skill 名称的一部分。[cite:1]

### 5.2 创建 plugin.json

文件路径：

```text
claude-marketplace/plugins/quality-review-plugin/.claude-plugin/plugin.json
```

内容示例：

```json
{
  "name": "quality-review-plugin",
  "description": "Adds a code review skill for Claude Code",
  "version": "1.0.0",
  "author": {
    "name": "Your Name"
  }
}
```

官方文档说明，`name` 是插件唯一标识符，也会成为 skill 命名空间前缀；`description` 用于在插件管理器中显示；`version` 建议使用语义化版本；`author` 可选但推荐填写。[cite:1]
因此这个插件中的 skill 最终会以 `/quality-review-plugin:...` 的形式暴露，而不是裸名字。[cite:1]

### 5.3 创建 SKILL.md

文件路径：

```text
claude-marketplace/plugins/quality-review-plugin/skills/quality-review/SKILL.md
```

内容示例：

```md
---
name: quality-review
description: Review code for bugs, security, and performance. Use when reviewing diffs, PRs, or recent code changes.
---

When reviewing code, check for:
1. Potential bugs and edge cases
2. Security concerns
3. Performance issues
4. Readability and maintainability

Be concise and actionable.
```

官方文档给出的 plugin 技能示例表明，`SKILL.md` 需要包含 frontmatter，并至少提供 `name` 和 `description`，后面再写指令正文。[cite:1]
其中 `description` 非常关键，因为 Claude 会根据任务上下文判断是否使用该 skill。[cite:1]

### 5.4 如何传参数

官方文档还支持在 skill 中使用 `$ARGUMENTS` 捕获用户在命令后输入的文本，例如：

```md
---
name: hello
description: Greet the user with a personalized message
---

Greet the user named "$ARGUMENTS" warmly and ask how you can help them today.
```

这样用户执行 `/my-first-plugin:hello Alex` 时，`$ARGUMENTS` 就会变成 `Alex`。[cite:1]
如果你要做带参数的 code review、生成模板、发布检查等技能，这个模式很常用。[cite:1]

## 6. 再做一个 command 型插件

### 6.1 为什么需要 command 型插件

官方文档在插件结构概览里把 `commands/` 单独列为一类组件，并说明它是“作为 Markdown 文件的 Skills/命令”。[cite:1]
实践上可以把它理解为：skill 更偏“让 Claude 根据上下文使用能力”，而 command 更偏“用户明确执行某个 slash command 工作流”。[cite:1]

### 6.2 创建目录

```bash
mkdir -p claude-marketplace/plugins/release-tools-plugin/.claude-plugin
mkdir -p claude-marketplace/plugins/release-tools-plugin/commands
```

### 6.3 创建 plugin.json

文件路径：

```text
claude-marketplace/plugins/release-tools-plugin/.claude-plugin/plugin.json
```

内容示例：

```json
{
  "name": "release-tools-plugin",
  "description": "Release workflow commands for changelogs and release notes",
  "version": "1.0.0",
  "author": {
    "name": "Your Name"
  }
}
```

这个清单字段的含义与 skill 型插件完全相同，依然由 `name`、`description`、`version` 等元数据定义插件身份。[cite:1]

### 6.4 创建 command 文件

文件路径：

```text
claude-marketplace/plugins/release-tools-plugin/commands/release-note.md
```

内容示例：

```md
---
description: Generate release notes from recent changes, merged PRs, or selected diffs
---

Generate release notes based on the current repository changes.

Requirements:
- Group changes by feature, fix, docs, and breaking change.
- Write in concise product-facing language.
- Call out any migration or rollout risk.
- End with a short internal QA checklist.

If the user passed arguments, treat them as release context: $ARGUMENTS
```

官方文档虽然在当前页面没有像 `SKILL.md` 一样展开完整 command 样例，但它明确指出 `commands/` 可以通过自定义路径暴露命令文件，而且这些文件属于插件组件的一部分，可被验证和分发。[cite:1][cite:2]
因此最稳妥的实践是：把 command 当成一个带 frontmatter 的 Markdown 指令文件来组织，并放在 `commands/` 目录里统一管理。[cite:1][cite:2]

另一个例子：

文件路径：

```text
claude-marketplace/plugins/release-tools-plugin/commands/changelog.md
```

```md
---
description: Draft a changelog entry for the current work
---

Draft a changelog entry using Keep a Changelog style.

Requirements:
- Include Added, Changed, Fixed, Removed when relevant.
- Keep each bullet short.
- Do not invent version numbers unless the user supplies one.
- If arguments are present, treat them as the target version or release window: $ARGUMENTS
```

## 7. 把两个 plugin 挂到同一个 marketplace

### 7.1 创建 marketplace.json

文件路径：

```text
claude-marketplace/.claude-plugin/marketplace.json
```

内容示例：

```json
{
  "name": "my-plugins",
  "owner": {
    "name": "Your Name",
    "email": "you@example.com"
  },
  "plugins": [
    {
      "name": "quality-review-plugin",
      "source": "./plugins/quality-review-plugin",
      "description": "Adds a code review skill for Claude Code",
      "version": "1.0.0"
    },
    {
      "name": "release-tools-plugin",
      "source": "./plugins/release-tools-plugin",
      "description": "Release workflow commands for changelog and release notes",
      "version": "1.0.0"
    }
  ]
}
```

官方文档规定，marketplace 文件必须放在仓库根目录的 `.claude-plugin/marketplace.json`，且至少要有 `name`、`owner` 和 `plugins` 三个字段。[cite:2]
每个 plugin 条目至少需要 `name` 和 `source`，而 `source` 可以是相对路径、GitHub 仓库、git URL、git 子目录或 npm 包。[cite:2]

### 7.2 相对路径的含义

当你在 `marketplace.json` 里写：

```json
{
  "name": "quality-review-plugin",
  "source": "./plugins/quality-review-plugin"
}
```

这个路径是**相对于 marketplace 根目录**解析的，而不是相对于 `.claude-plugin/` 目录解析的。[cite:2]
官方还特别说明不要使用 `../` 跳出 marketplace 根目录，否则验证会报错。[cite:2]

## 8. 本地测试完整 workflow

官方推荐开发时使用 `--plugin-dir` 测单个 plugin，或者用本地 marketplace 方式测试完整安装流。[cite:1][cite:2]
两种方式建议都掌握。

### 8.1 测单个 plugin

```bash
claude --plugin-dir ./claude-marketplace/plugins/quality-review-plugin
```

这会直接加载本地插件目录，无需先安装，适合快速开发 skill 或 command。[cite:1]
修改后可以运行 `/reload-plugins` 热重载，而不用重启整个会话。[cite:1]

### 8.2 测整个 marketplace

```bash
/plugin marketplace add ./claude-marketplace
/plugin install quality-review-plugin@my-plugins
/plugin install release-tools-plugin@my-plugins
```

官方文档提供了同样的本地 marketplace 测试流程：先添加 marketplace，再安装其中某个 plugin。[cite:2]
这也是最接近实际用户体验的验证方式。[cite:2]

### 8.3 验证配置

```bash
claude plugin validate .
```

或者在 Claude Code 里：

```bash
/plugin validate .
```

官方说明这个验证器会检查 `marketplace.json`、`plugin.json`、skill 或 command frontmatter，以及 `hooks.json` 等文件的语法和架构问题。[cite:2]
在对外发布之前，最好每次都跑一遍。[cite:2]

## 9. 发布到 GitHub 作为 marketplace

### 9.1 推送仓库

推荐把整个 marketplace 仓库推到 GitHub，因为官方文档把 GitHub 作为最简单的分发方式。[cite:2]
在 GitHub 托管后，用户可以通过 `/plugin marketplace add owner/repo` 直接添加你的 marketplace。[cite:2]

### 9.2 用户安装方式

例如你的仓库是 `your-org/claude-plugins`，用户可以这样安装：

```bash
/plugin marketplace add your-org/claude-plugins
/plugin install quality-review-plugin@my-plugins
/plugin install release-tools-plugin@my-plugins
```

这说明 marketplace 名称和 GitHub 仓库名不一定相同；安装命令里的 `@my-plugins` 来自 `marketplace.json` 中的 `name` 字段。[cite:2]
plugin 名称则来自每个插件条目的 `name` 字段。[cite:2]

## 10. 两类插件的设计建议

### 10.1 Skill 型插件适合什么

适合放这些内容：

- 代码审查、测试审查、安全检查。[cite:1]
- 文档总结、PR 分析、设计评审。[cite:1]
- Claude 根据上下文自动判断应不应该调用的能力。[cite:1]

如果你的目标是“让 Claude 在遇到某类任务时自动采用某套工作方式”，优先考虑 skill。[cite:1]
官方文档也直接把 Agent Skills 描述为模型会根据任务上下文自动使用的能力。[cite:1]

### 10.2 Command 型插件适合什么

适合放这些内容：

- 明确可触发的工作流，如发布说明生成、changelog 生成、部署前检查。[cite:1]
- 团队标准化命令，如 `/jira-sync`、`/release-note`、`/sprint-summary`。[cite:1]
- 用户心里已经知道要执行哪一步，希望直接跑命令而不是让 Claude 自己判断的时候。[cite:1]

简化理解就是：**skill 偏能力，command 偏动作。**[cite:1]

## 11. 一个推荐的真实仓库结构

如果你准备长期维护，推荐这样组织：

```text
claude-plugins/
├── README.md
├── .claude-plugin/
│   └── marketplace.json
└── plugins/
    ├── quality-review-plugin/
    │   ├── README.md
    │   ├── .claude-plugin/
    │   │   └── plugin.json
    │   └── skills/
    │       ├── quality-review/
    │       │   └── SKILL.md
    │       └── security-review/
    │           └── SKILL.md
    └── release-tools-plugin/
        ├── README.md
        ├── .claude-plugin/
        │   └── plugin.json
        └── commands/
            ├── release-note.md
            ├── changelog.md
            └── post-release-check.md
```

这样做的好处是，一个 marketplace 仓库下面可以维护多个插件，而每个插件又能根据职责继续拆分出多个 skills 或 commands。[cite:1][cite:2]
这也最符合官方 marketplace 的“一个目录列出多个 plugin”的设计方式。[cite:2]

## 12. 常见坑

### 12.1 把 plugin 内容放错目录

官方文档提醒，plugin 的目录结构要放在插件根目录，而不是放进 `.claude-plugin/` 里面。[cite:1]
也就是说，`.claude-plugin/` 只放 `plugin.json` 或 marketplace 清单，不要把 `skills/`、`commands/` 这些目录塞进去。[cite:1][cite:2]

### 12.2 相对路径失效

如果用户是通过直接 URL 添加 `marketplace.json`，那么 `"./plugins/..."` 这种相对路径可能无法解析；官方建议这种情况下改用 GitHub、npm 或 git URL 源。[cite:2]
所以如果你计划公开分发，优先让用户通过 GitHub 仓库添加 marketplace，而不是只给一个裸 `marketplace.json` 地址。[cite:2]

### 12.3 安装后找不到共享文件

官方文档强调，plugin 安装时会被复制到 `~/.claude/plugins/cache`，因此 plugin 不应依赖 `../shared-utils` 这类目录外文件，因为这些文件不会被一起复制。[cite:2]
如果多个 plugin 需要共享内容，官方建议考虑使用符号链接或重新组织目录结构。[cite:2]

### 12.4 名称冲突和命名方式

marketplace 名称和 plugin 名称都推荐使用 kebab-case，skill 名称也应尽量简洁清晰。[cite:2]
而 plugin 内的 skill 会自动带命名空间，这本身就是官方提供的防冲突机制。[cite:1]

## 13. 一套从零到发布的最短路径

下面是一套最实用的流程：

1. 先在本地做一个单独 plugin，用 `--plugin-dir` 调通 skill 或 command。[cite:1]
2. 再建立 `marketplace.json`，把一个或多个 plugin 挂进去。[cite:2]
3. 用 `/plugin marketplace add ./本地目录` 和 `/plugin install ...` 测完整流程。[cite:2]
4. 跑 `claude plugin validate .` 做验证。[cite:2]
5. 推送到 GitHub，让用户用 `/plugin marketplace add owner/repo` 安装。[cite:2]

## 14. 最小可用模板汇总

### 14.1 skill 型 plugin 最小模板

```text
my-skill-plugin/
├── .claude-plugin/
│   └── plugin.json
└── skills/
    └── hello/
        └── SKILL.md
```

`plugin.json`：

```json
{
  "name": "my-skill-plugin",
  "description": "A simple skill plugin",
  "version": "1.0.0"
}
```

`SKILL.md`：

```md
---
name: hello
description: Say hello to the user
---

Say hello to the user in a friendly and concise way.
```

### 14.2 command 型 plugin 最小模板

```text
my-command-plugin/
├── .claude-plugin/
│   └── plugin.json
└── commands/
    └── summarize.md
```

`plugin.json`：

```json
{
  "name": "my-command-plugin",
  "description": "A simple command plugin",
  "version": "1.0.0"
}
```

`summarize.md`：

```md
---
description: Summarize selected content or recent changes
---

Summarize the selected content or recent changes.
- Keep it concise.
- Highlight important decisions.
- End with next steps.
- Use $ARGUMENTS when provided as context.
```

### 14.3 marketplace 最小模板

```text
my-marketplace/
├── .claude-plugin/
│   └── marketplace.json
└── plugins/
    ├── my-skill-plugin/
    └── my-command-plugin/
```

`marketplace.json`：

```json
{
  "name": "my-plugins",
  "owner": {
    "name": "Your Name"
  },
  "plugins": [
    {
      "name": "my-skill-plugin",
      "source": "./plugins/my-skill-plugin"
    },
    {
      "name": "my-command-plugin",
      "source": "./plugins/my-command-plugin"
    }
  ]
}
```

## 15. 命令速查

```bash
# 测单个 plugin
claude --plugin-dir ./my-plugin

# 重载本地改动
/reload-plugins

# 添加本地 marketplace
/plugin marketplace add ./my-marketplace

# 从 marketplace 安装 plugin
/plugin install my-skill-plugin@my-plugins
/plugin install my-command-plugin@my-plugins

# 校验当前目录的 plugin 或 marketplace
claude plugin validate .
/plugin validate .

# 添加 GitHub marketplace
/plugin marketplace add your-org/claude-plugins
```

## 16. 最后怎么选

如果你想做的是“让 Claude 在某类任务里自动采用某个能力包”，做 **skill 型插件** 更合适。[cite:1]
如果你想做的是“给团队一个明确的 slash command 工作流入口”，做 **command 型插件** 更合适；而最常见的真实项目，其实会在同一个 marketplace 里同时维护这两类插件。[cite:1][cite:2]
