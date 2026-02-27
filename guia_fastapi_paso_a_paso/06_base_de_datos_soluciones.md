# Soluciones Sugeridas - Tema 06

## Ejercicio 1

```python
class NoteORM(Base):
    __tablename__ = "notes"
    id: Mapped[int] = mapped_column(Integer, primary_key=True, index=True)
    title: Mapped[str] = mapped_column(String(100), nullable=False)
    content: Mapped[str] = mapped_column(String(300), nullable=False)


class NoteCreate(BaseModel):
    title: str = Field(min_length=2, max_length=100)
    content: str = Field(min_length=1, max_length=300)
```

## Ejercicio 2

```python
from fastapi import HTTPException, status


@app.delete("/notes/{note_id}")
def delete_note(note_id: int, db: Session = Depends(get_db)) -> dict:
    note = db.query(NoteORM).filter(NoteORM.id == note_id).first()
    if note is None:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail="Nota no encontrada")

    db.delete(note)
    db.commit()
    return {"message": "Nota eliminada"}
```
