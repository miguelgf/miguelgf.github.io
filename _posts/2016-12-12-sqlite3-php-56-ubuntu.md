---
layout: post
title: 'Instalar extensión SQLite en PHP 5.6 en Ubuntu'
date: '2016-12-12'
author: Miguel González
tags:
- php
- php5.6
- sqlite
- linux
- ubuntu
---

Para instalar el driver de Sqlite3 para PHP 5.6 en Ubuntu 14.04 hay que seguir los siguientes pasos:

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
sudo apt-get install php5.6-sqlite3 
```


Según la instalación de PHP como se haya realizado, la localización del mods-available estará en la localización `/etc/php5` o en `/etc/php/{version}`,
por ejemplo en mi sistema que tengo concurrentemente las versiones 5.5, 5.6 y 7.0, las localización para sqlite3.ini sería en `/etc/php/5.6/mods-available`.

Para comprobar que se ha intalado correctamente inspeccionamos la configuración de PHP para ver que existen los registros de configuración de sqlite

```
php -i | grep sqlite
```

Deberemos tener un resultado parecido a:

```
/etc/php/5.6/cli/conf.d/20-pdo_sqlite.ini,
/etc/php/5.6/cli/conf.d/20-sqlite3.ini,
PDO drivers => mysql, pgsql, sqlite
pdo_sqlite
sqlite3
sqlite3.extension_dir => no value => no value
```