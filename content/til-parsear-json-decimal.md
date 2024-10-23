---                                                                             
title: "TIL: Parsear un json sin perder precisión decimal"
date: 2024-10-23T17:35:00+02:00
type: 'post'
layout: 'single'
tags: ['til', 'python', 'decimal', 'spanish']
images: ["https://enriquesoria.github.io/avatar-small.jpg"]
draft: false
---

Si trabajas con precios probablemente conozcas el tipo `Decimal` de python. Esto es algo que en un trabajo como el mío (un ecommerce) es muy importante.

Recientemente encontramos un bug en el que la confirmación de pago que nos llegó de nuestro proveedor difería por un céntimo de lo esperado. Esto nos resultó extraño ya que a través de todo nuestro proyecto trabajamos con el tipo `Decimal` siempre que trabajamos con precios. 

Pero se nos escapó en un sitio: la función `json.parse`, que por defecto convierte los números en `float`s. Para solucionarlo tan solo hay que pasarle el parámetro opcional `parse_float=Decimal`:

```pycon
>>> import json
>>> data = '{"amount": 160.39}'
>>> json.loads(data)["amount"] * 100
16038.999999999998
>>> json.loads(data, parse_float=Decimal)["amount"] * 100
Decimal('16039.00')
```
