---                                                                             
title: "Actualizar tu código automáticamente a nuevas versiones de Python y Django"
date: 2026-02-04T18:40:00+01:00
type: 'post'
layout: 'single'
tags: ['ruff', 'linters', 'guide', 'spanish']
images: ["https://enriquesoria.github.io/avatar-small.jpg"]
draft: false
---

Si acabas de actualizar entre versiones de Python o Django (o tienes un proyecto que ya has actualizado) es bastante posible que tengas código que, aunque funcione, esté obsoleto.

Por ejemplo, la antigua sintaxis de uniones de tipos era esta `Union[X, Y]` y, desde Python 3.10, se escribe como `X | Y`.

Gracias a [ruff](https://docs.astral.sh/ruff/) podemos actualizar esta sintaxis automáticamente utilizando [las reglas de pyupgrade](https://docs.astral.sh/ruff/rules/#pyupgrade-up):
```bash
ruff check --select UP --fix --target-version py310
```

Hay algunos fixes que están marcados como [inseguros](https://docs.astral.sh/ruff/settings/#unsafe-fixes). Mi recomendación es ejecutar primero los seguros, hacer commit, y luego probar con los inseguros, revisándolos bien.
```bash
ruff check --select UP --fix --target-version py310 --unsafe-fixes
```

Para el caso de Django tenemos otra herramienta, [django-upgrade](https://django-upgrade.readthedocs.io/en/latest/index.html), que nos actualizará el proyecto para que use las últimas características de Django.
```bash
git ls-files -z -- '*.py' | xargs -0r django-upgrade --target-version 6.0
```
