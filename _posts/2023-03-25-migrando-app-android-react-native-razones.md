---
layout: post
title: 'Migrando aplicación Android a React native: ¿Por qué?'
date: '2023-03-25'
author: Miguel G. Flores
tags:
- android
- react native
---

### Estado actual
Desde hace mucho tiempo he estado manteniendo la versión [Android de Empire Strike](https://play.google.com/store/apps/details?id=com.empire_strike.app), 
con ciertas partes implementadas nativamente y otras a través de una WebView para introducir toda la interfaz 
responsible del juego dentro de la app de Android.

Las funciones más importantes que hace la app nativamente, son:
- Gestión de login con Google (+ Google Play), Facebook y Usuario/contraseña.
- Gestión de push notifications
- Configuración de push notifications
- Gestión de in-app payments

No son más de 5/6 vistas nativas y el resto gestionado dentro de la webview, con una buena comunicación web <-> app, 
haciendo que pueda intercambiar sesiones entre ellas y gestionar llamadas entre ellas para pasar de nativo a web en 
cualquier momento.

### Siguientes pasos
Como la mayor base de usuarios del juego utiliza Android y el juego es 100% funcional dentro de web, hacer una 
aplicación iOS no ha sido nunca una prioridad (especialmente dentro del poco tiempo que puedo dedicar), 
pero desde que hace un tiempo estoy usando iPhone personalmente para conocer a el usuario desde distintos 
puntos de vista, más me doy cuenta que hace falta tener una aplicación para iOS.

Sin embargo, el tiempo disponible para hacer una aplicación Nativa, incluyendo aprender swift (y usar xcode) 
no me permite embarcarme en un proceso de este calibre, ni mucho menos mantener dos bases de código independientes 
por tiempo indefinido.

### Migrando Empire Strike a React Native
La decisión está clara, utilizaré un sistema que convierta a nativo para poder tener lo mejor de los dos mundos, 
una única base de código que me permita crear ambas aplicaciones, ya la vez que vaya construyendo la aplicación para 
iOS con el nuevo framework, ir migrando las pantallas que se mejoren en Android también.
