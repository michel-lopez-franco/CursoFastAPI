# Soluciones Sugeridas - Tema 01

## Ejercicio 1

```python
def calcular_total(subtotal: float, iva: float = 0.16, descuento: float = 0.0) -> float:
    subtotal_con_descuento = subtotal * (1 - descuento)
    return subtotal_con_descuento * (1 + iva)
```

## Ejercicio 2

```python
class OrderService:
    def crear_resumen_detallado(
        self,
        subtotal: float,
        iva: float = 0.16,
        descuento: float = 0.0,
    ) -> dict:
        validar_subtotal(subtotal)
        total = calcular_total(subtotal, iva=iva, descuento=descuento)
        return {
            "subtotal": subtotal,
            "iva": iva,
            "descuento": descuento,
            "total": total,
        }
```
