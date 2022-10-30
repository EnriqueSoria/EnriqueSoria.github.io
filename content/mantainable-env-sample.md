---                                                                             
title: 'Create mantainable env.sample using django-environ'
date: 2022-09-20T08:00:00+02:00
type: 'post'
layout: 'single'
tags: ['django', 'env']
---


# Create mantainable env.sample using [django-environ](https://github.com/joke2k/django-environ)

## Put every env var with its configuration when instantiating `Env` object
```python
from environ import Env

env = Env(
    VARIABLE_WITH_DEFAULT_VALUE=(str, "default value"),
    VARIABLE_WITHOUT_DEFAULT_VALUE=str,
)

value = env("VARIABLE_WITH_DEFAULT_VALUE")
value = env("VARIABLE_WITHOUT_DEFAULT_VALUE")
```

## Use this script that uses the `Env` instance to generate an `env.sample`
```python
from pathlib import Path

import click
import sys
from environ import Env


def get_env_config() -> Env:
    # Add env_location to python path so it can find it.  
    env_location = Path("app/your_app/")  
    sys.path.append(str(env_location.absolute()))
    
    # Import env instance
    from your_app import settings
    return settings.env


def get_env_sample_text(env: Env, write_defaults: bool = False) -> str:
    text = ""
    for env_name, env_config in env.scheme.items():
        has_default: bool = isinstance(env_config, tuple) and len(env_config) == 2

        if has_default:
            if write_defaults:
                cast, default_value = env_config
                text += f"{env_name}={default_value}\n"
        else:
            text += f"{env_name}=\n"

    return text


@click.command()
@click.option(
    "--file",
    "-f",
    default="./env.sample",
    help="Path of the sample env file",
    show_default=True,
)
@click.option(
    "--defaults",
    "-d",
    is_flag=True,
    default=False,
    help="Write default values to sample env file",
    show_default=True,
)
def generate_env_sample(*, file: str, defaults: bool):
    env = get_env_config()

    # Write env sample
    text = get_env_sample_text(env, write_defaults=defaults)
    with open(file, "w") as f:
        f.write(text)


if __name__ == "__main__":
    generate_env_sample()

```

## Add it to your pre-commit so it never gets outdated
```yaml
repos:
- repo: local
  hooks:
    - id: update-env-sample
      name: Auto update env.sample
      entry: python3 scripts/generate_env_file.py
      language: system
      always_run: true
      pass_filenames: false
```

## Enjoy!

