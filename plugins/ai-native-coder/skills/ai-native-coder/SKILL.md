---
name: ai-native-coder
description: "System Prompt / Skill for AI Agents: Enforces AI-Native Engineering rules with behavioral guardrails. Prioritizes context window efficiency, strict isolation, stateless execution, and LLM-optimized syntax."
---

# AI-Native Engineering Spec v6

This skill defines a **seven-layer engineering specification** for LLM / AI Agents.
Rules are organized by layer responsibility and loaded on-demand to minimize Context usage.

## Architecture Overview

| Layer | Responsibility | Reference File |
|-------|---------------|----------------|
| Layer 0 | Absolute Runtime Laws (Highest Priority) | `references/layer-0-runtime-laws.md` |
| Layer 1 | Cognitive Architecture (Context Organization) | `references/layer-1-cognitive-architecture.md` |
| Layer 2 | Runtime Architecture (Host/Logic Separation) | `references/layer-2-runtime-architecture.md` |
| Layer 3 | Data & State Engineering | `references/layer-3-data-state.md` |
| Layer 4 | Identity & Context Compression | `references/layer-4-identity-compression.md` |
| Layer 5 | Engineering Conventions | `references/layer-5-engineering-conventions.md` |
| Layer 6 | AI Runtime Evolution | `references/layer-6-ai-runtime-evolution.md` |

## How to Use

- **Before starting a task**: Confirm Layer 0's 10 absolute laws (see quick reference below).
- **When designing systems / organizing code**: Read Layer 1 + Layer 2.
- **When working with data, schema, or state**: Read Layer 3 + Layer 4.
- **When writing concrete implementations**: Read Layer 5.
- **When making architecture evolution decisions**: Read Layer 6.

Each rule's detailed explanation, examples, and prohibitions are in the corresponding layer's reference file.

---

## Layer 0 Quick Reference (Absolute Runtime Laws)

Layer 0 has the highest priority and must never be violated under any circumstances. For full details, see `references/layer-0-runtime-laws.md`.

| # | Law | Core Meaning |
|---|-----|-------------|
| LAW-1 | Runtime Correctness > Token Efficiency | Must not sacrifice system correctness for LLM convenience |
| LAW-2 | State > Code | Code is rebuildable, state is irreplaceable |
| LAW-3 | All Side Effects Must Be Replayable | DB/MQ/HTTP/Files must all support Replay |
| LAW-4 | Replay Must Be Deterministic | Forbidden: implicit randomness, uncontrollable time dependency |
| LAW-5 | Runtime Layer != Cognitive Layer | Runtime and cognitive responsibilities must not be conflated |
| LAW-6 | No Hidden Side Effects | Forbidden: decorator magic, AOP, implicit mutation |
| LAW-7 | No Deep Dependency Chains | Dependency depth ≤ 2 |
| LAW-8 | Context Must Be Locally Closed | Single reasoning pass ≤ 3 Context Units |
| LAW-9 | Semantic Version Must Be Locked | Forbidden: replay historical traffic with latest rules |
| LAW-10 | AI Outputs Must Be Traceable | All side effects must trace to model/prompt/schema |