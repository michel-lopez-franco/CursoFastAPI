# TEMA: Validación y Serialización con Pydantic

Rol de esta guía: Senior Backend Developer y Mentor de FastAPI.

## 1. Introducción y Concepto

Validar es asegurar que los datos de entrada sean correctos.
Serializar es transformar tus objetos en respuestas limpias para el cliente.

¿Por qué importa?

- Evitas datos basura en tu sistema.
- Tu API mantiene contratos claros.
- Reduces errores al guardar información.

## 2. Escenario de Aplicación

Construiremos un endpoint para registrar productos:

- Entrada: nombre, precio y stock.
- Regla: precio y stock no pueden ser negativos.
- Salida: incluir `is_available` según stock.

## 3. Explicación Paso a Paso (Línea por Línea)

### Bloque A: request model

```python
from pydantic import BaseModel, Field


class ProductCreate(BaseModel):
    name: str = Field(min_length=2, max_length=50)
    price: float = Field(gt=0)
    stock: int = Field(ge=0)
```

- `Field(...)`: agrega reglas de validación.
- `min_length` y `max_length`: controlan tamaño del texto.
- `gt=0`: precio debe ser mayor a cero.
- `ge=0`: stock puede ser 0, pero nunca negativo.

### Bloque B: response model

```python
class ProductResponse(BaseModel):
    id: int
    name: str
    price: float
    stock: int
    is_available: bool
```

- Separa entrada de salida: buena práctica real.
- `is_available` es un campo calculado para el frontend.

### Bloque C: usar response_model

```python
@app.post("/products", response_model=ProductResponse)
def create_product(payload: ProductCreate) -> ProductResponse:
    return ProductResponse(
        id=1,
        name=payload.name,
        price=payload.price,
        stock=payload.stock,
        is_available=payload.stock > 0,
    )
```

- `response_model=...`: FastAPI valida también la salida.
- `payload: ProductCreate`: valida body de entrada.
- `payload.stock > 0`: lógica de negocio simple.

## 4. Código Completo

Archivo sugerido: `main.py`

```python
from fastapi import FastAPI
from pydantic import BaseModel, Field

app = FastAPI(title="API Productos")


class ProductCreate(BaseModel):
    """Datos requeridos para crear un producto."""

    name: str = Field(min_length=2, max_length=50)
    price: float = Field(gt=0)
    stock: int = Field(ge=0)


class ProductResponse(BaseModel):
    """Datos que regresamos al cliente."""

    id: int
    name: str
    price: float
    stock: int
    is_available: bool


@app.post("/products", response_model=ProductResponse)
def create_product(payload: ProductCreate) -> ProductResponse:
    """Crea un producto y calcula disponibilidad."""
    return ProductResponse(
        id=1,
        name=payload.name,
        price=payload.price,
        stock=payload.stock,
        is_available=payload.stock > 0,
    )
```

## 5. Sección de Prácticas

1. Agrega un campo opcional `description` con máximo 120 caracteres.
2. En la respuesta, agrega `price_with_tax` (precio + 16%).

Revisa soluciones en: `03_validacion_serializacion_soluciones.md`
