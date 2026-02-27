# Soluciones Sugeridas - Tema 13

## Ejercicio 1

```python
@v1_router.get("/orders")
def orders_v1() -> dict:
    return {"orders": ["O-1", "O-2"]}


@v2_router.get("/orders")
def orders_v2() -> dict:
    return {"orders": ["O-1", "O-2"], "total": 2}
```

## Ejercicio 2

```python
@v2_router.get("/orders")
def orders_v2(page: int = 1, size: int = 10) -> dict:
    all_orders = ["O-1", "O-2", "O-3", "O-4"]
    start = (page - 1) * size
    end = start + size
    return {
        "orders": all_orders[start:end],
        "page": page,
        "size": size,
        "total": len(all_orders),
    }
```
