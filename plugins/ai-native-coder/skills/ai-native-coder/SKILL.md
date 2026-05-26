---
name: ai-native-coder
description: "System Prompt / Skill for AI Agents: Enforces AI-Native Engineering rules with behavioral guardrails. Prioritizes context window efficiency, strict isolation, stateless execution, and LLM-optimized syntax."
---

# AI-Native Engineering Spec v6

# Layer 0 — Absolute Runtime Laws

The following rules have the highest priority.
They must never be violated under any circumstances.

---

## LAW-1 Runtime Correctness > Token Efficiency

Token optimization must never compromise:

* Correctness
* Replayability
* Distributed Safety
* State Integrity

Sacrificing system correctness for LLM convenience is forbidden.

---

## LAW-2 State > Code

Code can be rebuilt.

State must never be lost.

System design must prioritize:

* Data consistency
* Idempotency
* Migration capability
* Replay capability
* Rollback capability

---

## LAW-3 All Side Effects Must Be Replayable

All side effects must support:

* Replay
* Snapshot Compare
* Shadow Validation

Including:

* DB writes
* MQ
* HTTP calls
* File modifications

---

## LAW-4 Replay Must Be Deterministic

Forbidden:

* Implicit random behavior
* Uncontrollable time dependencies
* Non-deterministic state mutations

Same input must produce same output.

---

## LAW-5 Runtime Layer != Cognitive Layer

Runtime Layer is responsible for:

* Correctness
* Scalability
* Distributed Safety

Cognitive Layer is responsible for:

* Attention Stability
* Context Compression
* AI Reasoning

Mixing the responsibilities of the two is forbidden.

---

## LAW-6 No Hidden Side Effects

All side effects must be explicitly declared.

Forbidden:

* decorator magic
* hidden transaction
* implicit mutation
* AOP

---

## LAW-7 No Deep Dependency Chains

Forbidden:

```txt
A → B → C → D
```

Dependency depth should be ≤ 2.

---

## LAW-8 Context Must Be Locally Closed

A feature must:

complete its full reasoning within minimal Context.

Target:

```txt
One reasoning pass
≤ 3 Context Units
```

---

## LAW-9 Semantic Version Must Be Locked

During Replay, the following must be locked:

* semantic_registry_version
* schema_version
* action_version
* prompt_version
* model_version

Replaying historical traffic with latest rules is forbidden.

---

## LAW-10 AI Outputs Must Be Traceable

All Side Effects must be traceable:

```yaml
model
prompt_hash
context_hash
schema_version
semantic_registry_version
action_version
```

---

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

---

# Layer 2 — Runtime Architecture

## 2.1 Host vs. Logic Separation

AI only generates business Actions.

The Host is responsible for:

* HTTP Runtime
* MQ
* Scheduling
* Retry
* Lifecycle
* Connection Pool

Forbidden to generate:

* express()
* FastAPI()
* runtime bootstrap

---

## 2.2 Explicit Resource Injection

All resources must be passed as parameters.

Example:

```python
process_order(
    db_conn,
    cache_client,
    payload
)
```

Forbidden:

* IOC
* Service Locator
* implicit dependency injection

---

## 2.3 Lifecycle Decoupling

Logic code:

only has usage rights to resources.

Forbidden:

```python
db.close()
pool.shutdown()
```

---

## 2.4 Replay Isolation

Replay / Snapshot Compare must run in:

* Shadow DB
* Sandbox Runtime
* Isolated Replay Environment

Validating directly on production side effects is forbidden.

---

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

* reasoning_traces
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

---

# Layer 4 — Identity & Context Compression

## 4.1 No High-Entropy IDs in Context

Forbidden to expose directly to LLM:

* UUID
* Base64
* Long random strings
* opaque hash

---

## 4.2 Typed Compact Identity

Recommended:

```txt
ORD_7
USR_12
PAY_3
```

---

## 4.3 Low-Entropy IDs Pass Through

If the system ID already satisfies:

* Short length
* Low entropy
* Stable
* Readable

It is allowed to enter Context directly.

Example:

```txt
104857
204913
```

---

## 4.4 Alias Must Be Deterministic

Forbidden:

Session-level random alias maps.

---

# Layer 5 — Engineering Conventions

## 5.1 Simplicity First

Only implement:

* Current requirements
* Current real paths
* Current necessary logic

Forbidden:

* Premature abstraction
* Future design
* Preventive architecture

---

## 5.2 Surgical Changes

Changes must:

* Be traceable to requirements
* Not affect unrelated behavior
* Not alter existing semantics

Forbidden:

* Opportunistic optimization
* Opportunistic refactoring
* Opportunistic reformatting

---

## 5.3 Context Recoverability

Any module must remain recoverable when:

* Truncated
* Summarized
* Taken over by a second AI agent
* Partial Context is lost

Moderate repetition is allowed for:

* Schema
* Semantic Rules
* Side Effects
* Output Structure

---

## 5.4 Immutable Core Layer

Allowed:

stable, low-change core libraries:

```txt
core/
  crypto/
  codecs/
  protocol/
  standards/
```

Requirements:

* Stateless
* No business semantics
* Extremely low change rate
* Strongly verifiable

---

## 5.5 No Shared Business Logic

Forbidden:

```txt
shared_business/
common_order_logic/
domain_utils/
```

Sharing evolving business behavior is forbidden.

---

# Layer 6 — AI Runtime Evolution

## 6.1 The Next Maintainer Is AI

Code must be suitable for:

* Automatic summarization
* Automatic recovery
* Automatic rebuilding
* Automatic trimming

---

## 6.2 Deletability First

Any module must be:

independently deletable and rebuildable.

Forbidden:

* Global coupling
* Deep dependency propagation
* Implicit shared state

---

## 6.3 Ultimate Goal

The ultimate goal is not generating code.

It is building:

* Replayable Runtime
* Attention-Oriented Architecture
* Context-Aware Infrastructure
* Deterministic Agent Systems
* AI-Native Operating Environment
