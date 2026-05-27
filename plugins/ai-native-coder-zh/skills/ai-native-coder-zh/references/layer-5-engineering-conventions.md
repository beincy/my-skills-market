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
