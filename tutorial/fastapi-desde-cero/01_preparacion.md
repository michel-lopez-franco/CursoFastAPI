# 01 - Preparacion del entorno

## 1. Crear carpeta del proyecto

```bash
mkdir mi_api_fastapi
cd mi_api_fastapi
```

## 2. Crear y activar entorno virtual

### macOS / Linux

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### Windows (PowerShell)

```powershell
py -m venv .venv
.venv\Scripts\Activate.ps1
```

## 3. Instalar dependencias iniciales

```bash
pip install fastapi uvicorn
```

## 4. Verificar instalacion

```bash
python -c "import fastapi; print(fastapi.__version__)"
```

Si imprime una version, FastAPI esta listo.

## 5. Crear archivo principal

```bash
touch main.py
```

## Checklist rapido

- Entorno virtual activo
- FastAPI instalado
- Uvicorn instalado
- Archivo `main.py` creado

## Ejercicio

Instala tambien `httpx` para pruebas desde Python:

```bash
pip install httpx
```
