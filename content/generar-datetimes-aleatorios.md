---
title: "Cómo generar `datetime`s aleatorios entre dos fechas"
date: 2025-11-13T18:30:02+01:00
type: 'post'
layout: 'single'
images: ["https://enriquesoria.github.io/como-generar-datetime-s-aleatorios-entre-dos-fechas.png"]
tags: ['python', 'spanish']
draft: false
---

Para el proyecto de ejemplo sobre cómo mostrar valores de un json en el admin de Django tuve la necesidad de generar datos aleatorios,
concretamente timestamps. Podría haber utilizado alguna librería especializada como [faker](https://faker.readthedocs.io/en/master/) (que tiene [un método dedicado](https://faker.readthedocs.io/en/master/providers/faker.providers.date_time.html#faker.providers.date_time.Provider.date_time_between_dates))
pero decidí hacerlo a mano para evitar instalar una dependencia solo para eso.


Una forma de representar una fecha y hora es mediante lo que se conoce como el Unix Time, que según [la Wikipedia](https://es.wikipedia.org/wiki/Tiempo_Unix):

> Tiempo Unix o Tiempo POSIX es un sistema para la descripción de instantes de tiempo: se define como la cantidad de segundos transcurridos desde la medianoche UTC del 1 de enero de 1970, sin contar segundos intercalares.

En Python podemos obtener un objeto `datetime` a partir de un timestamp usando la función [`datetime.fromtimestamp`](https://es.wikipedia.org/wiki/Tiempo_Unix),
pero también podemos obtener un timestamp a partir de un `datetime` usando el método [`.timestamp()`](https://docs.python.org/3/library/datetime.html#datetime.datetime.timestamp), por lo que podemos usar estos dos 
hechos para obtener un `datetime` aleatorio entre un punto de inicio y fin.

¿Cómo? Convirtiendo las fechas de inicio a fin a timestamp y generando un número aleatorio entre esos dos números. Como Python devuelve
el timestamp como un float tenemos que usar [`random.uniform`](https://docs.python.org/3/library/random.html#random.uniform) (que funciona con `float`s) en vez de `random.randrange` (que trabaja con `int`s).

{{< gist EnriqueSoria a1482a59c64c716cc71eaefd67926325 >}}

PD: Si no nos importan los milisegundos también podemos convertir los timestamps a `int` y usar `random.randrange`
