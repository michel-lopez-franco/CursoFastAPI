# Soluciones Sugeridas - Tema 12

## Ejercicio 1

```yaml
services:
  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      - APP_ENV=dev
```

## Ejercicio 2

```yaml
services:
  api:
    build: .
    ports:
      - "8000:8000"
    command: uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```
