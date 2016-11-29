---
layout: post
title: 'Variables de entorno en tiempo de ejecución en Symfony 3.2'
date: '2016-11-29'
author: Miguel González
tags:
- php
- symfony3
---

La versión 3.2 de Symfony trae nuevas mejoras importantes, especialmente en cuanto a componentes para ayudar a realizar
nuestras aplicaciones.

En este caso voy a hablar de poder usar variables de entorno en tiempo de ejecución, para los que hayan usado entornos
de symfony separados para staging, test, preproducción y producción esta mejora va a resultar muy beneficiosa.
 
La principal ventaja de las variables de entorno en general, es que puedes desplegar el mismo código en distintos 
entornos y según su valor usar un entorno distinto en symfony. Hasta ahora se aplicaba al entorno completo (con las
variables del tipo `SYMFONY__`) al generarse por primera vez el contenedor y en tiempo de ejecución no podía ser 
modificado. Con este nuevo cambio se podrán modificar, especialmente dando la oportunidad de no tener que meter estos 
valores dependientes del entorno en el repositorio de código.

En esta versión, una de los cambios es que no es necesario usar el prefijo `SYMFONY__` para estas variables, se pueden
llamar de cualquier manera, el modo de usarlas en los archivos de configuración es la siguiente:

```
# app/config/config.yml
doctrine:
    dbal:
        default_connection: general
        connections:
            general:
                // ...
                dbname: '%env(DB_NAME)%'
```

En el ejemplo anterior, siempre que se requiera el nombre de la base de datos para realizar una conexión con Doctrine,
se realizará una llamada a la variable de entorno `DB_NAME` y se utilizará su valor.
 
Para usar valores por defecto de estas variables se pueden definir igual que otras variables de configuración, usando
como el propio nombre de la variable `env(DB_NAME)`, por ejemplo:
```
# app/config/parameters.yml
parameters:
    env(DB_NAME): db_general
```