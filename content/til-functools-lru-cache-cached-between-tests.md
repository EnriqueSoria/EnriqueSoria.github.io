---                                                                             
title: "TIL: Django relations are not cached in model instance"
date: 2024-11-15T19:34:00+01:00
type: 'post'
layout: 'single'
tags: ['django', 'pytest', 'cache', 'english']
images: ["https://enriquesoria.github.io/avatar-small.jpg"]
draft: true
---


```python
@pytest.fixture(autouse=True)
def clean_stores_cache():
    from app import lru_cached_function

    lru_cached_function.cache_clear()
```
