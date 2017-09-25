---
layout: post
title: 'Pasar argumentos de línea de comandos en Xcode'
date: '2017-09-25'
author: Miguel González
tags:
- xcode
- ios
---

Cuando queremos pasar argumentos por línea de comandos a la ejecución o build de una aplicación para iOS desde Xcode, como por ejemplo en el caso que queramos hacer pruebas con el DebugView de Firebase, necesitaremos pasar el parámetro `-FIRDebugEnabled`. Se consigue de la siguiente manera:

En Xcode, pulsamos sobre el nombre de la aplicación y en el menú seleccionamos `Edit schema...`:

![Menu Edit Schema Xcode]({{ site.url }}/ssets/images/2017-09-25/menu-app-xcode.png)

En la ventana que se nos abre, en la pestaña de `Arguments` añadimos los argumentos que necesitemos:

![Cambiar argumentos pasados por linea de comando]({{ site.url }}/assets/images/2017-09-25/arguments-xcode-command-line.png)
