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
