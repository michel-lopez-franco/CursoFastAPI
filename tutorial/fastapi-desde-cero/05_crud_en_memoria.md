# 05 - CRUD en memoria

Este modulo construye un CRUD simple sin base de datos, usando una lista en memoria.

## Codigo completo (`main.py`)

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel, Field

app = FastAPI()


class TareaIn(BaseModel):
    titulo: str = Field(min_length=3, max_length=100)
    completada: bool = False


class Tarea(TareaIn):
    id: int


tareas: list[Tarea] = []
next_id = 1


@app.get("/tareas", response_model=list[Tarea])
def listar_tareas():
    return tareas


@app.post("/tareas", response_model=Tarea, status_code=201)
def crear_tarea(tarea_in: TareaIn):
    global next_id
    tarea = Tarea(id=next_id, **tarea_in.model_dump())
    tareas.append(tarea)
    next_id += 1
    return tarea


@app.get("/tareas/{tarea_id}", response_model=Tarea)
def obtener_tarea(tarea_id: int):
    for tarea in tareas:
        if tarea.id == tarea_id:
            return tarea
    raise HTTPException(status_code=404, detail="Tarea no encontrada")


@app.put("/tareas/{tarea_id}", response_model=Tarea)
def actualizar_tarea(tarea_id: int, tarea_in: TareaIn):
    for i, tarea in enumerate(tareas):
        if tarea.id == tarea_id:
            tareas[i] = Tarea(id=tarea_id, **tarea_in.model_dump())
            return tareas[i]
    raise HTTPException(status_code=404, detail="Tarea no encontrada")


@app.delete("/tareas/{tarea_id}", status_code=204)
def eliminar_tarea(tarea_id: int):
    for i, tarea in enumerate(tareas):
        if tarea.id == tarea_id:
            tareas.pop(i)
            return
    raise HTTPException(status_code=404, detail="Tarea no encontrada")
```

## Pruebas manuales en Swagger

1. `POST /tareas`
2. `GET /tareas`
3. `GET /tareas/{id}`
4. `PUT /tareas/{id}`
5. `DELETE /tareas/{id}`

## Limitacion importante

Los datos se pierden al reiniciar el servidor. Por eso el siguiente paso es base de datos.
