# TEMA: Despliegue (Docker + CI Básico)

Rol de esta guía: Senior Backend Developer y Mentor de FastAPI.

## 1. Introducción y Concepto

Desplegar es llevar tu API de local a un entorno reproducible y compartible.

¿Por qué importa?

- Tu app corre igual en tu máquina y en servidor.
- Facilita trabajo en equipo y CI/CD.

Problema que resuelve: evitar "en mi laptop sí funciona".

## 2. Escenario de Aplicación

Empaquetaremos una API FastAPI en Docker y añadiremos un pipeline mínimo de tests.

## 3. Explicación Paso a Paso (Línea por Línea)

### Bloque A: Dockerfile

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

- `FROM`: imagen base de Python.
- `WORKDIR`: carpeta de trabajo en contenedor.
- `COPY requirements.txt`: paso separado para cache eficiente.
- `RUN pip install ...`: instala dependencias.
- `CMD ...`: comando de arranque del servidor.

### Bloque B: docker compose

```yaml
services:
  api:
    build: .
    ports:
      - "8000:8000"
```

- `build: .`: construye desde Dockerfile local.
- `ports`: expone API al host.

### Bloque C: CI simple

```yaml
- run: pip install -r requirements.txt
- run: pytest -q
```

- Instala dependencias.
- Ejecuta pruebas en cada push/PR.

## 4. Código Completo

Archivo sugerido: `Dockerfile`

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

# Inicia FastAPI en el puerto 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

Archivo sugerido: `docker-compose.yml`

```yaml
services:
  api:
    build: .
    ports:
      - "8000:8000"
```

Archivo sugerido: `.github/workflows/ci.yml`

```yaml
name: CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      - run: pip install -r requirements.txt
      - run: pytest -q
```

## 5. Sección de Prácticas

1. Modifica `docker-compose.yml` para usar variable de entorno `APP_ENV=dev`.
2. Cambia el comando de arranque para usar `--reload` en desarrollo.

Revisa soluciones en: `12_despliegue_soluciones.md`
