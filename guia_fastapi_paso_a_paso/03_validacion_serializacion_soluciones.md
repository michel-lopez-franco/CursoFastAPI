# Soluciones Sugeridas - Tema 03

## Ejercicio 1

```python
class ProductCreate(BaseModel):
    name: str = Field(min_length=2, max_length=50)
    description: str | None = Field(default=None, max_length=120)
    price: float = Field(gt=0)
    stock: int = Field(ge=0)
```

## Ejercicio 2

```python
class ProductResponse(BaseModel):
    id: int
    name: str
    price: float
    stock: int
    is_available: bool
    price_with_tax: float


@app.post("/products", response_model=ProductResponse)
def create_product(payload: ProductCreate) -> ProductResponse:
    return ProductResponse(
        id=1,
        name=payload.name,
        price=payload.price,
        stock=payload.stock,
        is_available=payload.stock > 0,
        price_with_tax=payload.price * 1.16,
    )
```
