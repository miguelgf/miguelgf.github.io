---
layout: post
title: 'Instalar PHP Driver para Mongo en PHP 5.6'
date: '2016-11-28'
author: Miguel González
tags:
- php
- mongo
- linux
- ubuntu
---

Para instalar el driver de Mongo para PHP 5.6 en Ubuntu 14.04 hay que seguir los siguientes pasos:

Añadir a los repositorios de apt-get el repositorio de ondrej/php

```
sudo add-apt-repository ppa:ondrej/php
```

Una vez añadido actualizar las referencias en apt-get

```
sudo apt-get update
```

Con este repositorio en nuestro sistema, debe de encontrarnos el paquete correctamente para instalarlo con el siguiente comando:

```
sudo apt-get install php-mongo
```

Este último comando debe de crearnos el archivo mongo.ini con la depencencia con el .so añadida. Si no lo hace automáticamente por
alguna razón, podemos hacerlo a mano:

```
sudo echo "extension = mongo.so" > /etc/php5/mods-available/mongo.ini
```