---
layout: post
title: 'Arrancar Selenium server con Chrome Webdriver en Windows'
date: '2017-01-16'
author: Miguel González
tags:
- selenium
- testing
- chrome webdriver
---

Para arrancar el server de Selenium (3.0.1 en éste caso) y configurarlo para poder usar Chrome como navegador
vamos a necesitar `Java`, `Selenium server` y el `webdriver` de Chrome.

Instalación de Java 1.8
---

Vamos a la página de [descarga de Java](https://www.java.com/es/download/), descargamos Java 8 de manera gratuita,
obtendremos un archivo ejecutable y que sólo tendremos que ejecutar y se instalará en windows en la carpeta 
predeterminada.

En una consola al escribir `java -version` debemos obtener un resultado como:

````
C:\Users\miguelg.com>java -version
java version "1.8.0_111"
Java(TM) SE Runtime Environment (build 1.8.0_111-b14)
Java HotSpot(TM) Client VM (build 25.111-b14, mixed mode, sharing)
````

Para conocer la ruta de instalación podemos consultar el `JAVA_HOME` en una consola con `echo %JAVA_HOME%`

Descargar Selenium Server
---

El archivo de java standalone de selenium podremos encontrarlo en su [página oficial](http://www.seleniumhq.org/download/),
el enlace a la descarga directa del Selenium Standalone Server 3.0.1 puede encontrarse aquí:

[http://selenium-release.storage.googleapis.com/3.0/selenium-server-standalone-3.0.1.jar](http://selenium-release.storage.googleapis.com/3.0/selenium-server-standalone-3.0.1.jar)

Por defecto se usará Firefox como navegador.

Descargar Chrome Webdriver
---

El ejecutable `.exe` para windows podemos encontrarlo en la siguiente dirección:

[https://chromedriver.storage.googleapis.com/index.html](https://chromedriver.storage.googleapis.com/index.html)

A fecha de este post la última versión es la `2.27` y está funcionando correctamente, el enlace directo a la descarga
para windows es la siguiente (archivo .zip):
 
[https://chromedriver.storage.googleapis.com/2.27/chromedriver_win32.zip](https://chromedriver.storage.googleapis.com/2.27/chromedriver_win32.zip)


Arrancar Selenium con Chrome Webdriver
---

Si el archivo Jar de Selenium como el .exe del chromedriver, la manera de ejecutar selenium es la siguiente:

```
java -jar selenium-server-standalone-3.0.1.jar -Dwebdriver=chromedriver.exe
```

Pudiendo cambiar la ruta de ámbos archivos a rutas relativas o absolutas. Por defecto el puerto en el que escucha
selenium es el `4444` y el host `localhost`.
