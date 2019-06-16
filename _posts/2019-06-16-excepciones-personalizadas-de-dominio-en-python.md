---
layout: post
title: 'Excepciones personalizadas de dominio en Python'
date: '2019-06-16'
author: Miguel G. Flores
tags:
- python
- clean code
---

# Excepciones personalizadas de dominio

Cuando estamos tratando dentro del dominio de nuestra aplicación, lanzar excepciones genéricas puede tener sentido en algunas ocasiones, pero en otras tantas tiene más sentido lanzar excepciones específicas de dominio. De manera que una excepción exprese unívocamente un comportamiento inesperado y pueda capturarse de manera única.

Esto nos va a ayudar con la semántica del código y proporcionar unos errores más semánticos, encapsular los mensajes de las excepciones y que estos sean únicos para una excepción.

Por ejemplo, en nuestro código podríamos tener algo así:

```python
    if user.city not in allowed_cities:
    		raise Exception(f'The city {user.city} is not allowed')
```

En este caso estamos lanzando una excepción del tipo `Exception` genérica, que cualquier `except` va a capturar, pasándole un mensaje de error personalizado en ese momento, definiendo el formato del mensaje fuera de la excepción.

Este mismo caso puede refactorizado a una excepción de dominio personalizada, en la que semánticamente el error esté claro e independientemente de donde se llame a esta excepción, obtengamos el mismo mensaje.

```python
    # exceptions.py
    class CityNotAllowedException(Exception):
    		message = 'The city {city} is not allowed'
    		
    		def __init__(self, city: str) -> None:
    				self.city = city
    				super().__init__(self.message.format(city=city))
    
    # service.py
    from .exceptions import CityNotAllowedException
    
    # ...
    	
    if user.city not in allowed_cities:
    		raise CityNotAllowedException(user.city)
```

De este modo, la excepción está hablando nuestro lenguaje de dominio, el log que guardamos o enviamos a otro servicio externo (Rollbar, Papertrail, ...) va a ser mucho más concreto. El mensaje contenido en la excepción siempre va a ser el mismo sea donde sea que se lance la exceptión (cambiando el parámetro de entrada), evitando tener que duplicar el texto en todos los lugares donde se compruebe la ciudad y se lance una excepción similar y obligando a que un mensaje de excepción siempre sea igual para todas las excepciones del mismo tipo.

Así no se daría el caso de tener dos exceptiones indicando que la ciudad no está permitida con dos mensajes distintos (por ejemplo: `The city {city} is not allowed` y  `We don't allow the {city}`)

Esto nos añade un extra muy interesante, ya que de esta manera de lanzar excepciones, especialmente en Python, nos va a permitir que si nuestro logger es más o menos inteligente, podamos enviar los parámetros por separados y saber que ciudades están lanzando más excepciones, ya que la excepción contiene estos parámetros.

```python
    try:
        raise CityNotAllowedException('Barcelona')
    except CityNotAllowedException as e:
        print(e.city)
```

Aquí obtenemos como resultado `Barcelona`
