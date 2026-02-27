# TEMA: Nivel Pro (Evolución de la API)

Rol de esta guía: Senior Backend Developer y Mentor de FastAPI.

## 1. Introducción y Concepto

Nivel pro no significa "usar todo", significa elegir bien según necesidades reales.

En esta etapa verás ideas clave:

- Versionado de API.
- Tareas asíncronas fuera del request.
- Observabilidad distribuida.

Problema que resuelve: escalar sin romper clientes existentes.

## 2. Escenario de Aplicación

Tendremos una API con versión `v1` y `v2` para el mismo recurso, manteniendo compatibilidad.

## 3. Explicación Paso a Paso (Línea por Línea)

### Bloque A: crear routers versionados

```python
from fastapi import APIRouter

v1_router = APIRouter(prefix="/v1", tags=["v1"])
v2_router = APIRouter(prefix="/v2", tags=["v2"])
```

- `prefix`: define versión en URL.
- `tags`: organiza documentación en `/docs`.

### Bloque B: contratos distintos por versión

```python
@v1_router.get("/products")
def products_v1() -> dict:
    return {"items": ["A", "B"]}


@v2_router.get("/products")
def products_v2() -> dict:
    return {"items": ["A", "B"], "total": 2}
```

- `v1` mantiene contrato antiguo.
- `v2` agrega campos sin romper clientes de `v1`.

### Bloque C: registrar routers

```python
app.include_router(v1_router)
app.include_router(v2_router)
```

- La app expone ambas versiones al mismo tiempo.

## 4. Código Completo

Archivo sugerido: `main.py`

```python
from fastapi import APIRouter, FastAPI

app = FastAPI(title="API Versionada")

v1_router = APIRouter(prefix="/v1", tags=["v1"])
v2_router = APIRouter(prefix="/v2", tags=["v2"])


@v1_router.get("/products")
def products_v1() -> dict:
    """Versión inicial del contrato."""
    return {"items": ["A", "B"]}


@v2_router.get("/products")
def products_v2() -> dict:
    """Versión evolucionada con metadata adicional."""
    return {
        "items": ["A", "B"],
        "total": 2,
        "meta": {"source": "catalog-service"},
    }


app.include_router(v1_router)
app.include_router(v2_router)
```

## 5. Sección de Prácticas

1. Agrega endpoint `/v1/orders` y `/v2/orders` con contratos distintos.
2. En `v2`, agrega paginación (`page`, `size`) por query params.

Revisa soluciones en: `13_nivel_pro_soluciones.md`
