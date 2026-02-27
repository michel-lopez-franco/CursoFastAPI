# Soluciones Sugeridas - Tema 10

## Ejercicio 1

```python
def test_duplicar() -> None:
    response = client.post("/duplicar", json={"valor": 4})
    assert response.status_code == 200
    assert response.json()["doble"] == 8
```

## Ejercicio 2

```python
def test_duplicar_tipo_invalido() -> None:
    response = client.post("/duplicar", json={"valor": "texto"})
    assert response.status_code == 422
```
