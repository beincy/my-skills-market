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