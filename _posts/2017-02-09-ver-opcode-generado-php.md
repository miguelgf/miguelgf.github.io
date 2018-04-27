---
layout: post
title: 'Ver OPCode generado por un archivo PHP'
date: '2017-02-09'
author: Miguel G. Flores
tags:
- php
- zend
- opcode
---

Introducción
===

¿Cómo funciona PHP?
---
Resumiendo muchísimo el funcionamiento del proceso de ejecución de un archivo PHP, los pasos que se siguen son:

- Escaneo del archivo: Se convierte el archivo a una serie de tokens que delimitan las distintas partes del archivo 
([Más info](http://php.net/manual/es/function.token-get-all.php))
- Parseo de los tokens: Cada grupo de tokens es agrupado en expresiones simples.
- Compilación: Las expresiones simples se convierten a códigos de operaciones (instrucciones para el procesador de la máquina)
 ([Más info: Opcode](https://es.wikipedia.org/wiki/C%C3%B3digo_de_operaci%C3%B3n))
- Ejecución: Finalmente se ejecutan los códigos OPCodes y el programa hace lo que tenga indicado paso a paso.

¿Qué es OPCode?
---
El OPCode son las instrucciones que obtenemos del tercer paso indicado anteriormente. Es común en las aplicaciones en
producción cachear éste código una vez que se realizan los tres primeros pasos para sólo ejecutar el opcode cuando
se pide un archivo y mejorar el rendimiento.

Un ejemplo de OPcode. Para el siguiente código simple:

```php
$suma = 1 + 2;
echo $suma;
```

Las instrucciones a ejecutar en el procesador de la máquina serian las siguientes:

```
ine     #* E I O op                           fetch          ext  return  operands
-------------------------------------------------------------------------------------
   2     0  E >   ADD                                              ~0      1, 2
         1        ASSIGN                                                   !0, ~0
   3     2        ECHO                                                     !0
   4     3      > RETURN                                                   1

```

Hay una [lista de operaciones](http://www.php.net/manual/en/internals2.opcodes.list.php) donde consultar que se 
realiza en cada operación.

¿Para qué quiero saber yo ésto?
---
Bueno si estás leyendo ésto será porque te interesa. Pero dejando tonterías aparte. Es importante en determinadas
circustancias saber el código que se está ejecutando finalmente en la máquina en la que corre un archivo PHP. Analizando
el código ejecutado se puede optimizar a bajo nivel. Muy útil cuando se están creando extensiones de PHP o desarrollando
un framework que van a usar muchas otras aplicaciones.

Como obtener el OPCode de un archivo PHP
===

Para generar el código como el mostrado anteriormente, hay que usar alguna librería/extensión que nos transforme
el código PHP a operaciones. Una muy buena para nuestro propósito es [Vulcan Logic Disassembler](https://github.com/derickr/vld).


Instalación de VLD
---
Hay varias maneras de instalar la extensión. Vamos a instalarla usando un gestor de dependencias (Pickle). Otra
de las maneras sería compilando la extensión con phpize, en [su repositorio](https://github.com/derickr/vld) 
indican las instrucciones para hacerlo.

Para usar Pickle tenemos que instalarlo desde su repositorio clonándolo o bajarnos el .phar ya funcional.

- [Descargar pickle.phar v0.4.0](https://github.com/FriendsOfPHP/pickle/releases/download/v0.4.0/pickle.phar). Otras
versiones anteriores o más recientes pueden encontrarse en la [Página de descarga de su respositorio](https://github.com/FriendsOfPHP/pickle/releases).

Una vez descargado, lo usamos para instalar la extensión vld:

```
$ sudo pickle.phar install vld

  - Installing vld (latest-stable)
    Downloading: 100%         

+-----------------------------------+--------+
| Package name                      | vld    |
| Package version (current release) | 0.14.0 |
| Package status                    | beta   |
+-----------------------------------+--------+
```

Debemos ahora activar la extensión en PHP. Ésta instalación se está haciendo en PHP5 pero también está activa para PHP7.

```
$ echo 'extension=vld.so' | sudo tee /etc/php5/mods-available/vld.ini
$ sudo php5enmod vld
$ sudo service php5-fpm restart
```

Para comprobar que tenemos la extensión instalada y habilitada podemos comprobarlo de la siguiente manera:

```
$ php -m | grep vld 
```

Generar opcode de un archivo
---

El modo de uso de la extensión es el siguiente:

``` 
php -d vld.active=1 -d vld.execute=0 -f archivo_test.php
```
