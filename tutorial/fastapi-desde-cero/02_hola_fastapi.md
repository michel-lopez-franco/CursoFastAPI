# 02 - Tu primera aplicacion FastAPI

## 1. Codigo minimo

Crea `main.py` con este contenido:

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def home():
    return {"mensaje": "Hola FastAPI"}
```

## 2. Ejecutar servidor

```bash
uvicorn main:app --reload
```

## 3. Probar en navegador

- API: `http://127.0.0.1:8000/`
- Swagger UI: `http://127.0.0.1:8000/docs`
- ReDoc: `http://127.0.0.1:8000/redoc`

## 4. Que esta pasando

- `FastAPI()` crea la app.
- `@app.get("/")` define una ruta GET.
- FastAPI genera documentacion automatica.

## 5. Agregar metadata

```python
from fastapi import FastAPI

app = FastAPI(
    title="Mi primera API",
    description="API de ejemplo para aprender FastAPI",
    version="0.1.0",
)


@app.get("/")
def home():
    return {"mensaje": "Hola FastAPI"}
```

## Ejercicio

Crea una nueva ruta `/saludo` que regrese:

```json
{"mensaje": "Bienvenido al curso"}
```
