# Soluciones Sugeridas - Tema 07

## Ejercicio 1

```python
@app.get("/user")
def user_area(user: dict = Depends(get_current_user)) -> dict:
    return {"message": "Zona de usuario", "user": user}
```

## Ejercicio 2

```python
def create_access_token(email: str, role: str) -> str:
    payload = {
        "sub": email,
        "role": role,
        "exp": datetime.now(UTC) + timedelta(minutes=10),
    }
    return jwt.encode(payload, SECRET_KEY, algorithm=ALGORITHM)
```
