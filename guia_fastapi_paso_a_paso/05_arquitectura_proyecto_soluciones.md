# Soluciones Sugeridas - Tema 05

## Ejercicio 1

```python
class TaskRepository:
    def list_all(self) -> list[dict]:
        return [
            {"id": 1, "title": "Estudiar FastAPI", "done": False},
            {"id": 2, "title": "Practicar endpoints", "done": True},
        ]


class TaskService:
    def list_tasks(self) -> list[dict]:
        return self.repo.list_all()
```

## Ejercicio 2

```python
@router.get("")
def list_tasks() -> list[dict]:
    service = TaskService(TaskRepository())
    return service.list_tasks()
```
