---                                                                             
title: 'Create mantainable env.sample using django-environ'
date: 2022-09-20T08:00:00+02:00
type: 'post'
layout: 'single'
tags: ['django', 'env']
---

# Create mantainable env.sample using [django-environ](https://github.com/joke2k/django-environ)

Keeping your env.sample is a hard task, that's why I created a way to have it updated automatically: 

[![EnriqueSoria/update-django-environ-sample - GitHub](https://gh-card.dev/repos/EnriqueSoria/update-django-environ-sample.svg)](https://github.com/EnriqueSoria/update-django-environ-sample)

## How-to:

Put every env var with its configuration in a `ENV_DEFAULTS` variable, in a separate python file named `environment_defaults.py` (exactly this file name)
```python
# environment_defaults.py
ENV_DEFAULTS = dict(
    VARIABLE_WITH_DEFAULT_VALUE=(str, "default value"),
    VARIABLE_WITHOUT_DEFAULT_VALUE=str,
)
```

Instantiate `Env` with `ENV_DEFAULTS` on your Django settings file
```python
# settings.py
from environ import Env
from environment_defaults import ENV_DEFAULTS

env = Env(**ENV_DEFAULTS)
```

And ensure you are accessing it using `env("VAR_NAME")` instead of `env.str("VAR_NAME")`
```python
# settings.py
value = env("VARIABLE_WITH_DEFAULT_VALUE")
value = env("VARIABLE_WITHOUT_DEFAULT_VALUE")
```

Add [this pre-commit hook](https://github.com/EnriqueSoria/update-django-environ-sample) so your `env.sample` gets updated on each commit
```yaml
repos:
  - repo: https://github.com/EnriqueSoria/update-django-environ-sample
    rev: 'vX.X.X'  # Pick a release version
    hooks:
      - id: update-django-environ-sample
        args: [ "-p", "myapp/" ]  # Path where your environment_defaults.py is located
```


Enjoy your always up-to-date env.sample!
