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