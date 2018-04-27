---
layout: post
title: 'Asegurando estándares de código (PSR) en el desarrollo'
date: '2017-02-16'
author: Miguel G. Flores
tags:
- php
- PSR
---

# Introducción
Mantener una base de código es algo vital en todas las empresas. Cualquier programador pasará más
del 80% del tiempo dedicado a programar leyendo código (suyo o de otros) que escribiendo nuevo código. Por
lo que asegurar que este código sea limpio, bien estructurado y que cumpla unos estándares acordados por
todo el equipo es algo muy importante.

Durante muchos años en PHP no había un estándar de código bien definido, si bien estaban algunos muy bien
documentados como el de PEAR o Zend, no había una linea clara y definida. Desde hace ya unos años un
grupo de personas relativas a distintos frameworks ([FIG](http://www.php-fig.org/)) han estado diseñando una 
serie de recomendaciones a seguir (PSRs).

Vamos a ver como asegurar que nuestro código cumple éstos estándares con una alerta antes de realizar un commit
en Git y que siempre el código que subamos al repositorio cumpla con los estándares PSR2.

![XKCD Calidad código]({{ site.url }}/assets/images/2017-02-21/calidad-codigo-comic.png)
Imagen: https://xkcd.com/1513/

# Herramientas
##### Instalación de PHP Code Sniffer
El programa encargado de comprobar y analizar nuestro código será [PHP Code Sniffer](https://github.com/squizlabs/PHP_CodeSniffer). En
su repositorio podemos consultar todas las releases que tienen. A fecha de escritura de este post la
última versión estable es la [2.8.0](https://github.com/squizlabs/PHP_CodeSniffer/releases/tag/2.8.0).

```
wget https://squizlabs.github.io/PHP_CodeSniffer/phpcs.phar
chmod +x phpcs.phar
sudo mv phpunit.phar /usr/local/bin/phpcs
```

```
wget https://squizlabs.github.io/PHP_CodeSniffer/phpcsbf.phar
chmod +x phpcsbf.phar
sudo mv phpcsbf.phar /usr/local/bin/phpcsbf
```

Con éstos comandos nos bajaremos los ejecutables de phpcs y phpcsbf, moviéndolos a `/usr/local/bin/` para que puedan
ser ejecutados sin llamar a la ruta completa.

##### Uso con parámetros para comprobar PSR1 y PSR2
Ya con CodeSniffer en la máquina, podemos ir a nuestra base de código y realizar las comprobaciones en el código. 
Los comandos admiten distitos parámetros, pero la forma más básica de usarlos es la siguiente:

```
phpcs --standard=PSR1,PSR2 --extensions=php ./src
```

Con éste comando nos va a comprobar el estándar `PSR1` y `PSR2` en todos los archivos con extensión `.php` dentro
de la carpeta `./src`.

Con los mismos parámetros puede ejecutarse el fixer, que no solo comprobará los errores sino que también intentará
arreglar todos los que sea posible de forma automática.

```
phpcbf --standard=PSR1,PSR2 --extensions=php ./src
```

##### PHP Lint
Otra pequeña herramienta que vamos a usar, no es una herramienta en si, sino una opción del ejecutable de PHP para
comprobar la sintaxis de los archivos, para comprobar que el código va a ser ejecutable (independientemente de fatal 
errors, warnings, ...), simplemente que la sintaxis sea correcta.

```
php -l archivo.php
```

# Git hooks
Algo muy incómodo en los repositorios de control de versiones es encontrarse con múltiples commits 
de fixs de código después de haberlos subido al repositorio, o sin haberlos subidos por no [agregar los
cambios al commit anterior en Git](http://www.miguelg.com/2016/09/agregar-cambios-commit-anterior-git.html).

Vamos a ver la manera de comprobar éstos cambios en tu máquina local, antes de realizar un commit, capturando
el evento de `pre-commit` de git para ejecutar las comprobaciones de código.

##### Hook para un solo proyecto en pre-commit
Los hooks de cada proyecto se almacenan en la raiz del proyecto, dentro de `.git/hooks/`, por defecto hay unos
archivos de ejemplo de otras tareas. En nuestro caso vamos a crear un archivo `pre-commit` que va ejecutarse siempre antes
de realizar un commit.

He creado un gist con un ejecutable para comprobar con PHP Lint y Code Sniffer el código, es muy simple
y puede configurarse para realizar distintas comprobaciones o cambiar la salida que muestra.

<script src="https://gist.github.com/infinit89/4c3f0dfda663c2fe047ec27d9989fdef.js"></script>

El archivo `pre-commit` debe tener permisos de ejecución. Los comandos a ejecutar para bajarse el git,
dar permisos y comenzar a usarlo son los siguientes:

En la raiz del directorio del proyecto:

``` 
curl -L https://gist.githubusercontent.com/infinit89/4c3f0dfda663c2fe047ec27d9989fdef/raw/adc879591d0cc063768eef1c591762183bca03fb/pre-commit -o .git/hooks/pre-commit
sudo chmod +x .git/hooks/pre-commit
git init
```

Con esto sería suficiente para realizar las comprobaciones.

##### Hook para todos los proyectos en pre-commit
Git permite tener templates de hooks para reusar en todos los proyectos. Éstos templates deben de estar localizados
en `~/.git-templates/hooks`. Siguiendo los pasos anteriores de puede copiar el archivo a la carpeta de templates
y en cada proyecto ejecutar `git init` para reestablecer la configuración de Git. De esta manera todos
los nuevos proyectos heredarán el hook de pre-commit.

##### Conclusión
Con éstas prácticas la calidad del código que subes a repositorios debe de mejorar, ser más legible para todos
los programadores y mantener un estandar común. Tras configurar éstos hooks es cuestión de tiempo acostumbrarse
a escribir el código correcto de primeras.

