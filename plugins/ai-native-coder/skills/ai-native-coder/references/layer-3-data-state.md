# Layer 3 — Data & State Engineering

## 3.1 Schema Is the Core Asset

Including:

* PostgreSQL DDL
* JSON Schema
* Event Schema
* Replay Schema

These are the highest-priority contracts.

---

## 3.2 Strong State vs Weak Cognitive State

Must distinguish:

### Strong State

Strict Schema:

* payment
* order
* inventory
* balance

### Weak Cognitive State

High-evolution Blob:

* reasoning_trace
* tool_history
* multimodal_metadata
* browser_state

---

## 3.3 JSONB Strategy

Dynamic cognitive data may use JSONB storage.

Avoid:

* Frequent Migrations
* Schema Explosion
* Version Explosion

---

## 3.4 Migration First

Any Schema change must consider:

* Migration
* Rollback
* Replay
* Shadow Validation
* Backward Compatibility

---

## 3.5 DDL Attention Layout

DDL column order:

```sql
1. primary business keys
2. state fields
3. relation fields
4. mutable business fields
5. audit/meta fields
```

Audit fields:

must be at the bottom:

```sql
created_at
updated_at
deleted_at
```