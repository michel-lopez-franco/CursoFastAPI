# Soluciones Sugeridas - Tema 08

## Ejercicio 1

```python
from pydantic import BaseModel


class EventInput(BaseModel):
    message: str


@app.post("/event/custom")
def create_custom_event(payload: EventInput, background_tasks: BackgroundTasks) -> dict:
    background_tasks.add_task(log_event, payload.message)
    return {"message": "Evento personalizado recibido"}
```

## Ejercicio 2

```python
from fastapi import HTTPException, status


@app.post("/upload")
async def upload_file(file: UploadFile = File(...)) -> dict:
    content = await file.read()

    if len(content) > 1024 * 1024:
        raise HTTPException(
            status_code=status.HTTP_413_REQUEST_ENTITY_TOO_LARGE,
            detail="Archivo demasiado grande",
        )

    return {"filename": file.filename, "size": len(content)}
```
