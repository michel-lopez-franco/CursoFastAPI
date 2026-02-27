# Soluciones Sugeridas - Tema 02

## Ejercicio 1

```python
@app.get("/restar")
def restar(a: int, b: int) -> dict:
    return {"resultado": a - b}
```

## Ejercicio 2

```python
@app.post("/cuadrado")
def cuadrado(payload: NumeroEntrada) -> dict:
    return {"original": payload.valor, "cuadrado": payload.valor ** 2}
```
