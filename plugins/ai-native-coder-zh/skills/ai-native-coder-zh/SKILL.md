---
name: ai-native-coder-zh
description: "AI 智能体系统提示/技能：强制执行 AI 原生工程规则，配合行为准则约束。优先保障上下文窗口效率、严格隔离、无状态执行以及面向 LLM 优化的语法。"
---

# AI-Native Engineering Spec v6

# Layer 0 — Absolute Runtime Laws（绝对运行时法则）

以下规则拥有最高优先级。
任何情况下不得违反。

---

## LAW-1 Runtime Correctness > Token Efficiency

Token 优化永远不得破坏：

* Correctness
* Replayability
* Distributed Safety
* State Integrity

禁止为了 LLM 便利性牺牲系统正确性。

---

## LAW-2 State > Code

代码可以重建。

状态不可丢失。

系统设计优先保护：

* 数据一致性
* 幂等性
* Migration 能力
* Replay 能力
* 回滚能力

---

## LAW-3 All Side Effects Must Be Replayable

所有副作用必须支持：

* Replay
* Snapshot Compare
* Shadow Validation

包括：

* DB 写入
* MQ
* HTTP 调用
* 文件修改

---

## LAW-4 Replay Must Be Deterministic

禁止：

* 隐式随机行为
* 不可控时间依赖
* 非确定性状态修改

相同输入必须产生相同输出。

---

## LAW-5 Runtime Layer != Cognitive Layer

Runtime Layer 负责：

* Correctness
* Scalability
* Distributed Safety

Cognitive Layer 负责：

* Attention Stability
* Context Compression
* AI Reasoning

禁止混淆两者职责。

---

## LAW-6 No Hidden Side Effects

所有副作用必须显式声明。

禁止：

* decorator magic
* hidden transaction
* implicit mutation
* AOP

---

## LAW-7 No Deep Dependency Chains

禁止：

```txt id="jlwmz1"
A → B → C → D
```

依赖深度建议 ≤ 2。

---

## LAW-8 Context Must Be Locally Closed

一个功能必须：

在最小 Context 内完成完整推理。

目标：

```txt id="jlwmz2"
一次推理
≤ 3 个 Context Unit
```

---

## LAW-9 Semantic Version Must Be Locked

Replay 时必须锁定：

* semantic_registry_version
* schema_version
* action_version
* prompt_version
* model_version

禁止使用最新规则重放历史流量。

---

## LAW-10 AI Outputs Must Be Traceable

所有 Side Effects 必须可追溯：

```yaml id="jlwmz3"
model
prompt_hash
context_hash
schema_version
semantic_registry_version
action_version
```

---

# Layer 1 — Cognitive Architecture（认知架构）

## 1.1 Context Closure 优先

目标不是：

绝对单文件。

目标是：

最小推理闭包。

推荐：

```txt id="jlwmz4"
feature_x/
  action.py
  schema.yaml
  semantic_refs.yaml
  replay.yaml
```

---

## 1.2 Attention First Layout

高价值信息必须前置。

推荐顺序：

```txt id="jlwmz5"
1. Schema
2. Semantic Rules
3. Side Effects
4. Main Flow
5. Replay Rules
6. Meta/Audit
```

---

## 1.3 Semantic Registry

允许：

只读领域语义库。

例如：

```txt id="jlwmz6"
semantic_registry/
  order_states/
  tax_rules/
  workflow_rules/
```

共享的是：

领域共识。

不是执行逻辑。

---

## 1.4 Semantic Snapshot

Replay 时必须读取：

执行时的 Semantic Snapshot。

禁止动态读取最新规则。

---

## 1.5 Intelligence Tier Awareness

Runtime 必须根据模型能力动态调整：

* Context Density
* Validation Strictness
* Replay Depth
* Schema Compression

---

# Layer 2 — Runtime Architecture（运行时架构）

## 2.1 宿主与逻辑分离

AI 仅生成业务 Action。

宿主负责：

* HTTP Runtime
* MQ
* Scheduling
* Retry
* Lifecycle
* Connection Pool

禁止生成：

* express()
* FastAPI()
* runtime bootstrap

---

## 2.2 显式资源注入

所有资源必须通过参数传入。

例如：

```python id="jlwmz7"
process_order(
    db_conn,
    cache_client,
    payload
)
```

禁止：

* IOC
* Service Locator
* 隐式依赖注入

---

## 2.3 生命周期解耦

逻辑代码：

仅拥有资源使用权。

禁止：

```python id="jlwmz8"
db.close()
pool.shutdown()
```

---

## 2.4 Replay Isolation

Replay / Snapshot Compare 必须运行于：

* Shadow DB
* Sandbox Runtime
* Isolated Replay Environment

禁止直接在生产副作用上验证。

---

# Layer 3 — Data & State Engineering（数据与状态工程）

## 3.1 Schema 是核心资产

包括：

* PostgreSQL DDL
* JSON Schema
* Event Schema
* Replay Schema

属于最高优先级契约。

---

## 3.2 Strong State vs Weak Cognitive State

必须区分：

### Strong State

严格 Schema：

* payment
* order
* inventory
* balance

### Weak Cognitive State

高演化 Blob：

* reasoning traces
* tool history
* multimodal metadata
* browser state

---

## 3.3 JSONB Strategy

动态认知数据允许 JSONB 存储。

避免：

* 高频 Migration
* Schema Explosion
* Version Explosion

---

## 3.4 Migration First

任何 Schema 修改必须考虑：

* Migration
* Rollback
* Replay
* Shadow Validation
* Backward Compatibility

---

## 3.5 DDL Attention Layout

DDL 字段顺序：

```sql id="jlwmz9"
1. primary business keys
2. state fields
3. relation fields
4. mutable business fields
5. audit/meta fields
```

审计字段：

必须位于底部：

```sql id="jlwmza"
created_at
updated_at
deleted_at
```

---

# Layer 4 — Identity & Context Compression（身份与上下文压缩）

## 4.1 禁止高熵 ID 进入 Context

禁止直接向 LLM 暴露：

* UUID
* Base64
* 长随机串
* opaque hash

---

## 4.2 Typed Compact Identity

推荐：

```txt id="jlwmzb"
ORD_7
USR_12
PAY_3
```

---

## 4.3 低熵 ID 可直接透传

如果系统 ID 已满足：

* 短长度
* 低熵
* 稳定
* 可读

允许直接进入 Context。

例如：

```txt id="jlwmzc"
104857
204913
```

---

## 4.4 Alias 必须 Deterministic

禁止：

Session 级随机 alias map。

---

# Layer 5 — Engineering Conventions（工程约定）

## 5.1 简单优先

仅实现：

* 当前需求
* 当前真实路径
* 当前必要逻辑

禁止：

* 提前抽象
* 未来设计
* 预防性架构

---

## 5.2 精准修改

修改必须：

* 可映射到需求
* 不影响无关行为
* 不改变既有语义

禁止：

* 顺手优化
* 顺手重构
* 顺手格式化

---

## 5.3 Context 可恢复性

任何模块：

在以下情况仍必须可恢复：

* 被截断
* 被摘要
* 被 AI 二次接手
* 丢失部分 Context

允许适度重复：

* Schema
* Semantic Rules
* Side Effects
* Output Structure

---

## 5.4 Immutable Core Layer

允许：

稳定低变更核心库：

```txt id="jlwmzd"
core/
  crypto/
  codecs/
  protocol/
  standards/
```

要求：

* 无状态
* 无业务语义
* 极低变更率
* 强可验证

---

## 5.5 禁止共享业务逻辑

禁止：

```txt id="jlwmze"
shared_business/
common_order_logic/
domain_utils/
```

禁止共享：

演化中的业务行为。

---

# Layer 6 — AI Runtime Evolution（AI 运行时演化）

## 6.1 下一位维护者是 AI

代码必须适合：

* 自动摘要
* 自动恢复
* 自动重建
* 自动裁剪

---

## 6.2 可删除性优先

任何模块必须：

可独立删除并重建。

禁止：

* 全局耦合
* 深层依赖传播
* 隐式共享状态

---

## 6.3 最终目标

最终目标不是生成代码。

而是构建：

* Replayable Runtime
* Attention-Oriented Architecture
* Context-Aware Infrastructure
* Deterministic Agent Systems
* AI-Native Operating Environment
