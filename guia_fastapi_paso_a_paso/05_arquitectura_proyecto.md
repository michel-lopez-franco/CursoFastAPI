# TEMA: Arquitectura y Estructura de Proyecto

Rol de esta guía: Senior Backend Developer y Mentor de FastAPI.

## 1. Introducción y Concepto

Cuando todo está en `main.py`, el proyecto se vuelve difícil de mantener.

Este tema enseña separación por capas:

- `routes`: HTTP.
- `services`: reglas de negocio.
- `repositories`: acceso a datos.

Problema que resuelve: evitar archivos gigantes y facilitar pruebas.

## 2. Escenario de Aplicación

Tendremos un módulo de tareas con una ruta que consulta una tarea por ID, pero dividida por capas.

## 3. Explicación Paso a Paso (Línea por Línea)

### Bloque A: estructura de carpetas

```text
app/
  main.py
  routes/tasks.py
  services/task_service.py
  repositories/task_repository.py
```

- `main.py`: punto de entrada.
- `routes/`: define endpoints.
- `services/`: lógica de negocio.
- `repositories/`: lectura/escritura de datos.

### Bloque B: repositorio

```python
class TaskRepository:
    def get_by_id(self, task_id: int) -> dict | None:
        fake_db = {1: {"id": 1, "title": "Estudiar FastAPI"}}
        return fake_db.get(task_id)
```

- Repositorio no sabe de HTTP, solo de datos.
- `dict | None`: puede encontrar o no encontrar registro.

### Bloque C: servicio

```python
class TaskService:
    def __init__(self, repo: TaskRepository) -> None:
        self.repo = repo

    def get_task(self, task_id: int) -> dict:
        task = self.repo.get_by_id(task_id)
        if task is None:
            raise ValueError("Tarea no encontrada")
        return task
```

- Servicio aplica reglas de negocio.
- Si no existe tarea, lanza error de negocio (no HTTP).

### Bloque D: ruta

```python
@router.get("/{task_id}")
def get_task(task_id: int) -> dict:
    service = TaskService(TaskRepository())
    return service.get_task(task_id)
```

- Ruta conecta HTTP con servicio.
- Mantener endpoint corto mejora legibilidad.

## 4. Código Completo

Archivo sugerido: `app/routes/tasks.py`

```python
from fastapi import APIRouter, HTTPException, status

router = APIRouter(prefix="/tasks", tags=["tasks"])


class TaskRepository:
    """Acceso a datos (simulado)."""

    def get_by_id(self, task_id: int) -> dict | None:
        fake_db = {
            1: {"id": 1, "title": "Estudiar FastAPI", "done": False},
            2: {"id": 2, "title": "Practicar endpoints", "done": True},
        }
        return fake_db.get(task_id)


class TaskService:
    """Reglas de negocio del módulo de tareas."""

    def __init__(self, repo: TaskRepository) -> None:
        self.repo = repo

    def get_task(self, task_id: int) -> dict:
        task = self.repo.get_by_id(task_id)
        if task is None:
            raise ValueError("Tarea no encontrada")
        return task


@router.get("/{task_id}")
def get_task(task_id: int) -> dict:
    """Endpoint HTTP para consultar una tarea."""
    service = TaskService(TaskRepository())

    try:
        return service.get_task(task_id)
    except ValueError as exc:
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail=str(exc),
        ) from exc
```

## 5. Sección de Prácticas

1. Agrega método `list_all` en repositorio y servicio para devolver todas las tareas.
2. Crea endpoint `GET /tasks` que use ese método.

Revisa soluciones en: `05_arquitectura_proyecto_soluciones.md`
