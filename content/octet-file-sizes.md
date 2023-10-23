---                                                                             
title: 'Representar tamanys d'objectes sense números màgics'
date: 2023-10-23T19:42:00+01:00
type: 'post'
layout: 'single'
tags: ['refactoring', 'python', 'català']
images: ["[https://github.com/EnriqueSoria/EnriqueSoria.github.io/assets/7394684/e893e238-5d77-4f71-8f7a-d87362ce9cc7](https://ondemand.bannerbear.com/signedurl/D0nJ4XLedwbENRZa1x/image.jpg?modifications=W3sibmFtZSI6InJlcG8iLCJ0ZXh0IjoiRW5yaXF1ZVNvcmlhIC8gKm9jdGV0KiJ9LHsibmFtZSI6ImRlc2MiLCJ0ZXh0IjoiT2JqZWN0LW9yaWVudGVkIHdheSB0byByZXByZXNlbnQgZGlnaXRhbCBzaXplcyAoYml0cyBhbmQgYnl0ZXMpIn0seyJuYW1lIjoiYXZhdGFyNSIsImhpZGUiOnRydWV9LHsibmFtZSI6ImF2YXRhcjQiLCJoaWRlIjp0cnVlfSx7Im5hbWUiOiJhdmF0YXIzIiwiaGlkZSI6dHJ1ZX0seyJuYW1lIjoiYXZhdGFyMiIsImhpZGUiOnRydWV9LHsibmFtZSI6ImF2YXRhcjEiLCJpbWFnZV91cmwiOiJodHRwczovL2F2YXRhcnMuZ2l0aHVidXNlcmNvbnRlbnQuY29tL3UvNzM5NDY4ND92PTQifSx7Im5hbWUiOiJjb250cmlidXRvcnMiLCJ0ZXh0IjoiRW5yaXF1ZVNvcmlhIn0seyJuYW1lIjoic3RhcnMiLCJ0ZXh0IjoiMyJ9XQ&s=d48817b83267abb307111befdbbc74f68a8f9da4055adddbbde41e42b77b4262)"]
draft: false
---

Al igual que la millor forma de representar es la duració (en temps) es [evitant gastar números màgics](constants-duracio-temps-numero-magic-ttl.md) amb `datetime.timedelta()` , també deuriem poder expresar el tamany (bits i bytes) d'un fitxer d'una forma senzilla. Per això, i com que no vaig trobar cap llibreria que m'ho permetera, vaig decidir crear-la jo.

Com diria [el meme](https://twitter.com/David3141593/status/1366803872812068864): 

> This is REAL code written by REAL PROGRAMMERS:
```python
GiB_1_IN_BYTES: int = 1_073_741_824
MiB_200_IN_BYTES: int = 209_715_200
MiB_50_IN_BYTES: int = 52_428_800
MiB_30_IN_BYTES: int = 31_457_280
```

Amb [octet](https://github.com/EnriqueSoria/octet) el codi es podria reescriure d'esta manera (encara que de fet ja no caldria crear constants perquè es prou autoexplicatiu):
```python
from octet import GiB, MiB, Byte

GiB_1_IN_BYTES: int = GiB(1).convert_to(Byte).value
MiB_200_IN_BYTES: int = MiB(200).convert_to(Byte).value
MiB_50_IN_BYTES: int = MiB(50).convert_to(Byte).value
MiB_30_IN_BYTES: int = (30 * MiB()).convert_to(Byte).value
```

## Octet

[![A preview of EnriqueSoria/octet github repository](https://ondemand.bannerbear.com/signedurl/D0nJ4XLedwbENRZa1x/image.jpg?modifications=W3sibmFtZSI6InJlcG8iLCJ0ZXh0IjoiRW5yaXF1ZVNvcmlhIC8gKm9jdGV0KiJ9LHsibmFtZSI6ImRlc2MiLCJ0ZXh0IjoiT2JqZWN0LW9yaWVudGVkIHdheSB0byByZXByZXNlbnQgZGlnaXRhbCBzaXplcyAoYml0cyBhbmQgYnl0ZXMpIn0seyJuYW1lIjoiYXZhdGFyNSIsImhpZGUiOnRydWV9LHsibmFtZSI6ImF2YXRhcjQiLCJoaWRlIjp0cnVlfSx7Im5hbWUiOiJhdmF0YXIzIiwiaGlkZSI6dHJ1ZX0seyJuYW1lIjoiYXZhdGFyMiIsImhpZGUiOnRydWV9LHsibmFtZSI6ImF2YXRhcjEiLCJpbWFnZV91cmwiOiJodHRwczovL2F2YXRhcnMuZ2l0aHVidXNlcmNvbnRlbnQuY29tL3UvNzM5NDY4ND92PTQifSx7Im5hbWUiOiJjb250cmlidXRvcnMiLCJ0ZXh0IjoiRW5yaXF1ZVNvcmlhIn0seyJuYW1lIjoic3RhcnMiLCJ0ZXh0IjoiMyJ9XQ&s=d48817b83267abb307111befdbbc74f68a8f9da4055adddbbde41e42b77b4262)](https://github.com/EnriqueSoria/octet)

### Representar tamanys d'informació
Hi ha principalment dos formes de definir valors:
```python
>>> 10 * KiB()
KiB(10)
>>> KiB(10)
KiB(10)
```

I una extra, a partir de la unitat més xicoteta, que en este cas es el `bit`
```python
>>> KiB.from_minor_units(8192)
KiB(1)
```

### Convertir entre unitats
```python
>>> KiB(512).convert_to(MiB)
MiB(0.5)
>>> MiB(1).convert_to(bit)
b(8388608)
```

### Comparacions lògiques
```python
>>> Kb() == 1000 * b()
True
>>> 1000 * Kib() == 1024 * Kb()
True
>>> KiB(1) > KiB(0.7)
True
```

### Enllaços
 - La llibreria està disponible [a pypi](pypi.org/project/octet/)
 - El codi està disponible [en github](https://github.com/EnriqueSoria/octet)
