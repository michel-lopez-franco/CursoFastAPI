# TEMA: Funcionalidades Intermedias de FastAPI

Rol de esta guía: Senior Backend Developer y Mentor de FastAPI.

## 1. Introducción y Concepto

Estas funciones te acercan a casos de producción:

- Middleware.
- Background tasks.
- Upload de archivos.

Problema que resuelve: agregar capacidades reales más allá de CRUD básico.

## 2. Escenario de Aplicación

Construiremos un mini módulo para subir un archivo y registrar un evento en segundo plano.

## 3. Explicación Paso a Paso (Línea por Línea)

### Bloque A: middleware de tiempo

```python
import time


@app.middleware("http")
async def add_process_time(request, call_next):
    start = time.perf_counter()
    response = await call_next(request)
    response.headers["X-Process-Time"] = f"{time.perf_counter() - start:.6f}"
    return response
```

- Mide cuánto tarda cada request.
- Guarda el resultado en un header de respuesta.

### Bloque B: tarea en segundo plano

```python
from fastapi import BackgroundTasks


def log_event(message: str) -> None:
    with open("events.log", "a", encoding="utf-8") as file:
        file.write(message + "\n")


@app.post("/event")
def create_event(background_tasks: BackgroundTasks) -> dict:
    background_tasks.add_task(log_event, "Evento creado")
    return {"message": "Evento recibido"}
```

- `BackgroundTasks` permite responder rápido.
- `add_task(...)` ejecuta función después de enviar respuesta.

### Bloque C: upload file

```python
from fastapi import File, UploadFile


@app.post("/upload")
async def upload_file(file: UploadFile = File(...)) -> dict:
    content = await file.read()
    return {"filename": file.filename, "size": len(content)}
```

- `UploadFile`: maneja archivos enviados por formulario.
- `await file.read()`: lectura asíncrona.

## 4. Código Completo

Archivo sugerido: `main.py`

```python
import time
from fastapi import BackgroundTasks, FastAPI, File, UploadFile

app = FastAPI(title="API Intermedia")


@app.middleware("http")
async def add_process_time(request, call_next):
    """Agrega tiempo de proceso por request."""
    start = time.perf_counter()
    response = await call_next(request)
    response.headers["X-Process-Time"] = f"{time.perf_counter() - start:.6f}"
    return response


def log_event(message: str) -> None:
    """Registra un evento en archivo local."""
    with open("events.log", "a", encoding="utf-8") as file:
        file.write(message + "\n")


@app.post("/event")
def create_event(background_tasks: BackgroundTasks) -> dict:
    """Recibe evento y lo procesa en segundo plano."""
    background_tasks.add_task(log_event, "Evento creado")
    return {"message": "Evento recibido"}


@app.post("/upload")
async def upload_file(file: UploadFile = File(...)) -> dict:
    """Sube archivo y devuelve metadatos básicos."""
    content = await file.read()
    return {
        "filename": file.filename,
        "content_type": file.content_type,
        "size": len(content),
    }
```

## 5. Sección de Prácticas

1. Agrega endpoint `/event/custom` que reciba un mensaje por body y lo guarde en log.
2. En `/upload`, valida que el tamaño máximo permitido sea 1 MB.

Revisa soluciones en: `08_funcionalidades_intermedias_soluciones.md`
