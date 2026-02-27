# 04 - Validacion de datos con Pydantic

## 1. Crear un modelo

```python
from pydantic import BaseModel, Field


class Producto(BaseModel):
    nombre: str = Field(min_length=2, max_length=100)
    precio: float = Field(gt=0)
    stock: int = Field(ge=0, default=0)
```

## 2. Usar el modelo en un POST

```python
from fastapi import FastAPI
from pydantic import BaseModel, Field

app = FastAPI()


class Producto(BaseModel):
    nombre: str = Field(min_length=2, max_length=100)
    precio: float = Field(gt=0)
    stock: int = Field(ge=0, default=0)


@app.post("/productos")
def crear_producto(producto: Producto):
    return {"ok": True, "producto": producto}
```

FastAPI valida automaticamente el body JSON.

## 3. Modelo de respuesta

```python
class ProductoOut(BaseModel):
    id: int
    nombre: str
    precio: float
    stock: int


@app.post("/productos", response_model=ProductoOut)
def crear_producto(producto: Producto):
    return {"id": 1, **producto.model_dump()}
```

## 4. Errores comunes

- Enviar string en vez de numero.
- Omitir campos obligatorios.
- Enviar `precio <= 0`.

Todos regresan `422 Unprocessable Entity` con detalle.

## Ejercicio

Agrega un campo `email_proveedor` con validacion de email.
