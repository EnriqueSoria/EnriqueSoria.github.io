Vamos a crear un pequeño script para obtener las pull requests de nuestro repositorio:

```python
import os

def get_pull_requests(repo_name):
    try:
        github = Github(os.environ.get("GITHUB_TOKEN"))
        return github.get_repo(repo_name).get_pulls()
    except Exception:
        # shit happens, devolvemos array vacío por si falla
        return None

for pull_request in get_pull_requests(os.environ.get("REPO_NAME")):
    print(pull_request.title)
 ```
 
 - Vamos a probarlo:
```python
>>> for pull_request in get_pull_requests(os.environ.get("REPO_NAME")):
...    print(pull_request.title)

>>>
```

Vaya... algo pasa aquí, quizás no hemos seteado `REPO_NAME`?
```python
>>> for pull_request in get_pull_requests(os.environ.get("REPO_NAME")):
...     print(pull_request.title)
... 
...     
>>>
```

Sigue sin ir... qué será? Vamos a quitar el `try...except` a ver qué pasa:

```python
>>> def get_pull_requests(repo_name):
...     github = Github(os.environ.get("GITHUB_TOKEN"))
...     return github.get_repo(repo_name).get_pulls()
... 
>>> for pull_request in get_pull_requests(os.environ.get("REPO_NAME")):
...     print(pull_request.title)
... 
...     
Traceback (most recent call last):
  File "/usr/lib/python3.10/idlelib/run.py", line 578, in runcode
    exec(code, self.locals)
  File "<pyshell#20>", line 1, in <module>
  File "<pyshell#18>", line 2, in get_pull_requests
NameError: name 'Github' is not defined. Did you mean: 'github'?
```

Vaya, no había importado Github! Es más, ni siquiera lo tengo instalado!

```python
>>> from github import Github
Traceback (most recent call last):
  File "/usr/lib/python3.10/idlelib/run.py", line 578, in runcode
    exec(code, self.locals)
  File "<pyshell#21>", line 1, in <module>
ModuleNotFoundError: No module named 'github'
```
    
---


Vamos a ver cómo lo podríamos reescribir sin tener miedo a fallar:
```python
import os

from github import Github


# El nombre del repositorio es obligatorio para que nuestro programa funcione, 
#  así que vamos a obtenerlo tan pronto como sea posible.
# Además, en vez de usar dict.get(), vamos a acceder a la variable directamente
REPO_NAME = os.environ["REPO_NAME"]

# En el caso del token de github, sólo es necesario si nuestro repo es privado, por lo que
#  seguiremos usando dict.get() para que nos devuelva None en el caso de que no esté definido
GITHUB_TOKEN = os.environ.get("GITHUB_TOKEN")


def get_pull_requests(repo_name):
    github = Github(GITHUB_TOKEN)
    return github.get_repo(repo_name).get_pulls()

for pull_request in get_pull_requests(REPO_NAME):
    print(pull_request.title)
 ```

## keywords
 - fail fast

## bibliography
 - https://luzkan.github.io/smells/afraid-to-fail
