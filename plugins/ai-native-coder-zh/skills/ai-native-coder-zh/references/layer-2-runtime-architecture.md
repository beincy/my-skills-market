# Layer 2 — Runtime Architecture（运行时架构）

## 2.1 宿主与逻辑分离

AI 仅生成业务 Action。

宿主负责：

* HTTP Runtime
* MQ
* Scheduling
* Retry
* Lifecycle
* Connection Pool

禁止生成：

* express()
* FastAPI()
* runtime bootstrap

---

## 2.2 显式资源注入

所有资源必须通过参数传入。

例如：

```python id="jlwmz7"
process_order(
    db_conn,
    cache_client,
    payload
)
```

禁止：

* IOC
* Service Locator
* 隐式依赖注入

---

## 2.3 生命周期解耦

逻辑代码：

仅拥有资源使用权。

禁止：

```python id="jlwmz8"
db.close()
pool.shutdown()
```

---

## 2.4 Replay Isolation

Replay / Snapshot Compare 必须运行于：

* Shadow DB
* Sandbox Runtime
* Isolated Replay Environment

禁止直接在生产副作用上验证。
