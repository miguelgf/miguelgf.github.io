---
layout: post
title: 'Cambiar nombre de las tablas en tiempo de ejecución con Symfony/Doctrine'
date: '2017-06-10'
author: Miguel G. Flores
tags:
- php
- symfony
- doctrine
---

Cuando se lidia con aplicaciones symfony complejas, aplicaciones que vienen heredadas y han sido
transformadas a symfony o tienen un propósito de multiservicio, entre otras razones, puede darse
el caso de que tengamos en la misma base datos distintas tablas que realicen la misma misión, pero
respondiendo a distinto nombre, a través de un prefijo o placeholder.

Cuando se realiza el mapeo de una entidad con la base de datos, se especifica el nombre de la tabla
en la base de datos. Si queremos que la misma entidad pueda ser mapeada a distintas tablas deberiamos
duplicar la entidad, cada una apuntando a su tabla en la base de datos. Esta sería una solución lógica 
si tuviesemos un número finito de tablas y tuviese sentido tener dos entidades distintas para 
relacionarlas. En otros casos en que la cantidad de tablas no está bien definida y limitada sino que 
puede variar según distintas variables externas como el entorno, sesión o petición request, 
puede tener solución igualmente.

Para los ejemplos vamos a suponer que tenemos la entidad `MyEntityWithDynamicTableName` que tiene como
nombre de tabla `table_placeholder_one`. En este caso queremos modificar la parte de `placeholder` por
otro valor según nos interese:

```yml
# Archivo: Acme/AppBundle/Resources/config/doctrine/MyEntityWithDynamicTableName.orm.yml
Acme\AppBundle\Entity\MyEntityWithDynamicTableName:
    type: entity
    table: table_placeholder_one
```

### Solución con listener (No en tiempo de ejecución)
Si la apliación es genérica para varios clientes/sistemas o se despliega en distintos entornos, la mejor
solución es modificar los metadatos de las relaciones en el momento que se generan. Estos metadatos se
cachean y quedan guardados. Para ello debemos crear un listener al evento `loadClassMetadata`:

```yml
# Archivo: app/config/services.yml
services:
    mapping.listener:
        class: AppBundle\EventListener\TableNameListener
        tags:
            - { name: doctrine.event_listener, event: loadClassMetadata }
```

En este evento debemos modificar el nombre de la tabla (también vale para esquemas, índices, mapping, ...).

```php
use Doctrine\ORM\Event\LoadClassMetadataEventArgs;

class TableNameListener
{
    public function loadClassMetadata(LoadClassMetadataEventArgs $eventArgs)
    {
        $classMetadata = $eventArgs->getClassMetadata();
        $classMetadata->setTableName(str_replace('placeholder', 'xxx', $classMetadata->getTableName()));
    }
}
```

Según como tengas el sistema, en el más común de los casos estos metadatos van a estar cacheados, por lo que esta
solución no sería válida en tiempo de ejecución.

### Solución en tiempo de ejecución

Visto que la solución anterior no vale en muchas ocasiones, debemos realizar este reemplazo de nombre en tiempo de ejecución y no cuando
se crea la caché. Esta misma solución anterior puede usarse sin usar el listener de `loadClassMetadata` y aplicar
el cambio directamente antes de usar la entidad/repositorio:

```php
public function indexAction()
{
    $entityManager = $this->getDoctrine()->getManager();
    $classMetadata = $entityManager->getClassMetadata('AppBundle:MyEntityWithDynamicTableName');
    $classMetadata->setTableName(str_replace('placeholder', 'xxx', $classMetadata->getTableName()));

    $repository = $entityManager->getRepository('AppBundle:MyEntityWithDynamicTableName'):
    
    $all = $repository->findAll();
}
```

Con esta solución la consulta SQL generada sería:

`SELECT * FROM table_xxx_one`

Aún así usar este extracto de código en todas la partes donde quiera modificarse el nombre de la tabla
no es una buena práctica, la lógica va a estar repetida en todos los controladores y es mejor centralizarlo
en un solo punto.

### Solución con factoría + método con parámetros
Una posible implentación para mejorar el código anterior sería incluir la lógica de obtener el repositorio con
los nuevos metadatos en una factoría abstracta, que devuelva el repositorio para la entidad deseada con el
cambio de metadatos previamente establecido. De esta manera encapsulamos la lógica del cambio de nombre de tabla en
un solo lugar. 

```php
# Archivo: Acme\AppBundle\Doctrine\GameRepositoryFactory.php
class DynamicTableRepositoryFactory
{
    /**
     * @var EntityManagerInterface
     */
    private $entityManager;

    public function __construct(EntityManagerInterface $em)
    {
        $this->entityManager = $em;
    }

    public function getRepository($entity, $replacement)
    {
        $classMetadata = $this->entityManager->getClassMetadata($entity);
        $classMetadata->setTableName(str_replace('{placeholder}', $replacement, $classMetadata->getTableName()));

        return $this->entityManager->getRepository($entity);
    }
}

```

La definición del servicio sería la siguiente:

```
# Archivo: Acme\AppBundle\Resources\config\services.yml
services:
    acme.dynamic_table.repository.factory:
        class: Acme\AppBundle\Doctrine\DynamicTableRepositoryFactory
        arguments:
          - "@doctrine.orm.entity_manager"
```

De esta manera, el controlador anterior quedaría mucho más limpio, teniendo una invocación de este tipo:

```php
public function indexAction()
{
    $repository = $this->get('acme.dynamic_table.repository.factory')
                        ->getRepository('AppBundle:MyEntityWithDynamicTableName', 'xxx');
    
    $all = $repository->findAll();
}
```

Esta solución es bastante válida si el reemplazo del placeholder (`xxx` en este caso) varía en la aplicación 
constantemente y puede esr distinto según el sitio en el que estemos. Si esto no es así, podemos pasar la siguiente solución.

### Solución con servicio e inyección de datos

En caso de que el cambio al que correspondan todas las tablas sea el mismo (`xxx` en los ejemplos anteriores) se podría
hacer que este parámetro se inyecte directamente en nuestro servicio de obtención del repositorio o en la factoría 
anterior. De esta manera podriamos obtener directamente el servicio con 
`$this->get('app.my_entity_with_dynamic_table_name.repository')` y que ya tuviese el placeholder cambiado con la nueva 
tabla o usar la factoría anterior pero sin pasar el segundo parámetro a `getRepository`.

Este caso puede darse en cuanto la variable viene por entorno, sesión o está definida por ejemplo
en el `parameters.yml`.

Siguiendo el ejemplo de la factoría pero inyectando los nuevos parámetros, nuestra factoría pasaría a tomar el reemplazo
en su constructor:

```php
# Archivo: Acme\AppBundle\Doctrine\GameRepositoryFactory.php
class DynamicTableRepositoryFactory
{
    /**
     * @var EntityManagerInterface
     */
    private $entityManager;

    /**
     * @var string
     */
    private $remplacement;
    
    public function __construct(EntityManagerInterface $em, $replacement)
    {
        $this->entityManager = $em;
        $this->replacement = $replacement;
    }

    public function getRepository($entity)
    {
        $classMetadata = $this->entityManager->getClassMetadata($entity);
        $classMetadata->setTableName(str_replace('{placeholder}', $this->replacement, $classMetadata->getTableName()));

        return $this->entityManager->getRepository($entity);
    }
}
```

Y la definición del servicio tomaría como entrada el valor de reemplazo, en este ejemplo pasandole un valor definido en 
el `parameters.yml`

```yml
# Archivo: app/config/parameters.yml
parameters:
    app.replacement_table: xxx
```

```
# Archivo: Acme\AppBundle\Resources\config\services.yml
services:
    acme.dynamic_table.repository.factory:
        class: Acme\AppBundle\Doctrine\GameRepositoryFactory
        arguments:
          - "@doctrine.orm.entity_manager"
          - "%app.replacement_table%"
```

Otros ejemplos de reemplazo sería injectar la sesión (`@session`) para obtener un parámetro concreto o
la petición request (`@request_stack`) entre otros.
