# Layer 4 — Identity & Context Compression（身份与上下文压缩）

## 4.1 禁止高熵 ID 进入 Context

禁止直接向 LLM 暴露：

* UUID
* Base64
* 长随机串
* opaque hash

---

## 4.2 Typed Compact Identity

推荐：

```txt id="jlwmzb"
ORD_7
USR_12
PAY_3
```

---

## 4.3 低熵 ID 可直接透传

如果系统 ID 已满足：

* 短长度
* 低熵
* 稳定
* 可读

允许直接进入 Context。

例如：

```txt id="jlwmzc"
104857
204913
```

---

## 4.4 Alias 必须 Deterministic

禁止：

Session 级随机 alias map。
