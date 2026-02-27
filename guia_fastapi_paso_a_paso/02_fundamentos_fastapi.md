# TEMA: Fundamentos de FastAPI

Rol de esta guía: Senior Backend Developer y Mentor de FastAPI.

## 1. Introducción y Concepto

FastAPI es un framework para crear APIs rápidas y tipadas con Python.

¿Por qué importa?

- Te permite construir endpoints claros y mantenibles.
- Genera documentación automática (`/docs`).
- Usa type hints para validar entrada y salida.

Problema que resuelve: crear una API funcional sin mucho código repetitivo.

## 2. Escenario de Aplicación

Vamos a crear una API de calculadora simple:

- `GET /health`: verificar que el servidor vive.
- `GET /sumar`: sumar dos números por query params.
- `POST /duplicar`: recibir un número y regresar su doble.

## 3. Explicación Paso a Paso (Línea por Línea)

### Bloque A: crear la aplicación

```python
from fastapi import FastAPI

app = FastAPI(title="API Calculadora")
```

- `from fastapi import FastAPI`: importa la clase principal.
- `app = FastAPI(...)`: instancia la app con metadata.

### Bloque B: endpoint GET simple

```python
@app.get("/health")
def health() -> dict:
    return {"status": "ok"}
```

- `@app.get("/health")`: ruta HTTP GET.
- `def health() -> dict`: endpoint síncrono con tipo de retorno.
- `return {...}`: respuesta JSON.

### Bloque C: query parameters

```python
@app.get("/sumar")
def sumar(a: int, b: int) -> dict:
    return {"resultado": a + b}
```

- `a: int, b: int`: FastAPI los toma de query params.
- Si envías texto en lugar de número, FastAPI devuelve error 422.
- `a + b`: lógica mínima del endpoint.

### Bloque D: body con Pydantic

```python
from pydantic import BaseModel


class NumeroEntrada(BaseModel):
    valor: int
```

- `BaseModel`: define el esquema esperado en el body.
- `valor: int`: campo obligatorio de tipo entero.

## 4. Código Completo

Archivo sugerido: `main.py`

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI(title="API Calculadora")


class NumeroEntrada(BaseModel):
    """Schema para recibir un número entero por body."""

    valor: int


@app.get("/health")
def health() -> dict:
    """Endpoint de salud para monitoreo básico."""
    return {"status": "ok"}


@app.get("/sumar")
def sumar(a: int, b: int) -> dict:
    """Suma dos enteros recibidos por query params."""
    return {"resultado": a + b}


@app.post("/duplicar")
def duplicar(payload: NumeroEntrada) -> dict:
    """Duplica un valor recibido por body JSON."""
    return {"original": payload.valor, "doble": payload.valor * 2}
```

## 5. Sección de Prácticas

1. Crea un endpoint `GET /restar` con dos query params `a` y `b`.
2. Crea un endpoint `POST /cuadrado` que reciba `{ "valor": 5 }` y responda con el cuadrado.

Revisa soluciones en: `02_fundamentos_fastapi_soluciones.md`
