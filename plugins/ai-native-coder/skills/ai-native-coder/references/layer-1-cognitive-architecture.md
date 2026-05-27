# Layer 1 — Cognitive Architecture

## 1.1 Context Closure First

The goal is not:

absolute single-file.

The goal is:

minimal reasoning closure.

Recommended:

```txt
feature_x/
  action.py
  schema.yaml
  semantic_refs.yaml
  replay.yaml
```

---

## 1.2 Attention First Layout

High-value information must come first.

Recommended order:

```txt
1. Schema
2. Semantic Rules
3. Side Effects
4. Main Flow
5. Replay Rules
6. Meta/Audit
```

---

## 1.3 Semantic Registry

Allowed:

read-only domain semantic library.

Example:

```txt
semantic_registry/
  order_states/
  tax_rules/
  workflow_rules/
```

What is shared:

domain consensus.

Not execution logic.

---

## 1.4 Semantic Snapshot

During Replay, the system must read:

the Semantic Snapshot at execution time.

Dynamically reading the latest rules is forbidden.

---

## 1.5 Intelligence Tier Awareness

Runtime must dynamically adjust based on model capability:

* Context Density
* Validation Strictness
* Replay Depth
* Schema Compression