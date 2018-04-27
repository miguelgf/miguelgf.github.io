---
layout: post
title: 'Usar Cygwin como terminal con PHPStorm en Windows'
date: '2016-11-27'
author: Miguel G. Flores
tags:
- programación
- tools
---

Recientemente he actualizado a la versión de [PHPStorm 2106.3](https://www.jetbrains.com/phpstorm/whatsnew/), como suele
ocurrir con este tipo de actualizaciones tenemos la eterna molestia de que se pierde gran parte de la configuración del
IDE. Una de las cosas que tenía configurada antes y que he perdido es la terminal que muestra por defecto PHPStorm al 
abrir un terminal (**Alt + F12**). 

He tenido que volver a leer varios post de blogs para recordar la solución que utilicé. Resumo en este post los pasos a 
seguir para futuros posibles problemas.

En mi caso yo tengo instalado [Cygwin](https://www.cygwin.com/) junto a [Babun](http://babun.github.io/). Si en la 
configuración de terminal de PHPStorm indicas el bash.exe como terminal no vas a obtener todas las ventajas de la terminal,
que me interesa que tenga el estilo zsh.

Para comenzar vamos a crear un archivo de Batch Scripting de Windows, con el siguiente contenido:

```
@echo off
set currentdir=%cd:\=/%
@echo cd %currentdir% > "D:\babun\.babun\cygwin\home\%USERNAME%\.bashrc_cd"
set CHERE_INVOKING=1 & D:\babun\.babun\cygwin\bin\bash.exe --login -i
```

He guardado este archivo bajo `D:\Babun\.babun\cygwin.bat`. En mi caso mi instalación de Babun está en `D:\babun\`, esa 
ruta hay que cambiarla por donde sea que esté instalado, al igual que la instalación de cygwin que puede estar en otra 
ubicación y no dentro de .babun también, según como se haya realizado la instalación.

Ahora llega el momento de configurarlo en PHPStorm. La configuración de terminal puede realizarse por proyecto individual
o para todos los proyectos. Vamos a `File > Settings...` para la configuración para este proyecto o a 
`File > Default Settings...` para todos los futuros proyectos. En la parte superior izquierda buscamos "Terminal" y 
vamos a su sección bajo Tools. En el primer cuadro de texto indicamos el Shell path, indicando el archivo que acabamos
de crear.

![Cywgin con PHPStorm]({{ site.url }}/assets/images/2016-11-27-cygwin-phpstorm.png)
