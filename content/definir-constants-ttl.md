---                                                                             
title: 'Definint constants tipus TTL correctament'
date: 2022-11-07T14:44:00+01:00
type: 'post'
layout: 'single'
tags: []
draft: true
---

ttl = 86400

ttl = 24 * 60 * 60

ttl = 24 * 60 * 60  # 24h in seconds

```python
from datetime import timedelta
ttl = timedelta(hours=24).total_seconds()
```
