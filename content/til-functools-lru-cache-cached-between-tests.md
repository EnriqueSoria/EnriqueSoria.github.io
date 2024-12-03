---                                                                             
title: "TIL: La caché de functools.lru_cache se mantiene entre tests"
date: 2024-11-15T19:34:00+01:00
type: 'post'
layout: 'single'
tags: ['django', 'pytest', 'cache', 'spanish']
images: ["https://enriquesoria.github.io/avatar-small.jpg"]
draft: true
---

Asumamos que tenemos una función cacheada con `functools.lru_cache`:

```python
# utils.py
from functools import lru_cache


SETTINGS = {"DATABASE_NAME": "default"}

@lru_cache
def get_setting(key):
    return SETTINGS[key]
```

Y un par de tests que comprueban su comportamiento:
```python
# test_utils.py
import pytest

from utils import get_setting


def test_get_database_name():
    global SETTINGS

    assert get_setting("DATABASE_NAME") == "default"


def test_get_modified_database_name_after_modifying_it():
    from utils import SETTINGS

    SETTINGS["DATABASE_NAME"] = "modified"

    assert get_setting("DATABASE_NAME") == "modified"
```

Si lo ejecutamos comprobaremos que el segundo test falla, ya que devuelve el valor que tenía antes de ser modificado el `SETTINGS`. Esto es debido a que la caché `lru_cache` no se limpia entre tests. 

Si queremos limpiarla automáticamente, podríamos hacerlo de esta forma en pytest:

```python
# conftest.py
import pytest


@pytest.fixture(autouse=True)
def clean_stores_cache():
    from utils import get_setting

    get_setting.cache_clear()
```

De esta forma, la fixture se usará automáticamente en cada test, y por tanto la caché de `get_setting` se limpiará entre tests.
