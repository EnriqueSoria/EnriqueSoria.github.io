---                                                                             
title: "TIL: Parsear un json sin perder precisión decimal"
date: 2024-10-23T17:35:00+02:00
type: 'post'
layout: 'single'
tags: ['til', 'python', 'decimal', 'spanish']
images: ["https://github.com/user-attachments/assets/71a50374-0ae6-43ef-bcf1-8d842c11e933"]
draft: false
---

Recientemente encontramos un bug en el que la confirmación de pago que nos llegó de nuestro proveedor difería por un céntimo de lo esperado. Esto nos resultó extraño ya que a través de todo nuestro proyecto trabajamos con el tipo [`Decimal`](https://docs.python.org/3/library/decimal.html) siempre que trabajamos con precios. 

Pero se nos escapó en un sitio: la función [`json.loads`](https://docs.python.org/3/library/json.html#json.loads), que por defecto convierte los números en `float`s. Para solucionarlo tan solo hay que pasarle el parámetro opcional `parse_float=Decimal`:

```python
>>> import json
>>> data = '{"amount": 160.39}'
>>> json.loads(data)["amount"] * 100
16038.999999999998
>>> json.loads(data, parse_float=Decimal)["amount"] * 100
Decimal('16039.00')
```
