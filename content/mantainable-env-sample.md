---                                                                             
title: 'Create mantainable env.sample using django-environ'
date: 2022-09-20T08:00:00+02:00
type: 'post'
layout: 'single'
tags: ['django', 'env']
---


# Create mantainable env.sample using [django-environ](https://github.com/joke2k/django-environ)



 - Put every env var with its configuration in a separate python file named `environment_defaults.py`
```python
ENV_DEFAULTS = dict(
    VARIABLE_WITH_DEFAULT_VALUE=(str, "default value"),
    VARIABLE_WITHOUT_DEFAULT_VALUE=str,
)
```

 - Instantiate `Env` with `ENV_DEFAULTS` on your `settings.py`
```python
from environ import Env
from environment_defaults import ENV_DEFAULTS

env = Env(**ENV_DEFAULTS)
```

 - And ensure you are accessing it using `env("VAR_NAME")` instead of `env.str("VAR_NAME")`
```python
value = env("VARIABLE_WITH_DEFAULT_VALUE")
value = env("VARIABLE_WITHOUT_DEFAULT_VALUE")
```

- Use this script that uses the `Env` instance to generate an `env.sample`
```python
from pathlib import Path

import click


def get_env_sample_text(values: dict, write_defaults: bool = False) -> str:
    text = ""
    for env_name, env_config in values.items():
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
    "--environment_defaults_path",
    "-p",
    default="app/settings/",
    help="Path where environment_defaults.py is located",
    show_default=True,
)
@click.option(
    "--output",
    "-o",
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
def generate_env_sample(*, environment_defaults_path: str, output: str, defaults: bool):
    env_location = Path(environment_defaults_path)
    assert env_location.exists()
    assert (env_location / "environment_defaults.py").exists()

    # Make ENV_DEFAULTS importable
    import sys

    sys.path.append(str(env_location.absolute()))
    from environment_defaults import ENV_DEFAULTS

    # Write env sample
    text = get_env_sample_text(ENV_DEFAULTS, write_defaults=defaults)
    with open(output, "w") as f:
        f.write(text)


if __name__ == "__main__":
    generate_env_sample()

```

 - Add it to your pre-commit so it never gets outdated
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

- Enjoy!

