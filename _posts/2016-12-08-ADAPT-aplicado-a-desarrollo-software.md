---
layout: post
title: 'Metodologia ADAPT aplicada al desarrollo del software'
date: '2016-12-08'
author: Miguel González
tags:
- scrum
- equipos
- desarrollo
- software
---

Origen de ADAPT
----

El acrónimo ADAPT lo usa Mike Cohn para definir las fases por las que debe pasar un equipo
para **ADAPT**arse (ADAPTing) a la metodología Scrum, esta forma de pensar y de afrontar
la integración de cualquier cambio me ha parecido especialmente buena. En este post intentaré
resumir las partes que considero apropiadas para aplicarlas al propio desarrollo del software
en un equipo, para introducir cualquier metodología, tecnología o cambio en la forma de trabajar.

ADAPT aplicado al desarrollo del software
----

### Concienciación del equipo (*Awareness*)

- **Comunicar que hay un problema**: Es estrictamente necesario sacar a relucir los problemas que atraviesa un equipo. 
Muchos integrantes pueden pensar que el sistema que usan actualmente está perfecto porque están habituados a ello o 
porque no conocen otras (mejores) maneras de realizar la tarea. En este momento no es necesario aportar ninguna solución
ni proponer un cambio, este es momento de concienciar a todo el equipo de que algo no está funcionando.
- **Hacer ver el problema**: Junto al punto anterior debe ir este, no sólo por comunicar el problema desde un canal de
información vertical la información va a calar en el equipo, hay que hacer ver a el equipo la realidad fuera de la empresa
y como se ejecutan las tareas en otros equipos, una buena manera de concienciar y hacer ver los problemas es atender a 
charlas, conferencias, meetups o cursos.
- **Usar métricas**: En equipos hetereogéneos no todas las formas de hacer ver el problema son la misma, en mentes más
analistas y matemáticas, una buena forma de hacer ver el problema es a través de métricas. Por ej. para hacer ver que
un equipo necesita tener un proceso de validación y testing es la métrica de cambios sobre historias de usuarios ya
realizadas o cantidad de bugs que se reportan.
- **Focalizarse en las razones importantes**: Siempre hay que intentar hacer foco en la unidad más pequeña para realizar
el cambio pues será más fácil cambiar una cosa cada vez que todo de golpe. De primeras se deberían de abordar sólo
las partes más criticas y focalizarse en ellas (Ej: "No estamos entregando valor al final de cada sprint", "La 
cantidad de bugs que nos reportan es mayor cuanto más desarrollamos", ...)
- **Proyecto piloto**: Una de las mejores maneras (sino la mejor) de demostrar que algo funciona es mostrar casos
de éxito. Si un proyecto piloto se realiza y es un éxito total, es practicamente imposible negarse a que éste cambio
es el deseado para el equipo.

### Deseo de cambiar (*Desire*)

- **Comunicar que hay una manera mejor de hacer las cosas**: Dejando atrás el momento de comunicar el problema pasamos
 a atacar el problema, importando herramientas, metodologías o prácticas que atajen estos problemas (Ej: "Nuestras
 historias de usuario deben tener unas definiciones de cuando la historia está completa", "Vamos a realizar tests
 para no aumentar la cantidad de bugs", ...). Es importante separar el primer punto de la lista anterior de comunicar
 que hay un problema de éste punto de comunicar una solución, ya que si los juntamos, puede ocurrir que se niegue la
 existencia del problema por el simple hecho de no gustarle o no querer adaptarse a la solución propuesta.
- **Crear el sentido de la urgencia de cambio**: Ésta es una de las partes más complicadas pero más importante, crear
la sensación de urgencia, no sólo de que hay que eliminar el problema, sino de que hay que eliminarlo ya. Explicar
las consecuencias de no comenzar con el cambio cuanto antes es un buen punto por el que comenzar.
- **Hacer una prueba o demo en un entorno real**: Dedicar una pequeña parte del equipo a realizar esta nueva acción
sin tener que cambiar todo el sistema actual del equipo es muy recomendable para hacer ver las virtudes del cambio. En
este punto es importante incluir en esta prueba a la gente que está más motivada hacía el cambio que a la gente que no
lo está. Aunque parezca contradictorio, es mejor implicar a los que desean cambiar y darle las herramientas y que sean
los primeros en usarlo, porque te garantizarás que la prueba sea un éxito y de manera indirecta repercurirá en los
integrantes reacios al cambio.
- **Focalizarse en combatir el miedo**: La manera en la que nos comportamos usualmente está influenciada por lo que
tememos. Cada individuo tendrá sus razones por las que no quiera afrontar el cambio, algunos puede ser por miedo a 
perder su "status" en el equipo actual, otros porque piensen que no van a ser capaces de adaptarse a la tecnología u
otros simplemente por mentalidad reacia al cambio. Hay que abordar los miedos individuales de cada uno y hacerles ver
que podría aportarles a cada uno el nuevo cambio respecto a su estado anterior.

### Habilidad (*Ability*)

- **Cursos y formaciones**: Si ha habilidad técnica que se intenta aplicar en el equipo no es conocida por el líder
técnico u otro equipo no tiene la capacidad de transmitir el conocimiento, una de las mejores maneras de insertar
este conocimiento es a través de cursos y formaciones. Para practicamente todas las áreas hay empresas con personal
cualificado para impartir cursos y transmitir las aptitudes técnicas a el equipo.
- **Compartir conocimiento e información**: El conocimiento no debe transmitirse unicamente de forma vertical, sino
de forma horizontal. Un miembro del equipo puede haber estado indagando sobre esta información o haber tenido más
tiempo para dedicarle a la tecnología que se quiere implantar. Realizar charlas internas y talleres para que un miembro
del equipo transmita su conocimiento es una excelente práctica.
- **Establecer objetivos razonables**: Unos objetivos razonables, limitados y con sentido son la base para evitar el
desmotivamiento. Si se esperan resultados en un corto plazo de tiempo con una ambición demasiado amplia, es muy probable
no cumplir éstos objetivos y crear un sentimiento de fracaso general. Mientras que con límites más ajustados, se crea el
sentido justo contrario, de conseguir una pequeña meta y ganas de avanzar hacía la siguiente.
- **Simplemente hazlo**: Las cosas no tienen porque funcionar a la primera y todos podemos equivocarnos. Integrar
una nueva tecnología puede ser cuestión de simplemente meterse manos a la obra con ella, trastear, probar y equivocarse
usándola siempre dentro de un límite de poca criticidad. A veces desde dentro las cosas son más fáciles de lo que 
pueden parecer desde fuera.

### Promover (*Promotion*)

- **Publicar historias de éxitos**: Una vez que hemos mejorado el equipo en cualquier sentido, siempre habrá un beneficio
directo que podremos comunicar al resto de equipos, ya sean del mismo departamento o no. Hacer visible la mejora 
en un equipo y como ésto afecta a la mejora del trabajo general es motivante tanto para el equipo interno como para el
equipo que mira desde fuera.
- **Compartir métricas**: Junto al punto anterior y al tercer punto de "Concienciación", las metricas son una manera
visible de ver el cambio, mientras que el cambio realmente se inyecta en de manera indivual en cada miembro del equipo,
las métricas son visibles para cualquier persona, hacer visible estos valores es la prueba gráfica del trabajo bien 
hecho.

### Transferir (*Transfer*)

- **Informar y transferir**: La información de todo el proceso y el cambio que se ha realizado debe ser transferido en
medida de lo posible a otros equipos que puedan beneficiarse de él.
