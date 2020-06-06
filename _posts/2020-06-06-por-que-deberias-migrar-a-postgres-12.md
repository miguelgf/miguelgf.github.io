---
layout: post
title: 'Por qué deberías migrar a PostgreSQL 12'
date: '2020-06-06'
author: Miguel G. Flores
tags:
- postgresql
- rds
- db
---

![PostgreSQL 12]({{ site.url }}/assets/images/2020-06-06/novedades-postgresql-12.png)


Recientemente AWS anunció que [Amazon RDS es compatible con PostgreSQL 12](https://aws.amazon.com/es/about-aws/whats-new/2020/03/amazon-rds-now-supports-postgresql-12/) aunque ya
estaba [disponible en la versión de "Preview" desde finales de 2019](https://aws.amazon.com/es/about-aws/whats-new/2019/11/postgresql-12-0-now-available-in-amazon-rds-database-preview-environment/), por eso es
que me he parado a analizar los beneficios que tendría migrar a PostgreSQL 12.

### Índices B-Tree más rápidos
Los índices en la base de datos son una parte crucial, facilitando la búsqueda de información. PostgreSQL hace
un uno intensivo de índices [B-Tree](https://en.wikipedia.org/wiki/B-tree) (que comenzaron a usarse en el 1970), un tipo de índice que funciona bien con un alto 
nivel de lecturas respecto a escrituras en base de datos.

Sin embargo, cuando se hacen escrituras (`UPDATE`, `INSERT`) o borrado (`DELETE`) es cuando peor rendimiento tienen,
al tener que hacer escrituras en distintas páginas del árbol que mantiene constantemente balanceado.

En este caso, PostgreSQL 12, hace unos grandes progresos en como funcionan éstos índices, reduciendo su tamaño
hasta en un 40% en el mejor de los casos, permitiendo que sea más rápidos mantenerlos en casos de alta escritura y en 
consecuencia, también más rápidos en lectura.

### Reindex concurrently - Reindex sin caida de servicio
Muy ligado al punto anterior, está el reindexado. Una vez realizada la actualización de una versión a otra de postgres,
hay que recrear los índices. Esto es una operación que bloquea completamente el árbol de índice (tal y como lo hace
una escritura sobre una hoja del arbol, pero de manera completa), por lo que tendremos un tiempo de caida de servicio, que 
ha sido siempre necesario al actualizar versiones.

En este aspecto, pg12 trae la opción de recrear un índice sin realizar ningún bloqueo, usando `REINDEX CONCURRENTLY`. Esta manera
de recrear el índice es mucho más lenta, pues va a realizar dos escaneos completos de la tabla y otra serie de operaciones intermedias,
usando mucha más CPU, memoria y entrada/salida, pero no va a realizar ningún bloqueo en la base de datos, por lo que es perfecto para entornos productivos.

La manera de funcionar de `REINDEX CONCURRENTLY` es la siguiente (ejemplo en un solo índice, funciona igual para tablas completas, esquemas o bases de datos):

1.- Un nuevo íncide temporal es añadido al catálogo `pg_index` con un bloqueo exclusivo a nivel de sesión sobre el índice que se está
reindexando, para evitar modificaciones mientras se está realizando el proceso.

2.- Un primer escaneo sobre la tabla se realiza y se crea el nuevo índice. Una vez realizado, se marca este índice como
disponible para inserts (`pg_index.isdisready = true`), de esta manera otras sesiones pueden verlo y mantenerlo actualizado con los nuevos registros
que se modifiquen o añadan desde este momento en adelante.

3.- Una segunda pasada se realiza por la tabla para completar el índice con todos los registros que faltasen.

4.- Todas las condiciones que tuviese el primer índice se pasan a este nuevo segundo índice. Se renombra el segundo íncide
con el nombre del primero, se marca como índice válido (`pg_index.isdisvalid = true`) y el índice antiguo se marca como inválido
(`pg_index.isdisvalid = false`). También se invalida toda la caché que hiciese referencia a el índice antiguo.

5.- Se marca el índice que está siendo reemplazado a `pg_index.isdisready = false` para evitar nuevos inserts.

6.- El índice antiguo se borra y se libera el bloqueo sobre modificación que se hizo en el primer paso.

Si algún problema ocurre durante el proceso de estos 6 pasos, el índice se quedará en el sistema como inválido y habrá que gestionarlo
manualmente.


### Particionamiento declarativo
Postgres 10 introdujo el concepto de particionamiento declarativo. Que permitia definir como se iban a realizar
las particiones de grandes tablas al escribirlas en disco, permitiendo que esta separación cayese bajo una ruptura
lógica más adecuada para el caso de uso de tu aplicación concreta. 

- El rendimiento puede mejorar muchísimo cuando se crean particiones dividiendo las zonas que tienen un acceso constante
y las que no lo tienen, permitiendo consultar solamente una partición para satisfacer la mayoría de las consultas.
- Cuando una query lee o actualiza gran parte de una partición, se puede hacer uso de un escaneo secuencial en lugar de usar
un índice que va a realizar accesos aleatorios.
- Actualizaciones, borrado y carga de datos puede ser realizada en bloque en particiones completas.
- Pueden usarse distintos tipos de almacenamiento para cada partición y tener los datos menos consultados en un almacenamiento más barato.

Un caso de uso para este tipo de particiones es cuando tenemos borrado lógico en una tabla, en la que mantenemos registros en la base de 
datos con una marca de que ha sido borrado lógicamente (por ejemplo `deleted_at`), y la gran mayoría
de las consultas que estamos realizando se hacen sobre los registros activos. En este caso por ejemplo mantener todos
estos registros con `deleted_at != NULL` en otra partición nos mejoraría el rendimiento.
 
Como hemos comentado esta funcionalidad fue introducida en PG10, pero no ha sido hasta PG12 que esta funcionalidad no ha tenido un rendimiento que podriamos considerar para entornos 
productivos con alta carga, especialmente las aplicaciones que realicen operaciones en bloque sobre las tablas, como podrían ser COPY.


### Y otras tantas más...
Estas tres funcionalidades descritas anteriormente, como que [Just-In-Time (JIT)](https://www.postgresql.org/docs/12/jit.html) esté activada por defecto (solo se ejecutará
para queries donde tras analizar el coste, esté por encima del [jit_above_cost](https://www.postgresql.org/docs/11/runtime-config-query.html#GUC-JIT-ABOVE-COST)), 
mejora en velocidad de [Common Table Expresions](https://www.postgresql.org/docs/12/queries-with.html) (los `WITH`) en linea (`SELECT` dentro de `FROM` que solo se usan una vez), 
posibilidad de explorar campos JSON a través de expresiones "estandar" de SQL, 
[columnas autogeneradas](https://www.postgresql.org/docs/12/ddl-generated-columns.html) (las cuales no recomiendo si tu
aplicación tiene un dominio definido, donde sería mejor mantener esta lógica de en la propia aplicación), hacen
que migrar a PG12 sea una buena opción, en la que vamos a obtener una mejora en rendimiento importante.
