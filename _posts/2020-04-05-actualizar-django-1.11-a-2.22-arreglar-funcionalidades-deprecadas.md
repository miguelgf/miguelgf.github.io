---
layout: post
title: 'Actualizar Django 1.11 a 2.22: Arreglar funcionalidades deprecadas'
date: '2020-04-05'
author: Miguel G. Flores
tags:
- python
- django
---

# Actualizar Django 1.11 a 2.22: Arreglar funcionalidades deprecadas

En cada versión de Django (o de cualquier otro framework o librería) una serie de funcionalidades se van deprecando y otras van siendo eliminadas. Normalmente una funcionalidad va a ser deprecada (y no se aconseja su uso) en una o varias versiones antes de ser eliminada por completo.

Por ejemplo, en [Django 1.11 se deprecaron distintas funcionalidades](https://docs.djangoproject.com/en/3.0/releases/1.11/#features-deprecated-in-1-11) que luego fueron (eliminadadas en Django 2.1)[https://docs.djangoproject.com/en/3.0/releases/2.1/#features-removed-in-2-1]

#### Ver notificaciones (warnings) de uso de estas funcionalidades

En la lista de release notes de Django está muy detallado que es lo que va a ser eliminado, sin embargo, puede que tu sistema, de una manera u otra esté haciendo uso de estas funcionalidades que van a ser eliminada sin que sea explícito. En este caso buscarlas se vuelve una tarea bastante compleja o directamente imposible.

Sin embargo podemos usar el logger de python para mostrar todo este tipo de notificaciones. Hay que añadir los flags `-Wd` al ejecutar `python`

##### Ejecutar python en con logging debug en tests

Lo primero y más inmediato sería ejecutar nuestra suite de test con estos flags

    python -Wd manage.py test --noinput src/ -v 3

Esto activará una cantidad de logs importante, todo esto avisando de las funcionalidades que están deprecadas, un ejemplo de la salida de ejecución de tests con estos flags sería:

    /home/vagrant/venv/lib/python3.6/site-packages/cities/migrations/0001_initial.py:135: RemovedInDjango20Warning: on_delete will be a required arg for ForeignKey in Django 2.0. Set it to models.CASCADE on models and in existing migrations if you want to maintain the current default behavior. See https://docs.djangoproject.com/en/1.11/ref/models/fields/#django.db.models.ForeignKey.on_delete

En este caso es un archivo de migraciones de Django que tendríamos que actualizar si queremos que siga funcionando en Django 2

##### Ejecutar python en con logging debug en producción

Una vez todos nuestros tests estén limpios de avisos, que estemos ejecutando nuestra aplicación también en local con estos flags activados y no veamos nuestra salida, podemos activarlo en una de nuestras máquinas de producción, si tenemos varias balanceadas con ponerlo en una de ellas sería suficiente, para poder ver en los logs de producción si realmente se está usando algo que no hayamos visto en local o en la suite de test.

El tenerlo en producción va a producir muchísimos logs, es recomendable ponerlo una vez que en local se ha limpiado todo y solo ponerlo sobre una parte de la infraestructura o durante un tiempo limitado de tiempo. Recoger la salida del sistema, actualizar este código y volverlo a poner.
