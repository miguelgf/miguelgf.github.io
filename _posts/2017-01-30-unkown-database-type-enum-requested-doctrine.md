---
layout: post
title: 'Error "Unknown database type enum requested" con doctrine import'
date: '2017-01-30'
author: Miguel G. Flores
tags:
- php
- doctrine
- mysql
---

Recientemente en el proceso de refactorización y mudanza de un proyecto legacy a una estructura basada en symfony,
una de las tareas era crear todas las entidades con los mapeos desde una base de datos ya en el servidor. Para éste
caso concreto me vino genial el comando de Doctrine para leer una base de datos y de manera automática generar éstas
entidades (o el mapeo XML de Doctrine).

El comando se usa de la siguiente forma (Reemplazar `app` por `bin` si estás en Symfony >= 3):

```
php app/console doctrine:mapping:import --force NewAppBundle annotation
```

Éste comando va a recorrer todo el esquema de base de datos del EntityManager principal configurado en symfony (para
usar un EntityManager diferente especificar el argumento `--em` en la llamada al comando) y crear los archivos PHP
de las entidades dentro del bundle NewAppBundle.

Si la base de datos es simple con ésto debería de ser suficiente, sin embargo, en la base de datos que yo trabajaba
tenía un tipo de dato [`ENUM` de MySQL](https://dev.mysql.com/doc/refman/5.7/en/enum.html) que actualmente no está 
soportado por Doctrine, por lo que vamos a obtener un error tal que así:

```
[Doctrine\DBAL\DBALException]                                                                    
Unknown database type enum requested, Doctrine\DBAL\Platforms\MySqlPlatform may not support it.  
```

Las alternativas para usar Enum están descritas en éste artículo: 

[MySQL Enums with Doctrine](http://docs.doctrine-project.org/projects/doctrine-orm/en/latest/cookbook/mysql-enums.html)

La manera de aplicar ésto que he tomado es un poco "sucia", pero dado que éste proceso sólo va a ser ejecutado una vez
en la vida fue modificar directamente el comando `doctrine:mapping:import` para convertir los tipo ENUM a String.

La modificación esta se realiza en el archivo `vendor/doctrine/doctrine-bundle/Command/ImportMappingDoctrineCommand.php`,
justo debajo de donde obtenemos el EntityManager responsable de la conexión, vamos a obtener la conexión y registrar
la conversión del tipo de datos.

```
// Ésta línea está en el comando
$em = $this->getEntityManager($input->getOption('em'), $input->getOption('shard'));

// Debajo añadimos lo siguiente:
$conn = $em->getConnection();
$conn->getDatabasePlatform()->registerDoctrineTypeMapping('enum', 'string');

```

Con esta modificación ya nos libraremos de éste problema y podremos obtener nuestros archivos de entidades facilmente.
