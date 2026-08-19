---                                                                             
title: "Insertar un diff en un HTML usando la librería estándar de Python"
date: 2026-08-20T18:40:00+02:00
type: 'post'
layout: 'single'
tags: ['python', 'diff', 'html', 'django', 'spanish']
images: ["https://github.com/user-attachments/assets/9ba119b0-de52-4eed-aeda-1e5c2fcb732c"]
draft: true
---

La librería estándar de Python permite generar diffs en formato HTML.

Generar un fichero HTML con un diff es muy sencillo, y [testdriven.io](https://testdriven.io/) tiene un ejemplo de como hacerlo [aquí](https://testdriven.io/tips/43480c4e-72db-4728-8afd-0b0f4f42d4f4/)

Pero si en vez de generar un diff de dos ficheros lo que quieres es generar un diff de dos variables, y ponerlo en una template de Django, quizás te preguntes como hacerlo. Pues bien, ese ha sido mi caso.

<img width="577" height="149" alt="image" src="https://github.com/user-attachments/assets/9ba119b0-de52-4eed-aeda-1e5c2fcb732c" />

Generar la tabla:
```python
old_python_dict = {"foo": 1, "bar": "sinson"}
new_python_dict = {"foo": 2}
diff = difflib.HtmlDiff(wrapcolumn=self.DIFF_WIDTH).make_table(
    json.dumps(old_python_dict, indent=2).splitlines(),
    json.dumps(new_python_dict, indent=2).splitlines(),
    fromdesc="Old Python Dictionary",
    todesc="New Python Dictionary",
)
```

Obtener los estilos:
```python
import difflib
css = f"<style>{difflib.HtmlDiff._styles}</style>"
```

Y finalmente usarlo en tu HTML. Si, por ejemplo, usas Django, podrías añadir al contexto ambas variables (`diff` y `css`) e insertarlas en tu template de la siguiente forma:
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Diff</title>
    <style type="text/css">
      {{ css }}
    </style>
  </head>
  
  <body>
    {{ diff|safe }}
  </body>
</html>
```

---

```python
import difflib
import json
from typing import Any

from django.http import HttpResponse
from django.template import Template, RequestContext
from django.views import View

html = """
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Diff</title>
  </head>
  
  <body>
    {{ diff|safe }}
  </body>
</html>
"""

def render_from_string(template_string: str, request: Any, context: dict):
    template = Template(template_string)
    content = template.render(RequestContext(request, context))
    return HttpResponse(content, content_type=None)

def add_diff_css(diff_html: str) -> str:
    return diff_html + f"<style>{difflib.HtmlDiff._styles}</style>"

class DiffView(View):
    DIFF_WIDTH: int | None = 80

    def get(self, request, *args, **kwargs):
        old_python_dict = {"foo": 1}
        new_python_dict = {"foo": 2}

        diff = difflib.HtmlDiff(wrapcolumn=self.DIFF_WIDTH).make_table(
            json.dumps(old_python_dict, indent=2).splitlines(),
            json.dumps(new_python_dict, indent=2).splitlines(),
            fromdesc="Old Python Dictionary",
            todesc="New Python Dictionary",
        )
        diff = add_diff_css(diff)

        return render_from_string(html, request, {"diff": diff})
```
