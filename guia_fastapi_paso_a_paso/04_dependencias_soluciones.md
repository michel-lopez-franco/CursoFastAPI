# Soluciones Sugeridas - Tema 04

## Ejercicio 1

```python
@app.get("/admin")
def admin(_: str = Depends(get_api_key)) -> dict:
    return {"message": "Zona admin"}
```

## Ejercicio 2

```python
def get_api_key(x_api_key: str = Header(...)) -> str:
    valid_keys = {"dev-key-123", "mentor-key-456"}
    if x_api_key not in valid_keys:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="API key inválida",
        )
    return x_api_key
```
