---                                                                             
title: 'lifecycle hooks: una alternativa a les senyals de Django'
date: 2022-11-03T20:00:00+01:00
type: 'post'
layout: 'single'
tags: ['django', 'lifecycle-hooks', 'receivers', 'signals']
draft: true
---

Arriba un moment en teua la vida com a *django developer* en la que descobreixes les [signals](https://docs.djangoproject.com/en/4.1/topics/signals/), i notes de repent que el món seria un lloc millor si hi hagueren més senyals. Les senyals són el camí a seguir.

![imatge](https://user-images.githubusercontent.com/7394684/216316597-3415ca74-4b07-46ee-9dbf-37a87bf7567d.png)

Però *no es oro todo lo que reluce*.

## Les senyals són complicades
 - S'han de registrar al `App.ready()` 
 - Poden estar a qualsevol lloc del codi: Són difícils de localitzar
 - Separen la lògica del model al que pertany
 - No s'executen sempre: tests, `queryset.update()` 


## Quan usar senyals
- Per a integrar-se amb senyals que emeten third-party apps. Perquè estos models no els pots editar (postsave) i perquè potser llancen senyals que no tenen a vore amb models. exemple: allauth fa una senyal quan un usuari fa login.
- Si estàs fent aplicacions pensades per a ser reutilitzades i potser seria interessant que els usuaris de la teua aplicació escolten algunes senyals per a fer alguna gestió.
- Per a events dins del teu codi que potser necessiten escoltar-se desde varios llocs. ¿encara que potser es podria fer un dispatcher i cridar-lo quan siga necessari?


## Quan no
 - Per a accions relacionades amb el cicle de vida d'un model del que tens control.

En estos casos hi han alternatives: sobreescriure el `save()`? 

```python
class Blog(models.Model):
    title = models.CharField(...)
    slug = models.SlugField(...)

    def save(self, *args, **kwargs):
        if not self.slug:
            self.slug = slugify(self.title)
        super().save(*args, **kwargs)
```

```python
class Blog(models.Model):
    title = models.CharField(...)
    slug = models.SlugField(...)

    @hook(BEFORE_CREATE, when="slug", is_not=None)
    def set_slug_from_title(self):
        self.slug = slugify(self.title)
```

![imatge](https://github.com/EnriqueSoria/EnriqueSoria.github.io/assets/7394684/d46272f8-a908-467d-a791-36e1fc02b9ad)

---


Gran part de les senyals que es gasten serveixen per a fer accions abans o després de guardar: crear models relacionats, calcular coses...
creència false de que s'executen en segon pla

es podria sobreescriure el save... però millor amb la llibreria django-lifecycle, en la qual he colaborat :v: 


## problemes de les signals
 - son difícils de trobar si no saps de la seua existència
 - s'han de registrar al app.ready

## que soluciona django-lifecycle?
 - convertir funcions normals en `hooks` mitjançant un decorador on especifiques en quina acció s'executen

## que no soluciona django-lifecycle?
 - queryset.update()
 - integrar-se amb llibreries externes
 - en general: senyals no relacionades amb el `.save()` o `.delete()` d'un model


## keywords

cohesió, discoverability


## bibliografia

https://tech.kavehome.com/springfield/adr/0016-backend-signals-lifecycle-hooks.html
https://lincolnloop.com/insights/django-anti-patterns-signals/
https://www.django-antipatterns.com/antipattern/signals.html

