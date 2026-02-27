# TEMA: Prerequisitos de Python para FastAPI

Rol de esta guía: Senior Backend Developer y Mentor de FastAPI.

## 1. Introducción y Concepto

Antes de usar FastAPI, debes dominar piezas básicas de Python: funciones, type hints, clases, excepciones y `async/await`.

¿Por qué importa?

- FastAPI usa type hints para validar datos automáticamente.
- Si no manejas excepciones, tus endpoints devuelven errores confusos.
- Si no entiendes `async/await`, luego cuesta leer código real.

Problema que resuelve este tema: tener una base sólida para no aprender FastAPI "de memoria".

## 2. Escenario de Aplicación

Vamos a crear una mini lógica de negocio: calcular el total de una compra con IVA y validar que el subtotal sea válido.

Esto simula lo que haría un backend antes de guardar una orden.

## 3. Explicación Paso a Paso (Línea por Línea)

### Bloque A: función con type hints

```python
def calcular_total(subtotal: float, iva: float = 0.16) -> float:
    return subtotal * (1 + iva)
```

- `def calcular_total(...)`: defines una función reutilizable.
- `subtotal: float`: type hint; espera un número decimal.
- `iva: float = 0.16`: valor por defecto (16%).
- `-> float`: la función debe regresar `float`.
- `return ...`: devuelve el total con IVA.

### Bloque B: validación con excepción

```python
def validar_subtotal(subtotal: float) -> None:
    if subtotal <= 0:
        raise ValueError("El subtotal debe ser mayor a 0")
```

- `validar_subtotal(...)`: separa la regla de negocio.
- `if subtotal <= 0`: detecta dato inválido.
- `raise ValueError(...)`: falla temprano con mensaje claro.

### Bloque C: clase simple para agrupar comportamiento

```python
class OrderService:
    def crear_resumen(self, subtotal: float) -> dict:
        validar_subtotal(subtotal)
        total = calcular_total(subtotal)
        return {"subtotal": subtotal, "total": total}
```

- `class OrderService`: organiza lógica de negocio.
- `crear_resumen(...)`: método principal del servicio.
- `validar_subtotal(...)`: aplica regla antes de calcular.
- `total = ...`: resultado intermedio claro.
- `return {...}`: devuelve un objeto listo para API.

### Bloque D: async básico

```python
import asyncio


async def enviar_notificacion() -> str:
    await asyncio.sleep(0.1)
    return "Notificación enviada"
```

- `async def`: función asíncrona.
- `await asyncio.sleep(...)`: simula espera de red/DB sin bloquear.
- `return ...`: respuesta final del proceso async.

## 4. Código Completo

Archivo sugerido: `python_basics_demo.py`

```python
import asyncio


def calcular_total(subtotal: float, iva: float = 0.16) -> float:
    """Calcula el total de una orden aplicando IVA."""
    return subtotal * (1 + iva)


def validar_subtotal(subtotal: float) -> None:
    """Valida que el subtotal sea mayor a cero."""
    if subtotal <= 0:
        raise ValueError("El subtotal debe ser mayor a 0")


class OrderService:
    """Servicio de negocio para preparar el resumen de una orden."""

    def crear_resumen(self, subtotal: float) -> dict:
        validar_subtotal(subtotal)
        total = calcular_total(subtotal)
        return {"subtotal": subtotal, "total": total}


async def enviar_notificacion() -> str:
    """Simula una operación de I/O asincrónica."""
    await asyncio.sleep(0.1)
    return "Notificación enviada"


async def main() -> None:
    service = OrderService()

    try:
        resumen = service.crear_resumen(100.0)
        print(resumen)

        mensaje = await enviar_notificacion()
        print(mensaje)
    except ValueError as exc:
        print(f"Error de negocio: {exc}")


if __name__ == "__main__":
    asyncio.run(main())
```

## 5. Sección de Prácticas

1. Cambia `calcular_total` para que acepte descuento (porcentaje) y lo aplique antes del IVA.
2. Agrega un método `crear_resumen_detallado` que incluya: subtotal, IVA usado, descuento y total final.

Revisa soluciones en: `01_prerequisitos_python_soluciones.md`
