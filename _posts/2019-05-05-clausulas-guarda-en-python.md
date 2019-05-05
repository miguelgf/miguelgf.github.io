---
layout: post
title: 'Clausulas de guarda en Python'
date: '2019-05-05'
author: Miguel G. Flores
tags:
- python
- clean code
---

# Clausulas de guarda
Las cláusulas de guarda actuan de la misma manera en todos los lenguajes de programación, en este caso especificamos en python porque es el lenguaje de los ejemplos.

Una clausula de guarda es una pieza de código que normalmente está al comienzo del método y comprueba una serie de condiciones para continuar con la ejecución o cortarla. Es muy usada cuando programamos sin usar **else** (lo cual es una muy buena práctica para mantener un código limpio y entendible), cuando no queremos tener muchos niveles de identación del código y para ayudar a mejorar la legibilidad y semántica del código.

Vamos a ver a través de un ejemplo como las cláusulas de guarda pueden ayudar a mejorar la legibilidad del código.

Tomemos esta pieza de código como ejemplo, está extraido de una pieza de código real de producción simplificada para hacer el ejemplo más fácil.

```python
class ACL:
	def can_access(user: User, permission: str) -> bool:
	    if not user.is_superuser():
	        if not user.is_blocked():
	            for allowed_permission in user.permissions:
			if permission == allowed_permission:
				return True
	        else:
	            raise Exception('User is blocked')
	    else:
			return True

	    return False
```

En este código tenemos (partiendo de la definición de la función) 5 niveles de identación, lo cual dificulta la lecutura y aumenta la complejidad cognitiva.

Lo primero que vamos a intentar es eliminar los `else`, ya que en general nunca son necesarios y eliminarlos deja el código más limpio y claro. En el código podemos ver que solo se comprueban permisos si NO es un super usuario, en caso contrario siempre se devuelve bool. Vamos a definir una clausula de guarda para este caso, en el que los superusuarios se detecten al comienzo y se corte la ejecución.

```python
class ACL:
	def can_access(user: User, permission: str) -> bool:
    	if user.is_superuser():
			return True

    	if not user.is_blocked():
        	for allowed_permission in user.permissions:
				if permission == allowed_permission:
					return True
    	else:
        	raise Exception('User is blocked')

		return False
```

Primer `else` eliminado, se comprueba si es superusuario y se devuelve True en ese caso. En segundo lugar vemos que los usuarios bloqueados están restringidos también, en este caso con una excepción en caso de que estén bloqueados, hacemos el mismo ejercicio.

```python
class ACL:
	def can_access(user: User, permission: str) -> bool:
    	if user.is_superuser():
			return True

    	if user.is_blocked():
			raise Exception('User is blocked')

    	for allowed_permission in user.permissions:
			if permission == allowed_permission:
				return True

		return False
```

Hemos vuelto a bajar el nivel de identación, además de dejar claro que el último bloque se va a ejecutar únicamente cuando las dos condiciones previas se cumplan. En este caso concreto, al estar levantar una excepción, podemos encapsularla dentro de un método privado que le de semántica al código, en caso de que la comprobación fuese más compleja ayudaría mucho.

```python
class ACL:
	def can_access(user: User, permission: str) -> bool:
    	if user.is_superuser():
			return True

    	self.__check_user_is_blocked(user);

    	for allowed_permission in user.permissions:
			if permission == allowed_permission:
				return True

		return False

  	def __check_user_is_blocked(user: User) -> None:
		if user.is_blocked():
			raise Exception('User is blocked')
```

Esta es una manera simple y efectiva de simplificar nuestro código, que sea más legible y fácil de entender para cualquier persona que lo lea. Añade más semántica y reduce la carga cognitiva de entener el código.
