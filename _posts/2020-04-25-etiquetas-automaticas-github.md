---
layout: post
title: 'Etiquetas automáticas en Pull Request de Github'
date: '2020-04-25'
author: Miguel G. Flores
tags:
- ci/cd
- github
---

![Resultado Github Labeler]({{ site.url }}/assets/images/2020-04-25/resultado-github-action-labeler.png)


### Etiquetas
Las etiquetas en Github permiten ordenar y priorizar el trabajo tanto en issues como en pull request. De esta manera
podemos ver tanto en la pull request como en los listados facilmente, posibles propiedades, como nivel de urgencia, 
tipo de issue/pr, etc.

Las etiquetas no tienen sentido ninguno si el equipo no tiene un consenso de lo que significa cada una. Con una tabla
explicando cada valor con que descripción se corresponde.

El proceso de añadir etiquetas ha sido siempre manual en el momento de la creación de la issue/pr. Pero este proceso
puede ser automatizado, para que en base a diferentes patrones se añadan distintas etiquetas.

Por ejemplo, en un proyecto que se realicen migraciones a la base de datos, es muy común tener una etiqueta `migrations`, 
indinando al revisor de la pull request que tiene que ejecutar las migraciones para probar el resultado.


### Github Actions: Labeler
[La Action de Github Labeler](https://github.com/actions/labeler), nos permite conseguir justo esto con algunos patrones
ya definidos.

Para continuar con el ejemplo anterior, suponiendo que nuestro proyecto tiene distintas divisiones de contextos, dominios,
bundles o apps dentro del repositorio, y cada una de ellas puede tener migraciones, con esta estructura de directorios:

```
src/
   domain1/
      migrations/
         migration_file_1.php
         migration_file_2.php
      ...
   domain2/
      migrations/
         migration_file_1.php
      ...
```

La configuración que debemos añadir en el archivo `.github/labeler.yml` en la raiz del proyecto para identificar
estos archivos, sería:

```
migrations:
  - src/*/migrations/*
```

Con esta [expresión glob](https://mincong.io/2019/04/16/glob-expression-understanding/) buscamos en todos los directorios dentro de `src`, y en cada uno de ellos tienen que tener un
directorio en el siguiente nivel con `migrations`, y dentro de este directorio encontramos cualquier archivo.

Para leer y ejecutar este archivo, usaremos un workflow de github llamando al Action labeler, este archivo estará en
`.github/workflows/labeler.yml`

```
name: Labeler
on:
- pull_request

jobs:
  triage:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/labeler@v2
      with:
        repo-token: "${{ secrets.GITHUB_TOKEN }}"
```
