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