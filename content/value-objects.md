---                                                                             
title: "ValueObjects"
date: 2025-08-13T00:04:00+02:00
type: 'post'
layout: 'single'
tags: ['python', 'catala']
images: ["https://enriquesoria.github.io/avatar-small.jpg"]
draft: true
---

Vam començar a gastar 12factor. Durant la migració vam pasar a gastar esta funció per a obtindre una URL a partir de les variables d'entorn separades:

```python
# settings.py
REDIS_HOST = os.getenv("REDIS_HOST")
REDIS_PORT = os.getenv("REDIS_PORT")
CACHE_REDIS_DB = os.getenv("CACHE_REDIS_DB")

REDIS_LOCATION = f"redis://:@{REDIS_HOST}:{REDIS_PORT}/{CACHE_REDIS_DB}"
```

Podem moure com obtindre la URL a una funció

```python
def build_redis_cache_connection_string(
    host: str,
    port: int,
    db: int,
    password: str | None = None,
) -> str:
    if password:
        return f"redis://:{password}@{host}:{port}/{db}"

    return f"redis://{host}:{port}/{db}"
```

...o podem gastar un ValueObject
```python
from dataclasses import dataclass

@dataclass(kw_only=True)
class RedisConnection:
    host: str
    port: int
    db: int
    password: str | None = None

    def as_connection_string(self) -> str:
        if self.password:
            return f"redis://:{self.password}@{self.host}:{self.port}/{self.db}"

        return f"redis://{self.host}:{self.port}/{self.db}"

redis_connection = RedisConnection(host="localhost", port=6379, db=0) 
redis_url = redis_connection.as_connection_string()
```

¿Que guanyem? Ara tenim la informació estructurada. A més a més podem afegir mètodes auxiliars i/o validació.
