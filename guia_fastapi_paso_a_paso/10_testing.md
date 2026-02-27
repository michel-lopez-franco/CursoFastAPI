# TEMA: Testing en FastAPI

Rol de esta guía: Senior Backend Developer y Mentor de FastAPI.

## 1. Introducción y Concepto

Testing verifica que tu API siga funcionando después de cambios.

¿Por qué importa?

- Evitas regresiones.
- Puedes refactorizar con confianza.

Problema que resuelve: detectar errores antes de producción.

## 2. Escenario de Aplicación

Probaremos dos endpoints:

- `GET /health`
- `GET /sumar?a=2&b=3`

## 3. Explicación Paso a Paso (Línea por Línea)

### Bloque A: cliente de pruebas

```python
from fastapi.testclient import TestClient
from app.main import app

client = TestClient(app)
```

- `TestClient` levanta la app en memoria.
- No necesitas iniciar Uvicorn para probar.

### Bloque B: test de salud

```python
def test_health() -> None:
    response = client.get("/health")
    assert response.status_code == 200
    assert response.json() == {"status": "ok"}
```

- `client.get(...)`: simula request real.
- `assert`: valida estado y contenido.

### Bloque C: test con query params

```python
def test_sumar() -> None:
    response = client.get("/sumar", params={"a": 2, "b": 3})
    assert response.status_code == 200
    assert response.json()["resultado"] == 5
```

- `params={...}` envía query parameters.
- Validas solo el campo importante.

## 4. Código Completo

Archivo sugerido: `tests/test_main.py`

```python
from fastapi.testclient import TestClient
from main import app

client = TestClient(app)


def test_health() -> None:
    """Verifica endpoint de salud."""
    response = client.get("/health")
    assert response.status_code == 200
    assert response.json() == {"status": "ok"}


def test_sumar() -> None:
    """Verifica suma correcta de query params."""
    response = client.get("/sumar", params={"a": 2, "b": 3})
    assert response.status_code == 200
    assert response.json()["resultado"] == 5


def test_sumar_tipo_invalido() -> None:
    """Verifica validación automática de tipos."""
    response = client.get("/sumar", params={"a": "hola", "b": 3})
    assert response.status_code == 422
```

## 5. Sección de Prácticas

1. Crea test para `POST /duplicar`.
2. Crea test negativo para `POST /duplicar` enviando texto en lugar de entero.

Revisa soluciones en: `10_testing_soluciones.md`
