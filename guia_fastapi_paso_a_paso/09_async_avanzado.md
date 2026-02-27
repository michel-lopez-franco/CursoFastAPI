# TEMA: Async Avanzado en FastAPI

Rol de esta guía: Senior Backend Developer y Mentor de FastAPI.

## 1. Introducción y Concepto

`async/await` permite manejar múltiples tareas de I/O sin bloquear el servidor.

¿Por qué importa?

- Mejoras tiempo de respuesta en endpoints que llaman APIs o DB.
- Puedes atender más requests concurrentes.

Problema que resuelve: evitar cuellos de botella por esperas bloqueantes.

## 2. Escenario de Aplicación

Crearemos un endpoint que consulta dos fuentes externas simuladas en paralelo y regresa una respuesta combinada.

## 3. Explicación Paso a Paso (Línea por Línea)

### Bloque A: funciones asíncronas

```python
import asyncio


async def get_users() -> list[str]:
    await asyncio.sleep(1)
    return ["Ana", "Luis"]


async def get_orders() -> list[str]:
    await asyncio.sleep(1)
    return ["Orden-1", "Orden-2"]
```

- `await asyncio.sleep(1)`: simula espera de red o DB.
- Ambas funciones son asíncronas, ideales para concurrencia.

### Bloque B: ejecutar en paralelo

```python
users, orders = await asyncio.gather(get_users(), get_orders())
```

- `gather(...)` corre ambas tareas al mismo tiempo.
- Sin `gather`, se ejecutarían una después de otra.

### Bloque C: endpoint async

```python
@app.get("/dashboard")
async def dashboard() -> dict:
    users, orders = await asyncio.gather(get_users(), get_orders())
    return {"users": users, "orders": orders}
```

- `async def` porque usamos `await` dentro.
- La respuesta combina datos de dos fuentes.

## 4. Código Completo

Archivo sugerido: `main.py`

```python
import asyncio
from fastapi import FastAPI

app = FastAPI(title="API Async")


async def get_users() -> list[str]:
    """Simula una consulta de usuarios."""
    await asyncio.sleep(1)
    return ["Ana", "Luis"]


async def get_orders() -> list[str]:
    """Simula una consulta de órdenes."""
    await asyncio.sleep(1)
    return ["Orden-1", "Orden-2"]


@app.get("/dashboard")
async def dashboard() -> dict:
    """Agrega datos concurrentes para una vista de dashboard."""
    users, orders = await asyncio.gather(get_users(), get_orders())
    return {"users": users, "orders": orders}


@app.get("/ping")
def ping() -> dict:
    """Endpoint simple síncrono (sin await)."""
    return {"message": "pong"}
```

## 5. Sección de Prácticas

1. Agrega una tercera función async `get_notifications` y combínala con `gather`.
2. Mide el tiempo total con `time.perf_counter` para comprobar que se ejecutan en paralelo.

Revisa soluciones en: `09_async_avanzado_soluciones.md`
