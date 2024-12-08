---                                                                             
title: "TIL: La caché de `functools.lru_cache` se mantiene entre tests"
date: 2024-12-03T01:01:00+01:00
type: 'post'
layout: 'single'
tags: ['django', 'pytest', 'cache', 'spanish', 'til']
images: ["https://github.com/user-attachments/assets/0f9e21ab-c80e-4b8f-af71-aafdaf93805b"]
draft: false
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

```
============================= short test summary info =============================
FAILED tests/test_utils.py::test_get_modified_database_name_after_modifying_it - AssertionError: assert 'default' == 'modified'
=========================== 1 failed, 1 passed in 0.03s ===========================
```

Si queremos limpiarla automáticamente, podríamos hacerlo de esta forma en pytest:

```python
# conftest.py
import pytest


@pytest.fixture(autouse=True)
def clean_get_setting_cache():
    from utils import get_setting

    get_setting.cache_clear()
```

De esta forma, la fixture se usará automáticamente en cada test, y por tanto la caché de `get_setting` se limpiará entre tests. 

Puedes probarlo tu mismo con [este proyecto de ejemplo](https://github.com/EnriqueSoria/test-lru-cache-is-persistent-between-tests).
