
```python
# stores.py
DEFAULT_STORE_CODE = "main_store"

def get_current_store() -> Store | None:
    store = Store.objects.filter(...).first()
    if store:
        return store
    return Store.objects.filter(code=DEFAULT_STORE_CODE).first()
```

```python 
# tax_rates.py
DEFAULT_TAX_RATE = Decimal("21.00")

def get_vat_rate(product: Product) -> Decimal:
    store = get_current_store()
    if store and store.tax_rate:
        return store.tax_rate
    return DEFAULT_TAX_RATE
```




## bibliography
 - https://luzkan.github.io/smells/afraid-to-fail
