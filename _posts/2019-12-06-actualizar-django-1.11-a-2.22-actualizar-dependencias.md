---
layout: post
title: 'Actualizar Django 1.11 a 2.22: Actualizar dependencias'
date: '2019-11-06'
author: Miguel G. Flores
tags:
- python
- django
---

# Actualizar Django 1.11 a 2.22: Actualizar dependencias

Según la mentalidad de tu empresa y tu equipo, puede ser que querais estar siempre a la última versión de todas las dependencias o por el contrario actualizar solo cuando sea necesario. Si eres de los que está en el primer escenario, las migraciones grandes van a ser mucho más sencillas (como por ejemplo de Python 2 a 3, o como es este caso, de Django 1.11 a 2.22). 

Para mantener este ritmo de actualización, tiene que ser algo grabado en el ADN de tu equipo e ir acompañado de una batería de tests de integración lo suficientemente grande como para saber que la actualización de una dependencia no rompe tu código.

Si vamos un paso más allá, y estamos cuidando la arquitectura de nuestro software, habremos prevenido estos momentos y no nos habremos acoplado directamente a una librería, sino que habremos usado Wrappers alrededor de estas dependencias para que se ajusten a nuestras necesidades, y en el caso de que haya un cambio importante o se rompa la compatibilidad de una librearía solo habrá un sitio donde adaptarse.

#### Detectar dependencias que requieren actualización

Para saber las librerías que tenemos instaladas y las versiones a las que podemos actualizar, existe un paquete en PyPy llamado [https://pypi.org/project/updatable/](https://pypi.org/project/updatable/), que nos va a dar un bonito resultado con toda y cada una de nuestras dependencias y las versiones a actualizar disponibles.

    pip install updatable
    updatable requirements.txt

El programa acepta un archivo requirements.txt para analizar solo las dependencias directas de tu proyecto, el resultado va a ser algo como:

    drf-extensions (0.3.1)
      Minor releases:
      -- 0.5.0 on 2019-05-10 06:23:39
      -- 0.4.0 on 2018-09-11 04:47:32
    ___
    elasticsearch (1.6.0)
      Major releases:
      -- 7.0.4 on 2019-08-22 16:11:19
      -- 7.0.3 on 2019-08-21 16:01:28
      -- 7.0.2 on 2019-05-28 17:33:25
    ___
    factory_boy (2.11.1)
      Minor releases:
      -- 2.12.0 on 2019-05-11 14:39:40

En este caso no queda mas remedio que ir **una por una** por las dependencias y analizar lo que nos afectaría el cambio y cuales son totalmente necesarias actualizar. En este caso por ejemplo esta extensión de Django:

    django-object-actions (0.10.0)
      Major releases:
      -- 1.1.0 on 2019-05-04 15:17:11
      -- 1.0.0 on 2018-03-09 06:52:45

Está en la versión 0.10 y podemos subir a la 1.0.0 ó 1.1.2, si miramos el changelog ([https://github.com/crccheck/django-object-actions/blob/master/CHANGELOG.md#100](https://github.com/crccheck/django-object-actions/blob/master/CHANGELOG.md#100)) vemos que el soporte a Django 2 fue añadido en la versión 1.0.0, así que es totalmente necesario actualizar esta librería con el impacto que pueda tener. Es un buen momento para no quedarse en la versión mínima e ir un paso más allá y actualizar a la última (1.1.2).
