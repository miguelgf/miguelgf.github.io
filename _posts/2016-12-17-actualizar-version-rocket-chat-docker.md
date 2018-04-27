---
layout: post
title: 'Actualizar última versión rocket chat con Docker'
date: '2016-12-17'
author: Miguel G. Flores
tags:
- docker
- rocket chat
- linux
---

Para actualizar una instancia ya funcionando de Rocket.Chat que haya sido instalada con Docker como recomiendan
en su documentación oficial, debemos seguir los siguientes pasos:

Entramos por SSH a la máquina donde tengamos el servidor andando y examinamos las imágenes de docker
que están ejecutándose

```
$ docker ps -a
```

Con esto vamos a tener un listado de este estilo:

```
CONTAINER ID        IMAGE                           COMMAND                  CREATED             STATUS              PORTS                    NAMES
fc08b0541081        rocketchat/rocket.chat:latest   "node main.js"           6 days ago          Up 6 days           0.0.0.0:3000->3000/tcp   rocketchat_rocketchat_1
84cbdeabd1ca        mongo:3.0                       "/entrypoint.sh mongo"   6 days ago          Up 6 days           27017/tcp                rocketchat_db_1
```

Lo importante en este caso es la última columna, de "NAMES". Vamos a tomar el nombre de la instancia de rocket chat 
(la de Mongo) puede quedarse tal y como está, en este caso el nombre es: `rocketchat_rocketchat_1`, así que vamos a parar
esta intancia, actualizarla y ponerla en marcha de nuevo:
 
```
$ docker stop rocketchat_rocketchat_1
$ docker pull rocketchat/rocket.chat
$ docker start rocketchat_rocketchat_1
```

Simplemente con ésto ya tendremos actualizada la instancia a la última versión, para comprobar
que ha funcionado todo correctamente podemos hacer una llamada a la API:

```
$ curl localhost:3000/api/v1/info
```

En el JSON de vuelta podremos ver la versión que tenemos instalada y comprobar que efectivamente
se ha actualizado a la última versión.
