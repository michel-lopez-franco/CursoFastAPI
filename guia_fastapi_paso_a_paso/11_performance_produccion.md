# TEMA: Performance y Producción

Rol de esta guía: Senior Backend Developer y Mentor de FastAPI.

## 1. Introducción y Concepto

En local todo suele funcionar. En producción necesitas estabilidad, monitoreo y límites.

¿Por qué importa?

- Detectas fallos antes de que escalen.
- Aseguras tiempos de respuesta razonables.

Problema que resuelve: operar tu API con prácticas de producción.

## 2. Escenario de Aplicación

Implementaremos una base mínima de producción:

- Health checks.
- Logging estructurado sencillo.
- Ejecución con Gunicorn + Uvicorn workers.

## 3. Explicación Paso a Paso (Línea por Línea)

### Bloque A: health checks

```python
@app.get("/health/live")
def live() -> dict:
    return {"status": "alive"}


@app.get("/health/ready")
def ready() -> dict:
    return {"status": "ready"}
```

- `live`: proceso encendido.
- `ready`: proceso listo para tráfico.

### Bloque B: logging simple

```python
import logging

logger = logging.getLogger("app")
logger.setLevel(logging.INFO)
```

- Define logger central.
- Nivel `INFO` para eventos operativos.

### Bloque C: comando de ejecución

```bash
gunicorn main:app -k uvicorn.workers.UvicornWorker -w 2 -b 0.0.0.0:8000
```

- `-k`: worker ASGI para FastAPI.
- `-w 2`: dos procesos para concurrencia inicial.

## 4. Código Completo

Archivo sugerido: `main.py`

```python
import logging
from fastapi import FastAPI

app = FastAPI(title="API Producción")

logger = logging.getLogger("app")
logger.setLevel(logging.INFO)


@app.get("/health/live")
def live() -> dict:
    """Confirma que el proceso está vivo."""
    return {"status": "alive"}


@app.get("/health/ready")
def ready() -> dict:
    """Confirma que el servicio está listo para recibir tráfico."""
    return {"status": "ready"}


@app.get("/report")
def report() -> dict:
    """Endpoint de ejemplo con logging."""
    logger.info("Se consultó /report")
    return {"message": "Reporte generado"}
```

Comando sugerido para producción inicial:

```bash
gunicorn main:app -k uvicorn.workers.UvicornWorker -w 2 -b 0.0.0.0:8000
```

## 5. Sección de Prácticas

1. Agrega un endpoint `/health/details` que incluya versión de la API.
2. Agrega logging para el endpoint `/health/ready`.

Revisa soluciones en: `11_performance_produccion_soluciones.md`
