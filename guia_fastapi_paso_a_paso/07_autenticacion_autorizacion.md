# TEMA: Autenticación y Autorización

Rol de esta guía: Senior Backend Developer y Mentor de FastAPI.

## 1. Introducción y Concepto

Autenticación: verificar quién eres.
Autorización: verificar qué puedes hacer.

¿Por qué importa?

- Proteges datos sensibles.
- Evitas acceso no permitido.

Problema que resuelve: exponer endpoints seguros para usuarios correctos.

## 2. Escenario de Aplicación

Vamos a implementar:

- Login que entrega JWT.
- Endpoint protegido `/me`.
- Endpoint admin protegido por rol.

## 3. Explicación Paso a Paso (Línea por Línea)

### Bloque A: crear token

```python
from datetime import UTC, datetime, timedelta
import jwt


def create_access_token(email: str, role: str) -> str:
    payload = {
        "sub": email,
        "role": role,
        "exp": datetime.now(UTC) + timedelta(minutes=30),
    }
    return jwt.encode(payload, "secret-key", algorithm="HS256")
```

- `sub`: sujeto autenticado.
- `role`: permiso del usuario.
- `exp`: expiración por seguridad.

### Bloque B: leer token

```python
from fastapi.security import OAuth2PasswordBearer

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/login")
```

- FastAPI espera token tipo Bearer.
- `tokenUrl` indica endpoint de login.

### Bloque C: endpoint protegido

```python
@app.get("/me")
def me(token: str = Depends(oauth2_scheme)) -> dict:
    payload = jwt.decode(token, "secret-key", algorithms=["HS256"])
    return {"email": payload["sub"], "role": payload["role"]}
```

- `Depends(oauth2_scheme)`: extrae token del header.
- `jwt.decode(...)`: valida firma y expiración.

## 4. Código Completo

Archivo sugerido: `main.py`

```python
from datetime import UTC, datetime, timedelta
from fastapi import Depends, FastAPI, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
import jwt

app = FastAPI(title="API Auth")
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/login")

SECRET_KEY = "secret-key"
ALGORITHM = "HS256"

FAKE_USERS = {
    "ana@example.com": {"password": "1234", "role": "admin"},
    "luis@example.com": {"password": "abcd", "role": "user"},
}


def create_access_token(email: str, role: str) -> str:
    """Genera JWT de acceso."""
    payload = {
        "sub": email,
        "role": role,
        "exp": datetime.now(UTC) + timedelta(minutes=30),
    }
    return jwt.encode(payload, SECRET_KEY, algorithm=ALGORITHM)


def get_current_user(token: str = Depends(oauth2_scheme)) -> dict:
    """Decodifica token y devuelve usuario autenticado."""
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        return {"email": payload["sub"], "role": payload["role"]}
    except jwt.PyJWTError as exc:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Token inválido o expirado",
        ) from exc


def require_admin(user: dict = Depends(get_current_user)) -> dict:
    """Permite acceso solo a rol admin."""
    if user["role"] != "admin":
        raise HTTPException(status_code=status.HTTP_403_FORBIDDEN, detail="Sin permisos")
    return user


@app.post("/login")
def login(form_data: OAuth2PasswordRequestForm = Depends()) -> dict:
    """Valida usuario y regresa access token."""
    user = FAKE_USERS.get(form_data.username)
    if user is None or user["password"] != form_data.password:
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Credenciales inválidas")

    token = create_access_token(form_data.username, user["role"])
    return {"access_token": token, "token_type": "bearer"}


@app.get("/me")
def me(user: dict = Depends(get_current_user)) -> dict:
    return user


@app.get("/admin")
def admin_panel(user: dict = Depends(require_admin)) -> dict:
    return {"message": "Bienvenido al panel admin", "user": user}
```

## 5. Sección de Prácticas

1. Agrega un endpoint `/user` que permita cualquier usuario autenticado.
2. Cambia expiración del token a 10 minutos y prueba login otra vez.

Revisa soluciones en: `07_autenticacion_autorizacion_soluciones.md`
