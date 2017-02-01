---
layout: post
title: 'Usar triggers y procedures en MySQL con Amazon RDS'
date: '2017-02-01'
author: Miguel González
tags:
- amazon
- rds
- mysql
---

A la hora de importar una base de datos MySQL 5.5 que contenía varios disparadores (triggers) de MySQL, hay un error
que aparece en las instancias de AWS RDS que es el siguiente:

```
ERROR 1419 (HY000) at line 1116: You do not have the SUPER privilege and binary logging is enabled (you *might* want to use the less safe log_bin_trust_function_creators variable)
```

Ésto ocurre porque Amazon se protege por defecto de crear triggers y procedures en su sistema. La manera de solventarlo
es la siguiente:

- Vamos al dashboard de RDS y en la parte izquierda pulsamos en `Parameter Groups`
- Creamos un nuevo parámeter group para la versión de MySQL que estemos usando.

![Crear nuevo grupo de parámetros Amazon RDS]({{ site.url }}/assets/images/2017-02-01/parameter-groups-amazon-rds.png)

- Una vez creado apareceremos en el listado de grupos de parámetros, pulsamos en el que acabamos de crear y en la parte 
superior pulsamos "Edit parameters".
- Buscamos en el buscador el parámetro `log_bin_trust_function_creators` y en la columna "Edit Values" marcamos `1` como valor

![log_bin_trus_functions_creators en Amazon RDS]({{ site.url }}/assets/images/2017-02-01/log_bin_trus_functions_creators.png)

- Pulsamos en guardar cambios ("Save changes") en la parte superior. Ahora es el turno de aplicar éste grupo
de parámetros a la instancia de Amazon RDS.
- Pulsamos en el menú de la izquierda en `Instances`, después en la instancia deseada y en la parte inferior de la información,
en el seleccionable de `Instance Actions` pulsamos en `Modify`.
- En la pantalla de editar la instancia, bajo  la sección `Database options` seleccionamos el nuevo grupo de parámetros en
`DB Parameter Group` y guardamos los cambios.
- Sólo nos queda reiniciar la instancia usando el mismo botón de `Instance Actions` en el menú de instancias que usamos para
modificarla pero pulsando sobre `Reboot`

Con éstos pasos debería desaparecer el error y poder importar .sql que creen triggers o procedimientos almacenados.

