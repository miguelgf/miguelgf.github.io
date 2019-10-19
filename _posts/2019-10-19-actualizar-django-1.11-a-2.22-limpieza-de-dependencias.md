---
layout: post
title: 'Actualizar Django 1.11 a 2.22: Limpieza de dependencias'
date: '2019-10-19'
author: Miguel G. Flores
tags:
- python
- django
---

# Actualizar Django 1.11 a 2.22: Limpieza de dependencias

Cuando trabajamos en proyectos legados, donde una cantidad ingente de código llega a nuestras manos y ha pasado por decenas de programadores, uno de los patrones comunes que se puede encontrar entre todos los proyectos es una cantidad de código muerto y sin uso muy grande.

A un programador se le suele evaluar por la cantidad de funcionalidad que añade y es lo que se busca, añadir más y más funcionalidad a un sistema, pero la parte de eliminar lo que ya no se usa o reemplazar dependencias obsoletas es algo que nunca ocurre, y cuanta más gente distinta pase por el proyecto, más grande es la bola de nieve. 

Esto viene también asociado a la seguridad que te da realizar acciones. Al añadir algo vas a probarlo (al menos manualmente) y ver que tu funcionalidad está operativa, pero, al borrar algo, ¿Cómo sabes que no ha dejado de funcionar algo que ni conoces? El miedo es tan grande y la ganancia (visible) tan pequeña, que la opción elegida mayormente es dejarlo ahí para siempre.

Este problema se extrapola también a las dependencias externas de un proyecto. ¿Quién sabría decirme de todas las dependencias instaladas en su proyecto legado, qué es cada una y para qué se está usando? 

### Problemas del archivo requirements.txt

En el caso de Python, por lo general, vamos a encontrar estas dependencias en el archivo `requirements.txt`, una lista de nombre de paquetes y la versión de deseada, de la cual algunas nos sonará para que se usan y otras no tendremos ni idea.

El caso concreto de Python plantea un problema añadido, el nombre del paquete y su `import` no tienen porque coincidir, por lo que tendremos paquetes como puede ser el caso de `beautifulsoup4`, donde su linea en el requirements.txt sería algo como:

    beautifulsoup4==4.8.0

Pero su import en un archivo

    from bs4 import BeautifulSoup

Por lo que una búsqueda en el proyecto de la palabra "beautifulsoup4" no nos dará resultados aunque se esté usando.

### Detectar dependencias en uso en base a los imports

Para detectar las dependencias que sí estamos usando en nuestro proyecto, existe la herramienta [https://github.com/bndr/pipreqs](https://github.com/bndr/pipreqs) que permite analizar estáticamente el código fuente de un proyecto y generar un archivo requirements.txt con la dependencias que sí están en uso.

Este es el primer paso, con el que podemos a ciencia cierta determinar las dependencias que sí estamos usando, su uso sería:

    $> pip install pipreqs
    $> pipreqs .
    INFO: Successfully saved requirements file in ./requirements.txt

**Warning**: La versión de las dependencias que te genera **no** es la versión que tú tienes instalada, pero sí sirve para determinar cuales están en uso en el proyecto.

A partir de este punto, la diferencia de dependencias son las que tenemos en duda. Python al ser un lenguaje dinámico puede ser que haga imports de estas librerías en tiempo de ejecución o bajo algunas premisas, por ejemplo casi todas las extensiones usadas por  `Django` del estilo `django-xxx` no van a aparecer en la lista pero puede ser que sí las esté usando tu proyecto.

Lamentablemente, a partir de aquí la única manera de actuar es analizar una por una la dependencia de esta lista más reducida y para que se usa, ser valiente, cubrir con la mayor cantidad de test tu aplicación y probar que ocurre al eliminarla.
