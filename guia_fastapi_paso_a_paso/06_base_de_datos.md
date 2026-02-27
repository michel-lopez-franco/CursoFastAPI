# TEMA: Base de Datos con SQLAlchemy (Inicio)

Rol de esta guía: Senior Backend Developer y Mentor de FastAPI.

## 1. Introducción y Concepto

FastAPI no trae ORM integrado. SQLAlchemy es una opción muy usada para mapear tablas a clases Python.

¿Por qué importa?

- Persistes datos reales.
- Separas lógica de API y almacenamiento.

Problema que resuelve: dejar de usar datos en memoria y guardar en BD.

## 2. Escenario de Aplicación

Crearemos una API de notas con SQLite (más simple para empezar):

- Crear nota.
- Listar notas.

## 3. Explicación Paso a Paso (Línea por Línea)

### Bloque A: configuración de sesión

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, declarative_base

DATABASE_URL = "sqlite:///./notes.db"
engine = create_engine(DATABASE_URL, connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(bind=engine, autocommit=False, autoflush=False)
Base = declarative_base()
```

- `sqlite:///./notes.db`: archivo local de base de datos.
- `check_same_thread=False`: requerido en SQLite con FastAPI.
- `SessionLocal`: crea sesión por request.
- `Base`: clase base para modelos ORM.

### Bloque B: modelo ORM

```python
from sqlalchemy import Integer, String
from sqlalchemy.orm import Mapped, mapped_column


class NoteORM(Base):
    __tablename__ = "notes"
    id: Mapped[int] = mapped_column(Integer, primary_key=True, index=True)
    title: Mapped[str] = mapped_column(String(100), nullable=False)
```

- `__tablename__`: nombre de tabla.
- `mapped_column(...)`: columnas y restricciones.

### Bloque C: dependencia de DB

```python
from collections.abc import Generator


def get_db() -> Generator:
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

- `yield db`: comparte sesión con endpoint.
- `db.close()`: libera conexión al terminar.

## 4. Código Completo

Archivo sugerido: `main.py`

```python
from collections.abc import Generator
from fastapi import Depends, FastAPI
from pydantic import BaseModel, Field
from sqlalchemy import Integer, String, create_engine
from sqlalchemy.orm import Mapped, Session, declarative_base, mapped_column, sessionmaker

app = FastAPI(title="API Notas")

DATABASE_URL = "sqlite:///./notes.db"
engine = create_engine(DATABASE_URL, connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(bind=engine, autocommit=False, autoflush=False)
Base = declarative_base()


class NoteORM(Base):
    """Modelo ORM para persistencia."""

    __tablename__ = "notes"

    id: Mapped[int] = mapped_column(Integer, primary_key=True, index=True)
    title: Mapped[str] = mapped_column(String(100), nullable=False)


class NoteCreate(BaseModel):
    """Schema de entrada para crear una nota."""

    title: str = Field(min_length=2, max_length=100)


class NoteResponse(BaseModel):
    """Schema de salida para devolver una nota."""

    id: int
    title: str


Base.metadata.create_all(bind=engine)


def get_db() -> Generator[Session, None, None]:
    """Dependencia de sesión por request."""
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()


@app.post("/notes", response_model=NoteResponse)
def create_note(payload: NoteCreate, db: Session = Depends(get_db)) -> NoteResponse:
    note = NoteORM(title=payload.title)
    db.add(note)
    db.commit()
    db.refresh(note)
    return NoteResponse(id=note.id, title=note.title)


@app.get("/notes", response_model=list[NoteResponse])
def list_notes(db: Session = Depends(get_db)) -> list[NoteResponse]:
    notes = db.query(NoteORM).all()
    return [NoteResponse(id=note.id, title=note.title) for note in notes]
```

## 5. Sección de Prácticas

1. Agrega campo `content` en el modelo y schemas.
2. Crea endpoint `DELETE /notes/{note_id}`.

Revisa soluciones en: `06_base_de_datos_soluciones.md`
