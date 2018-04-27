---
layout: post
title: 'Cobertura de código de los tests en PHPStorm'
date: '2016-11-28'
author: Miguel G. Flores
tags:
- php
- phpunit
- phpstorm
---

La cobertura de código es una medida expresada en porcentaje del código fuente que se ejecuta respecto al código fuente total al pasar una batería de tests. La idea
general es mantener un porcentaje de cobertura lo más alto posible, en el que más parte de nuestro código esté bajo tests. En cualquier caso, no es una medida que esté
ligada con tests de calidad y en muchos casos puede ser una [medida sin sentido](http://blog.ploeh.dk/2015/11/16/code-coverage-is-a-useless-target-measure/).

Vamos a ver como configurar PHPStorm para analizar la cobertura del código. 

Paso 1: Instalar Xdebug
======

En primer lugar debemos tener instalado [Xdebug](https://xdebug.org/) como extensión de PHP. Para instalarlo en PHP5.6 ejecutamos:

```
sudo apt-get install php-xdebug
```

Para comprobar que se ha intalado correctamente inspeccionamos la configuración de PHP para ver que existen los registros de configuración de xdebug

```
$ php -i | grep xdebug
/etc/php/5.6/cli/conf.d/20-xdebug.ini,
xdebug
xdebug support => enabled
xdebug.auto_trace => Off => Off
xdebug.cli_color => 0 => 0
xdebug.collect_assignments => Off => Off
xdebug.collect_includes => On => On
xdebug.collect_params => 0 => 0
xdebug.collect_return => Off => Off
xdebug.collect_vars => Off => Off
xdebug.coverage_enable => On => On
xdebug.default_enable => On => On

```

Paso 2: Instalar PHPUnit y configurarlo en PHPStorm
======

Si en nuestro proyecto estamos usando composer, lo mejor es instalarlo a [través de composer](https://phpunit.de/manual/current/en/installation.html#installation.composer). Si no seguir los [pasos de instalación](https://phpunit.de/getting-started.html) de la web de PHPUnit.

En PHPStorm debemos indicar la ruta de la instalacioń de PHPUnit. Vamos a las Settings `Ctrl + Alt + S` y en la parte superior izquierda buscamos phpunit. Dentro del apartado
de `Languages & Frameworks > PHP` está la opción de PHP, en esta ventana indicamos la ruta al archivo phpunit.phar. En el caso de la captura se está indicando la ruta al archivo de phpunit instalado a través de composer.

![Config PHPUnit en PHPStorm]({{ site.url }}/assets/images/2016-11-28-phpstorm-phpunit.png)

Paso 3: Definir suite de tests en PHPStorm
======

Vamos a definir la batería de tests que queremos correr, suponiendo que tengamos una, sino podemos [crear nuestros primeros test](https://phpunit.de/documentation.html).

![Config PHPUnit en PHPStorm]({{ site.url }}/assets/images/2016-11-28-phpstorm-phpunit-gif.gif)

Paso 4: Correr test para medir cobertura
======

Con las configuraciones realizadas previamente, tendremos activo el icono de correr test y analizar cobertura. Cuando estos test se pasen nos mostrarán toda la estructura
de directorio y de cada archivo el porcentaje de líneas que han sido procesadas, por cada carpeta y archivo.

Para correr los tests, se puede puslar en la parte superior derecha en el siguiente icono:

![Config PHPUnit en PHPStorm]({{ site.url }}/assets/images/2016-11-28-run-coverage-test-phpstorm.png)

O en el menú `Run > Run 'PHP Unit' with coverage`
