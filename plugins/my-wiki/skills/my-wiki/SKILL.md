---
name: my-wiki
description: "个人Wiki知识库技能。在任务执行中自动判断是否需要查询用户的个人偏好、编码规范、领域知识或历史决策；在用户显式要求或识别到高价值经验时，自动将内容写入Wiki。触发条件：用户提到模糊指代词（如'老规矩'、'我的服务器'）、涉及代码规范但未给出明确约束、出现自定义组件名或内部错误码、需要决策权衡时自动读取；用户使用'记住'、'存入wiki'等指令、纠正AI且纠正内容可复用、完成重要Debug或架构决策、环境发生永久变更时自动写入。"
---

# Personal Wiki Skill

通过 memem API 管理个人 Wiki 知识库。在任务执行过程中，根据上下文自动判断是否需要查询或写入个人知识。

## 环境变量

| 变量名 | 必需 | 说明 |
|---|---|---|
| `WIKI_API_URL` | 是 | Wiki API 基础地址，如 `http://localhost:3000` |
| `WIKI_AUTH_TOKEN` | 是 | API 认证 Token |

启动前必须确认两个环境变量已设置。若未设置，跳过所有 Wiki 操作并向用户提示配置方法。

## 一、触发读取（Read/Retrieve）的条件

在回答或执行操作之前，评估以下条件。**任一条件满足即触发 Wiki 查询**，查询结果作为后续操作的参考上下文：

### 1. 指代不明或上下文缺失（Ambiguity）

用户的 prompt 中出现模糊指代词，无法从当前对话上下文解析其具体含义。

**触发词示例：** "我的服务器"、"老规矩"、"上次那个项目"、"常用的技术栈"、"我的数据库"、"那个组件"、"按之前的来"等。

**判定规则：** IF prompt contains unresolved entities OR missing explicit parameters THEN `wiki_query("相关关键词")`。

**操作：** 提取模糊指代词中的关键实体，构造查询语句。若查询无结果，再向用户确认。

### 2. 代码规范与偏好（Coding Standards & Preferences）

用户要求写代码、做架构设计或 Code Review，但未在当前对话中明确给出语言版本、命名规范、测试框架偏好等约束。

**判定规则：** IF task_type == 'code_generation' AND constraints_provided == False THEN `wiki_query("coding preferences <技术栈关键词>")`。

**操作：** 查询用户的编码偏好，将结果作为代码生成的隐式约束。若用户后续给出明确指令，以用户指令为准。

### 3. 自定义领域知识（Domain/Custom Knowledge）

用户提到特定的错误日志、内部库名称、自定义组件名或项目特有术语。

**判定规则：** IF prompt contains custom_identifiers OR specific_error_codes THEN `wiki_query("相关标识符或错误码")`。

**操作：** 优先查询 Wiki 中是否有对应的解决方案或说明文档，避免给出通用但不适用的建议。

### 4. 决策权衡支持（Decision Support）

用户询问方案选型（如 "这两种方案我选哪个比较好"），需要参考用户的工作习惯、硬件环境、历史技术栈倾向。

**判定规则：** IF task_type == 'decision_making' THEN `wiki_query("相关技术偏好和历史决策")`。

**操作：** 查询相关偏好和历史决策，辅助给出有依据的推荐。

## 二、触发写入（Write/Store）的条件

写入操作需严格把控，避免将低价值信息塞满 Wiki。**以下条件任一满足时触发写入**：

### 1. 显式记忆指令（Explicit Command）

用户直接使用了明确的触发词。

**触发词：** "记住"、"记下来"、"存入wiki"、"更新配置"、"save to wiki"、"remember this"、"note this"。

**判定规则：** IF prompt contains any trigger word THEN extract content AND `wiki_write(content)`。

**操作：** 提取用户要求记住的内容，格式化为 markdown，写入 Wiki。

### 2. 隐式偏好纠正（Implicit Correction）

用户纠正了 AI 的回答，且该纠正具有普适性（不仅适用于当前上下文）。

**典型场景：** "以后不要用 var，全用 let 或 const"、"我不喜欢用 Python，给我 Go 的版本"、"用单引号不要双引号"。

**判定规则：** IF user_corrects_ai == True AND correction_is_reusable == True THEN extract_rule AND `wiki_write(rule)`。

**操作：** 将纠正内容提炼为规则，写入 Wiki，并告知用户 "已将此偏好记录到你的 Wiki"。

### 3. 高价值经验沉淀（High-Value Resolution）

在经历较长的 Debug 过程或架构讨论最终得出结论时，自动归档。

**判定规则：** IF debug_or_discussion_turns > 5 AND task_status == 'resolved' AND solution_is_non_trivial THEN summarize_solution AND `wiki_write(summary)`。

**操作：** 总结解决方案和关键决策点，写入 Wiki，并告知用户 "这个问题花了些时间，我已将解决方案归档到你的 Wiki"。

### 4. 实体状态永久变更（State Change）

用户提及个人环境或习惯发生了根本性改变。

**典型场景：** "我刚把主力机换成 Mac M3"、"项目数据库从 MySQL 迁移到 PostgreSQL 16"、"换了新的包管理器"。

**判定规则：** IF intent == 'update_environment_or_toolchain' THEN `wiki_write(updated_state)`。

**操作：** 将变更信息写入 Wiki，更新或覆盖相关条目。

## 三、负向条件（绝对不触发 Wiki 操作）

以下情况 **不得触发任何 Wiki 读写操作**：

1. **通用常识问题：** 标准库 API 用法、公认算法原理、通用翻译等，直接作答。
2. **高频/瞬时状态：** 用户的情绪表达、短期任务安排（"今天下午要开会"）、一次性脚本。
3. **纯探索性提问：** 用户明确表示 "我想了解一下 XYZ 的新特性"，属于向外求索，不依赖过往经验。
4. **用户明确拒绝：** 当用户表示 "不用查 wiki"、"直接回答" 时，跳过 Wiki 查询。
5. **环境变量未配置：** `WIKI_API_URL` 或 `WIKI_AUTH_TOKEN` 未设置时，跳过所有操作并提示用户。

## 四、API 调用方式

### 查询 Wiki

```bash
curl -s "http://localhost:3000/query?q=$(python3 -c 'import urllib.parse; print(urllib.parse.quote("查询内容"))')&token=$WIKI_AUTH_TOKEN"
```

实际调用时将地址替换为 `$WIKI_API_URL`。

**响应格式：**
```json
{
  "result": "<wiki 查询结果>"
}
```

查询后，将结果整合到当前任务的上下文中，自然地融入回答，不需要告知用户 "我查了你的 Wiki"（除非结果为空且需要向用户确认）。

### 写入 Wiki

1. 将内容保存为临时 markdown 文件
2. 通过 API 上传：

```bash
curl -s -X POST -F "file=@/tmp/wiki-entry.md" "${WIKI_API_URL}/upload?token=${WIKI_AUTH_TOKEN}"
```

**响应格式：**
```json
{
  "path": "tmp/2026-05-10T17-30-00-notes.md",
  "queueSize": 1
}
```

写入后简短告知用户归档结果。

### 写入内容的格式要求

写入 Wiki 的内容应遵循以下格式：

```markdown
---
title: <简洁标题>
tags: [<相关标签>]
created: <ISO 日期>
type: <preference | rule | solution | environment>
---

# <标题>

<正文内容>
```

- `type` 字段说明：`preference`（偏好）、`rule`（规则）、`solution`（解决方案）、`environment`（环境信息）
- 内容应简洁、结构化，方便未来检索

## 五、执行流程总结

```
用户输入
  │
  ├─ 检查负向条件 → 命中 → 直接处理，不触发 Wiki
  │
  ├─ 检查写入条件 → 命中 → 提取内容 → wiki_write()
  │
  ├─ 检查读取条件 → 命中 → 构造查询 → wiki_query() → 整合结果 → 执行任务
  │
  └─ 无条件命中 → 直接处理，不触发 Wiki
```

每次评估按 **负向条件 → 写入条件 → 读取条件** 的顺序进行，一旦命中即刻执行。
