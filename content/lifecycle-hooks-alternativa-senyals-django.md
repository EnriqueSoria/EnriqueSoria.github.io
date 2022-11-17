---                                                                             
title: 'lifecycle hooks: una alternativa a les senyals de Django'
date: 2022-11-03T20:00:00+01:00
type: 'post'
layout: 'single'
tags: ['django', 'lifecycle-hooks', 'receivers', 'signals']
draft: true
---

Gran part de les senyals que es gasten serveixen per a fer accions abans o després de guardar: crear models relacionats, calcular coses...
creencia false de que s'executen en segon plano

es podria sobreescriure el save... però millor amb la llibreria django-lifecycle, en la qual he colaborat :v: 


## problemes de les signals
 - son difícils de trobar si no saps de la seua existència
 - s'han de registrar al app.ready

## que soluciona django-lifecycle?
 - convertir funcions normals en `hooks` mitjançant un decorador on especifiques en quina acció s'executen

## que no soluciona django-lifecycle?
 - queryset.update()


## keywords

cohesió, discoverability


## bibliografia

https://tech.kavehome.com/springfield/adr/0016-backend-signals-lifecycle-hooks.html
https://lincolnloop.com/insights/django-anti-patterns-signals/
https://www.django-antipatterns.com/antipattern/signals.html

