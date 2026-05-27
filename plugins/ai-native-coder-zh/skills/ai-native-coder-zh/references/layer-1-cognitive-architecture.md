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
