# Soluciones Sugeridas - Tema 11

## Ejercicio 1

```python
@app.get("/health/details")
def health_details() -> dict:
    return {"status": "ready", "version": "1.0.0"}
```

## Ejercicio 2

```python
@app.get("/health/ready")
def ready() -> dict:
    logger.info("Se consultó /health/ready")
    return {"status": "ready"}
```
