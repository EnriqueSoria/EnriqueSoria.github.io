---                                                                             
title: "Adding ruff to existing codebase"
date: 2023-10-17T19:34:00+02:00
type: 'post'
layout: 'single'
tags: ['ruff', 'linters', 'guide', 'english']
draft: false
---

Are you looking to add [ruff](https://docs.astral.sh/ruff/) to an existing codebase? If yes, keep reading.

Do you want your team mates to hate you ~~more than they already do~~? If not, keep reading.

Are you reading this in the voice of a teleshopping advertisement? Let's go.

## Introduction

I've recently found myself wanting to improve the quality of our codebase and reduce the time reviewing pull requests with errors that ruff is able to detect. So I decided to include more rules than the default ones in ruff to achieve this. And I ended up telling everyone to skip pre-commit hooks because they were not able to commit because of ruff is yelling at old code.

After failing so badly, I've find *the proper way™* to do it.


## The tutorial: Adding ruff to an existing codebase

### Commit n° 1: Configure ruff at your will
 - [Install ruff](https://docs.astral.sh/ruff/installation/) and/or add it as a [pre-commit](https://github.com/astral-sh/ruff-pre-commit) hook
 - Check available [rules](https://docs.astral.sh/ruff/rules/).
 - [Configure ruff](https://docs.astral.sh/ruff/configuration/) by specifying:
   - Which rules are enabled, depending on your (or your team's) code style.
   - Select for which rules you want autofixes (if any), using `fixable` and `unfixable` settings
  
### Commit n° 2: Apply autofixes
Apply [autofixes](https://docs.astral.sh/ruff/configuration/#fixes) to change all your code at once.
```bash
ruff check . --fix
```

### Commit n° 3: Add noqa directives
Add `noqa` directives for automatically unfixable rules to already existing errors, to avoid anyone having to deal with them when modifying an existing file:
```bash
ruff check . --add-noqa
```

### Commit n 4: (optional) Tell git blame to ignore these changes 
In order to keep your `git blame` untouched after adding a lot of autofixes and `noqa` directives, git has an option to ignore certain commits when showing `blame` information. 

Michael Heap [wrote about this](https://www.michaelheap.com/git-ignore-rev/) and I recommend to read their post, but anyway I will make a TLDR for you:
 - Create a `.git-blame-ignore-revs` and add the commit hash of your previous two commits in one line each one. Something like this:
 ```
# Apply ruff autofixes
0ea947095f9e7a085302f26a51901e1962fb085e

# Add noqa directives for ruff
ge456456dfge5456rgdg544564rgdfg5t4565476
```
 - Tell everyone in your team to configure their `git` so it actually ignores them:
```bash
git config --global blame.ignoreRevsFile .git-blame-ignore-revs
```

### Last step: 
Merge this commits at once in your main branch and enjoy your brand new linter!
