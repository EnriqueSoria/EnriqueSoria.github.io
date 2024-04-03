---
title: "TIL: Enable post-mortem PDB in shell_plus command"
date: 2024-04-03T16:14:30+02:00
type: 'post'
layout: 'single'
tags: ['python', 'debugging', 'english', 'TIL']
draft: false
---

If you, like me, use the fantastic [shell_plus](https://django-extensions.readthedocs.io/en/latest/shell_plus.html#shell-plus) 
from [django-extensions](https://django-extensions.readthedocs.io) to debug your Django apps, you may want to know 
how to automatically launch a post-mortem debugger when an uncaught exception happens in your shell.

**TIL** how to do it:

```shell
python manage.py shell_plus -- --pdb
```

