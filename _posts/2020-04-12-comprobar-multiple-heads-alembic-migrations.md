---
layout: post
title: 'Comprobar migraciones con misma base en Alembic'
date: '2020-04-12'
author: Miguel G. Flores
tags:
- python
- alembic
- flask
---

![Comprobar migraciones con misma base en Alembic]({{ site.url }}/assets/images/2020-04-12/alembic-two-heads.png)


# Comprobar migraciones con misma base en Alembic

Si estáis trabajando en un proyecto con Flask + Alembic (quizás a través de Flask-Migrate) es probable, que si trabajáis varios desarrolladores a la vez, te hayas topado con el siguiente problema:

```
ERROR [root] Error: Multiple head revisions are present for given argument 'head'; please specify a specific target revision, '<branchname>@head' to narrow to a specific head, or 'heads' for all heads
```

Al ejecutar un `flask db upgrade` (o `alembic upgrade` si se usa Alembic directamente).

### Cuál es el problema?
Ocurre que cuando dos ramas simultaneamente parten de la misma base, y ambas ejecutan crean una migración, en el momento que una de la dos se mezcle, la segunda va a devolver este error cuando intentemos desplegar nuestra aplicación.

```
A -> B
      \-> C
      \-> D
```

En este primer grafo, tanto C como D parten de B. Ambas tendrán `down_revision = "b"` (donde B, el hash correspondiente). 

Cuando C se mezcle en master, y D también.

```
A -> B -> C -> D
```

En este momento, en master tendremos dos migraciones que no son secuenciales, por lo que tendremos dos cabezas. 

### Qué debemos hacer cuando ocurre?
Para solucionar este problema, debemos clear una migración de mezcla. 

```
flask db merge heads

# Si se usa directamente Alembic
alembic merge heads
```

Esto va a crear una tercera migración, con un punto de mezcla.

### Como evitar que ocurra
La mejor solución para este problema es la prevención. Una vez que esto ocurre y tenemos un deploy fallido está bloqueándonos la salida a producción y tenemos que hacer una pull requests extra para solucionarlo.


Si tenemos un flujo sano de pull request e integración continua, donde parte de esta suite son test de integración que ejecutan las migraciones para poner una base de datos en el estado deseado, esto se va a comprobar automáticamente.

Si por otra parte queremos detectarlo en fases más tempranas, mientras ejecutamos los tests unitarios o no contamos con este tipo de tests en el sistema de integración continua, podemos crear un test unitario simple que lo compruebe usando la librería de Alembic, que fallaría en la segunda rama que intentase mezclarse.


```
from unittest import TestCase

from alembic.script import ScriptDirectory
from flask_migrate import Config


class TestAlembicMigrations(TestCase):
    def test_migration_tree(self):
        config = Config()
        config.set_main_option("script_location", "app:migrations")
        script = ScriptDirectory.from_config(config)

        current_head = script.get_current_head()

        self.assertIsNotNone(current_head)
```
