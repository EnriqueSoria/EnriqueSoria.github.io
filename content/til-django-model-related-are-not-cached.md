---                                                                             
title: "TIL: Django relations are not cached in model instance"
date: 2024-11-19T19:34:00+01:00
type: 'post'
layout: 'single'
tags: ['django', 'ORM', 'prefetching', 'english', 'til']
images: ["https://github.com/user-attachments/assets/de3faf89-c86d-48b4-9241-67bfa78970d3"]
draft: false
---


## Evaluating relation multiple times using `.all()` performs `n` queries
Input:
```python
order = Order.objects.first()

with query_debugger("without prefetch"):
    list(order.lines.all())
    list(order.lines.all())
    list(order.lines.all())
```
Output:
```
without prefetch: 3 queries
```

## Evaluating a related queryset stored in a variable only performs one query
Input:
```python
order = Order.objects.first()

with query_debugger("storing related queryset in a variable"):
    lines = order.lines.all()
    list(lines)
    list(lines)
    list(lines)
```
Output:
```
storing related queryset in a variable: 1 queries
```

## Evaluating a prefetched related queryset multiple times using `.all()` doesn't perform any extra query
Input:
```python
order = Order.objects.prefetch_related("lines").first()

with query_debugger("with prefetching"):
    list(order.lines.all())
    list(order.lines.all())
    list(order.lines.all())
```
Output:
```
with prefetching: 0 queries
```

---

## Models and tools used for this test

### Models

```python
from django.db import models


class Order(models.Model):
    pass


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
