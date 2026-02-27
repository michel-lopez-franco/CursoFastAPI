# Soluciones Sugeridas - Tema 09

## Ejercicio 1

```python
async def get_notifications() -> list[str]:
    await asyncio.sleep(1)
    return ["Notif-1", "Notif-2"]


@app.get("/dashboard")
async def dashboard() -> dict:
    users, orders, notifications = await asyncio.gather(
        get_users(),
        get_orders(),
        get_notifications(),
    )
    return {
        "users": users,
        "orders": orders,
        "notifications": notifications,
    }
```

## Ejercicio 2

```python
import time


@app.get("/dashboard")
async def dashboard() -> dict:
    start = time.perf_counter()
    users, orders = await asyncio.gather(get_users(), get_orders())
    duration = time.perf_counter() - start
    return {"users": users, "orders": orders, "duration": duration}
```
