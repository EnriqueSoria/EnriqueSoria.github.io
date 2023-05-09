---                                                                             
title: 'Millorar la llegibilitat del nostre codi: el naming'
date: 2023-05-09T18:07:00+02:00
type: 'post'
layout: 'single'
tags: ['clean code', 'python', 'català']
draft: true
---


## El *naming*
El nom que li donem a les nostres variables es molt important. Un bon *naming* es la diferència entre un codi llegible i uno difícil d'entendre. Per aquest motiu, anem a repasar una sèrie de regles senzilles que ens ajudaran molt a l'hora de millorar la llegibilitat del nostre codi:

 - No abrevies. De veritat, no ens cobren per tindre variables amb noms més llargs, i els IDE que utilitzem per a programar ens ajuden amb l'autocompletat.
 - Intenta ser el més clar possible amb la nomenclatura. Val la pena.

### Variables
Les variables representen, habitualment, objectes. Per tant utilitzarem substantius (encara que hi ha una excepció: els booleans)

#### `bool`
Normalment una variable de tipus `bool` ens hauria de contestar a una pregunta. Per tant, normalment hauria de començar amb un verb que ens force a contestar amb un si o no, és a dir, amb un `True`o `False`. Alguns d'ells serien (en anglès):
 - is / are
 - has
 - can / should
 - matches
 - ...

#### `list`s i col·leccions d'objectes
Aquesta regla es molt òbvia i fàcil, però pot comfondre molt si no es respecta. Les variables que representen més d'una unitat haurien d'estar en **plural**.

### Funcions
Les funcions poden ser molt variades, hi han algunes que tornen un valor, altres que mostren alguna cosa per la consola, altres que realitzen peticions a un servidor web... és per això que és molt important triar un nom que represente **exactament** qué fa una funció.

Algunes regles per a aconseguir-ho son:
 - Una funció per regla general hauria de començar amb un verb.
 - Si retorna un valor, potser es bona idea afegir com a prefix algún verb que ho done a entendre:
    - `get_` / `retrieve_`
    - `calculate_`
 - Si retorna un booleà, podem seguir la mateixa regla que amb els booleans: `is_`, `are_`, `has_`...
 - No tingues por en fer un nom de funció llarg. Es millor tindre un bon nom que un nom curt. Ah, ¡i els caràcters son gratis!
 - Una funció hauria de fer una única cosa: si el teu nom de funció conté un `_and_` en el nom possiblement hauries de dividir-la


### Un exemple:
```python
import datetime
from dataclasses import dataclass

@dataclass
class User:
    authenticated: bool
        
    def log_in(self):
        self.authenticated = True
    
def anonymous():
    return [user for user in users if user.authenticated]

result = anonymous()
for u in result:
    u.log_in()

@dataclass
class Post:
    date_published: datetime.datetime
    
    @property
    def public(self) -> bool:
        return datetime.now() >= self.date_published
```

## Convertir comentaris en codi


## Convertir comentaris en funcions
