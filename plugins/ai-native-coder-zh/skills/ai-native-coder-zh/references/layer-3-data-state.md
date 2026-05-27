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

* reasoning_trace
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
