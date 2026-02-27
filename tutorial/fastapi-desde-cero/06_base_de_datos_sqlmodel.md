# 06 - Base de datos con SQLModel (SQLite)

## 1. Instalar dependencia

```bash
pip install sqlmodel
```

## 2. Modelo y conexion

```python
from typing import Optional
from fastapi import FastAPI, Depends, HTTPException
from sqlmodel import Field, Session, SQLModel, create_engine, select

app = FastAPI()


class Tarea(SQLModel, table=True):
    id: Optional[int] = Field(default=None, primary_key=True)
    titulo: str
    completada: bool = False


sqlite_url = "sqlite:///./tareas.db"
engine = create_engine(sqlite_url, echo=False)


def crear_tablas():
    SQLModel.metadata.create_all(engine)


def get_session():
    with Session(engine) as session:
        yield session


@app.on_event("startup")
def on_startup():
    crear_tablas()
```

## 3. CRUD con DB

```python
@app.post("/tareas", response_model=Tarea, status_code=201)
def crear_tarea(tarea: Tarea, session: Session = Depends(get_session)):
    session.add(tarea)
    session.commit()
    session.refresh(tarea)
    return tarea


@app.get("/tareas", response_model=list[Tarea])
def listar_tareas(session: Session = Depends(get_session)):
    return session.exec(select(Tarea)).all()


@app.get("/tareas/{tarea_id}", response_model=Tarea)
def obtener_tarea(tarea_id: int, session: Session = Depends(get_session)):
    tarea = session.get(Tarea, tarea_id)
    if not tarea:
        raise HTTPException(status_code=404, detail="Tarea no encontrada")
    return tarea
```

## 4. Mejor practica

Separa en modulos:

- `database.py` para engine y sesiones
- `models.py` para entidades
- `routers/` para endpoints

## Ejercicio

Implementa `PUT /tareas/{id}` y `DELETE /tareas/{id}` usando `Session`.
