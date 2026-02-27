# TEMA: Inyección de Dependencias en FastAPI

Rol de esta guía: Senior Backend Developer y Mentor de FastAPI.

## 1. Introducción y Concepto

Las dependencias (`Depends`) permiten reutilizar lógica común:

- autenticación,
- acceso a base de datos,
- lectura de configuración.

¿Por qué importa?

- Evitas duplicar código en cada endpoint.
- Tu API queda modular y testeable.

## 2. Escenario de Aplicación

Vamos a proteger un endpoint de perfil con una API key enviada en headers.

## 3. Explicación Paso a Paso (Línea por Línea)

### Bloque A: dependencia de API key

```python
from fastapi import Header, HTTPException, status


def get_api_key(x_api_key: str = Header(...)) -> str:
    if x_api_key != "dev-key-123":
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="API key inválida")
    return x_api_key
```

- `Header(...)`: obliga a enviar el header.
- `if ... != ...`: validación simple de seguridad.
- `HTTPException`: devuelve un error HTTP claro.

### Bloque B: usar `Depends`

```python
from fastapi import Depends


@app.get("/profile")
def profile(_: str = Depends(get_api_key)) -> dict:
    return {"message": "Perfil autorizado"}
```

- `Depends(get_api_key)`: ejecuta la dependencia antes del endpoint.
- `_`: no usamos el valor, solo su validación.

### Bloque C: dependencia con `yield`

```python
from collections.abc import Generator


def get_fake_db() -> Generator[dict, None, None]:
    db = {"connection": "open"}
    try:
        yield db
    finally:
        db["connection"] = "closed"
```

- `yield`: entrega recurso durante el request.
- `finally`: cleanup automático al finalizar.

## 4. Código Completo

Archivo sugerido: `main.py`

```python
from collections.abc import Generator
from fastapi import Depends, FastAPI, Header, HTTPException, status

app = FastAPI(title="API con Dependencias")


def get_api_key(x_api_key: str = Header(...)) -> str:
    """Valida una API key enviada por header."""
    if x_api_key != "dev-key-123":
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="API key inválida",
        )
    return x_api_key


def get_fake_db() -> Generator[dict, None, None]:
    """Simula apertura y cierre de conexión."""
    db = {"connection": "open"}
    try:
        yield db
    finally:
        db["connection"] = "closed"


@app.get("/profile")
def profile(
    _: str = Depends(get_api_key),
    db: dict = Depends(get_fake_db),
) -> dict:
    """Endpoint protegido por dependencia."""
    return {"message": "Perfil autorizado", "db": db["connection"]}
```

## 5. Sección de Prácticas

1. Crea un endpoint `/admin` que también use `get_api_key`.
2. Cambia `get_api_key` para aceptar dos llaves válidas: `dev-key-123` y `mentor-key-456`.

Revisa soluciones en: `04_dependencias_soluciones.md`
