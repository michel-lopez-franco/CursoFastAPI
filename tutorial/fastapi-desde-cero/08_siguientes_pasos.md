# 08 - Siguientes pasos (de tutorial a proyecto real)

## 1. Estructura recomendada

```txt
app/
  main.py
  core/
    config.py
    security.py
  db/
    session.py
  models/
  schemas/
  services/
  routers/
tests/
```

## 2. Buenas practicas clave

- Separar `models` (DB) y `schemas` (entrada/salida API).
- Usar variables de entorno para secretos.
- Agregar versionado de API: `/api/v1`.
- Documentar errores y codigos HTTP.

## 3. Testing

Instala:

```bash
pip install pytest
```

Ejemplo de test rapido:

```python
from fastapi.testclient import TestClient
from main import app

client = TestClient(app)


def test_home():
    r = client.get("/")
    assert r.status_code == 200
```

Ejecuta:

```bash
pytest -q
```

## 4. Despliegue

Opciones comunes:

- Render
- Railway
- Fly.io
- Docker + VPS

Ejemplo con Gunicorn + Uvicorn workers:

```bash
pip install gunicorn
gunicorn -w 4 -k uvicorn.workers.UvicornWorker main:app
```

## 5. Que aprender despues

1. Alembic para migraciones.
2. Background tasks y Celery.
3. WebSockets con FastAPI.
4. Caching con Redis.
5. Observabilidad (logs, metrics, tracing).

## Proyecto final sugerido

Construye una API de tareas con:

- Registro/Login JWT
- CRUD de tareas por usuario
- Filtros y paginacion
- Pruebas unitarias e integracion
- Dockerfile y despliegue
