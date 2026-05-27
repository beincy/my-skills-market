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