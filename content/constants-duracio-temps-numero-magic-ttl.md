---                                                                             
title: 'Representar duracions temporals sense números màgics'
date: 2022-11-07T19:42:00+01:00
type: 'post'
layout: 'single'
tags: ['refactoring', 'python', 'català']
draft: false
---

## Introducció

Si has treballat amb Django (i si no, probablement, també) has vist o escrit alguna volta una variable d'este estil:
```python
ttl = 84600
```

...però, que significa `84600`? Possiblement el valor te sona, i t'atreviries a dir que són 24 hores, expresat en segons. 

O no. Perquè hi ha un error.

```python
ttl = 86400
```

Quin despiste! Ara si, 24 hores expresat en segons.

![terminal(2)](https://user-images.githubusercontent.com/7394684/200399615-9192c405-ae5e-47c2-82d5-1edfe9046035.gif)



## El problema

En este cas `86400` es el que s'anomena un [número màgic](https://refactoring.guru/replace-magic-number-with-symbolic-constant). Un número màgic es aquell que podem trobar al nostre codi i que el seu valor no és evident.

Com podem reescriure este número? 

### Un comentari explicatiu
Podem escriure un comentari explicant qué significa el número màgic:
```python
ttl = 86400  # 24h in seconds
```
O, refinant-ho un poc més podem deixar la multiplicació per a que s'entenga encara millor:
```python
ttl = 24 * 60 * 60  # 24h in seconds
```

Però esta solució té un problema: cada volta que actualitzem el valor de la variable ens hem de recordar de canviar també el comentari. Pareix una obvietat, però es fàcil que amb les preses sen's oblide fer-ho. I ara tenim una cosa molt perillosa: un comentari explicant malament el valor d'una variable.


### Constants amb noms explicatius
Altra solució que podem aplicar es la següent: definir una sèrie de constants que ens ajuden a reescriure el valor màgic de la forma més entendible posible:

```python
MINUTES_IN_SECONDS = 60
HOURS_IN_SECONDS = 60 * MINUTES_IN_SECONDS
DAYS_IN_SECONDS = 24 * HOURS_IN_SECONDS

ttl = 1 * DAYS_IN_SECONDS
```

Esta solució es, en general, la que aplicaria a dia de hui per a evitar escriure números màgics. Amb una excepció.


### timedelta
Si estem treballant concretament amb unitats de temps expresades en segons. En este cas la solució ideal pense que es la següent:

```python
from datetime import timedelta
ttl = timedelta(hours=24).total_seconds()
```

L'objecte [`datetime.timedelta`](https://docs.python.org/3/library/datetime.html#timedelta-objects) de la llibreria estàndar de Python ens permet expresar duracions temporals mitjançant d'una forma llegible. I posteriorment convertir esta duració a segons d'una forma fàcil:

```python
>>> from datetime import timedelta
>>> one_day = timedelta(days=1)
>>> one_day.total_seconds()
86400.0
>>> print(timedelta(minutes=1, seconds=30).total_seconds())
90.0
```
