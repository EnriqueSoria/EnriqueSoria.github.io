---                                                                             
title: 'Jugando con la cli de Github'
date: 2022-11-04T15:00:00+01:00
type: 'post'
layout: 'single'
tags: ['gh-cli', 'github', 'cli']
draft: true
---

```sh
export PR_NUMBER=1
export REPO_NAME=EnriqueSoria/GithubDiscordBot
```

Obtener los ficheros que han cambiado en una PR:
```sh
gh pr diff $PR_NUMBER --repo $REPO_NAME --name-only
```

Después los podemos filtrar por extensión con `grep`
```sh
gh pr diff $PR_NUMBER --repo $REPO_NAME --name-only | grep \.py --color=never
```
