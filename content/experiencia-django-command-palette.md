---                                                                             
title: "Una gran experiència amb l'open source"
date: 2022-11-26T13:20:00+01:00
type: 'post'
layout: 'single'
tags: ['django', 'open-source', 'català']
---

Lo que ha pasat hui ha sigut increïble. 


## 07:56 AM - El descobriment
Cada dia quan em desperte perd un rato amb el mòbil, mire notificacions, xarxes socials, etc... cosa que no es molt recomanable però ey, com diu Cactus: "[ni som perfectes, ni volem ser-ho](https://www.youtube.com/watch?v=GB-vFtTtiys)".

Mentre perdia el temps mirant mastodon ([on em podeu seguir](https://mastodon.social/@esoria_dev)) he trobat un toot recomanant una llibreria per a [Django](/tags/django/):

[![rajasimon/django-command-palette - GitHub](https://gh-card.dev/repos/rajasimon/django-command-palette.svg)](https://github.com/rajasimon/django-command-palette)

Es una llibreria simple, però que afegix un buscador que, personalment, trobe molt a faltar a l'admin de django.


## 10:49 - El problema
Ja en la faena trobe un rato lliure i em dispose a provar la llibreria que he descobert feia unes hores. Cree una rama nova des de `master`, execute `poetry add django-command-palette` i seguixc les instruccions d'instal·lació (que són molt senzilles).

Execute el projecte, vaig a l'admin... i merda, no em funciona 😅


## 11:26 - El fix
Obric [una pull request](https://github.com/rajasimon/django-command-palette/pull/9) que soluciona el problema que tenia jo, i altre usuari que había obert una issue.

![imagen](https://user-images.githubusercontent.com/7394684/204090228-cc54f344-5a45-47ed-9b09-e8b812725dee.png)

## 12:41 - El testing
Sorprenentment, la conversació a la pull request ha sigut molt fluida:

![imagen](https://user-images.githubusercontent.com/7394684/204090258-ad8a3f58-a442-46c6-a380-162b0ee75215.png)


## 18:03 - La release
Una nova release amb el meu fix s'ha pujat [a pypi](https://pypi.org/project/django-command-palette/2022.11.25/)

![imagen](https://user-images.githubusercontent.com/7394684/204090321-bcf4abbe-4472-49aa-8b7d-afe595b17904.png)


## 19:22 - La pujà a producció
Una volta sol·lucionat l'error, i com a estes hores ja no estic treballant (gràcies a déu), decidixc provar a instal·lar el paquet a [festivales.wiki](https://festivales.wiki), i... 🎉

![festivales-command-palette](https://user-images.githubusercontent.com/7394684/204088918-15d28d13-b8fe-436a-bddc-c8b362b0470d.gif)


## Conclusió
Les experiències que he tingut abans contribuint amb el codi obert no han sigut moltes:
 - Algunes han quedat en pull requests oblidades sense resposta dels desenvolupadors del projecte
 - Altres si que han aplegat a ser contestades i han acabat sent *mergeades*, però ha sigut un procés molt lent
 - I després ha estat esta experiència que ha sigut rapidíssima i m'ha fet molta il·lusió
