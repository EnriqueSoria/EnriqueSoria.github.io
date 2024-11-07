---                                                                             
title: "TIL: Django relations are not cached in model instance"
date: 2024-11-07T10:34:00+01:00
type: 'post'
layout: 'single'
tags: ['django', 'ORM', 'prefetching', 'english']
images: ["https://enriquesoria.github.io/avatar-small.jpg"]
draft: true
---


Given these models:

```python
from django.db import models


class Order(models.Model): ...


class Line(models.Model):
    order = models.ForeignKey("order.Order", on_delete=models.CASCADE, related_name="lines")
```

and a context decorator that measures how many queries are done:

```python
import time
from contextlib import contextmanager

from django.db import connection


@contextmanager
def query_debugger(name: str = ""):
    start_queries = len(connection.queries)

    start = time.perf_counter()
    yield
    end = time.perf_counter()
    end_queries = len(connection.queries)
    query_count = end_queries - start_queries
    time_elapsed_in_seconds = end - start
    msg = f"query_count={query_count} took={time_elapsed_in_seconds:.2f}s"
    if name:
        msg = f"{name}: {msg}"
    print(msg)
```
