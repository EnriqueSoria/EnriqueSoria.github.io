Vamos a crear un pequeño script para obtener las pull requests de nuestro repositorio:

```python
import os

# Obtenemos el token de github, por si nuestro repositorio es privado
GITHUB_TOKEN = os.environ.get("GITHUB_TOKEN")
REPO_NAME = os.environ.get("REPO_NAME")

github = Github(GITHUB_TOKEN)


def get_pull_requests(repo_name):
    try:
        return github.get_repo(repo_name).get_pulls()
    except Exception:
        # shit happens, devolvemos None por si falla
        return None

for pull_request in get_pull_requests(REPO_NAME)):
    print(pull_request.title)
 ```



## bibliography
 - https://luzkan.github.io/smells/afraid-to-fail
