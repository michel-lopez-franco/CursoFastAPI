# 03 - Rutas, path params y query params

## 1. Path parameters

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/usuarios/{usuario_id}")
def obtener_usuario(usuario_id: int):
    return {"usuario_id": usuario_id}
```

`usuario_id` llega desde la URL y se valida como `int`.

## 2. Query parameters

```python
@app.get("/buscar")
def buscar(q: str, limite: int = 10):
    return {"q": q, "limite": limite}
```

Ejemplo:

`/buscar?q=fastapi&limite=5`

## 3. Parametros opcionales

```python
from typing import Optional


@app.get("/items")
def listar_items(categoria: Optional[str] = None):
    return {"categoria": categoria}
```

## 4. Validaciones con Query

```python
from fastapi import Query


@app.get("/productos")
def listar_productos(
    pagina: int = Query(1, ge=1),
    tamano: int = Query(10, ge=1, le=100),
):
    return {"pagina": pagina, "tamano": tamano}
```

## Ejercicio

Define endpoint `GET /peliculas/{id}` con:

- `id` como entero en path
- `detalle` como boolean opcional en query (default `false`)
