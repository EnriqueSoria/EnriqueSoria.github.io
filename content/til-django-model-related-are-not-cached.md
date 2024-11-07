---                                                                             
title: "TIL: Django relations are not cached in model instance"
date: 2024-11-07T10:34:00+01:00
type: 'post'
layout: 'single'
tags: ['django', 'ORM', 'prefetching', 'english']
images: ["https://enriquesoria.github.io/avatar-small.jpg"]
draft: true
---



```pycon
>>> order = Order.objects.first()
>>> 
>>> with query_debugger("without prefetch"):
>>>     list(order.lines.all())
>>>     list(order.lines.all())
>>>     list(order.lines.all())
without prefetch: 3 queries


```







Given these models:

```python
from django.db import models


class Order(models.Model): ...


class Line(models.Model):
    order = models.ForeignKey("order.Order", on_delete=models.CASCADE, related_name="lines")
```

and a context decorator that measures how many queries are done

```python
import time
from contextlib import contextmanager

from django.db import connection


@contextmanager
def query_debugger(name: str):
    start_queries = len(connection.queries)
    yield
    end_queries = len(connection.queries)
    query_count = end_queries - start_queries
    print(f"{name}: {query_count} queries")
```

and a utility function to check if an instance is prefetched:

```python
from django.db import models


def is_prefetched(instance: models.Model, attribute_name: str) -> bool:
    """Check if a ManyToMany is prefetched on an instance"""
    try:
        return attribute_name in instance._prefetched_objects_cache
    except (AttributeError, KeyError):
        return False
```

