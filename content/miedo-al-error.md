
Let's create a little script to retrieve a list of pull requests for a given repository:

```python
import os

# Retrieve github token from env, useful if our repo is private
GITHUB_TOKEN = os.environ.get("GITHUB_TOKEN")
REPO_NAME = os.environ.get("REPO_NAME")

github = Github(GITHUB_TOKEN)


def get_pull_requests(repo_name):
    try:
        return github.get_repo(repo_name).get_pulls()
    except Exception:
        # shit happens, let's catch exceptions (just in case)
        return []
 
for pull_request in get_pull_requests(REPO_NAME)):
    print(pull_request.title)
 ```



## bibliography
 - https://luzkan.github.io/smells/afraid-to-fail
