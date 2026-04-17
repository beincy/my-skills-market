# my-skills-market

个人 Claude Code 插件市场，基于官方 `/plugin` 命令体系。

## 添加市场

```bash
/plugin marketplace add beincy/my-skills-market
```

## 查看可用插件

```bash
/plugin list
```

## 安装插件

```bash
/plugin install <plugin-name>@my-skills-market
```

## 更新市场

```bash
/plugin marketplace update
```

---

## 插件列表

> 以下插件已可安装使用。

### spec — 规范驱动开发 (Storm & Plan)

> 在动手写代码之前，先观察设计 (Storm)，再执行蓝图 (Plan)。

**安装：**
```bash
/plugin install spec@my-skills-market
```

**使用：**
- `/spec:require <任务描述>`：进入需求澄清助手模式，理清模糊想法，多方案对比。
- `/spec:plan <需求内容>`：根据确认的设计，生成规范的 `task.md` 任务清单。

**包含模块：**
- 🎯 需求澄清与方案对比 (Require)
- 📋 严格结构化的任务流水线 (Plan)
- 🛡️ 自动熔断机制：信息不足时拒绝生成并提示澄清
- ✅ 验收检查清单 (Checklist)

---

## 本地开发测试

```bash
# 在项目根目录执行
claude plugin validate .

# 本地添加测试
/plugin marketplace add ./
```
