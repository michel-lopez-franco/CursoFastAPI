# 07 - Autenticacion basica con JWT

## 1. Instalar dependencias

```bash
pip install "python-jose[cryptography]" passlib[bcrypt] python-multipart
```

## 2. Concepto rapido

- El usuario inicia sesion con usuario y password.
- El backend devuelve un token JWT.
- El cliente envia ese token en `Authorization: Bearer <token>`.

## 3. Ejemplo simplificado

```python
from datetime import datetime, timedelta, timezone
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from jose import jwt, JWTError

SECRET_KEY = "cambia-esta-clave"
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30

app = FastAPI()
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

fake_user = {"username": "admin", "password": "123456"}


def crear_access_token(data: dict):
    to_encode = data.copy()
    expire = datetime.now(timezone.utc) + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)


@app.post("/token")
def login(form_data: OAuth2PasswordRequestForm = Depends()):
    if form_data.username != fake_user["username"] or form_data.password != fake_user["password"]:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Credenciales invalidas",
        )
    token = crear_access_token({"sub": form_data.username})
    return {"access_token": token, "token_type": "bearer"}


@app.get("/perfil")
def perfil(token: str = Depends(oauth2_scheme)):
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        username = payload.get("sub")
        if not username:
            raise ValueError("Token invalido")
        return {"usuario": username}
    except (JWTError, ValueError):
        raise HTTPException(status_code=401, detail="Token invalido")
```

## 4. Recomendaciones

- Nunca guardar passwords en texto plano.
- Hashear passwords con `passlib`.
- Mover `SECRET_KEY` a variable de entorno.
- Expiracion corta para access token.
