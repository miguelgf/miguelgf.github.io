---
layout: post
title: 'Migración BaseTestListener de PHPUnit6 a PHPUnit7'
date: '2018-10-05'
author: Miguel G. Flores
tags:
- php
- testing
---

En el la actualización de PHPUnit 6 a PHPUnit 7, podemos encontrar que la clase `BaseTestListener`
[ha sido eliminada](https://github.com/sebastianbergmann/phpunit/blob/master/ChangeLog-7.0.md#removed) (ya
antes fue deprecada en PHPUnit 6.4) y si lo estamos usando nuestra suite de test va a fallar.

La manera de actualizar nuestros test para dejar de usar el `BaseTestListener` es comenzar a usar la interfaz
TestListener e implementar los métodos que necesitemos.

Al estar implementando una interfaz en lugar de extender una clase, necesitamos implementar todos los métodos definidos
en la interfaz, para completar estas implementaciones con el comportamiento estandar, podemos hacer el uso de un trait
llamado `TestListenerDefaultImplementation`.

La conversión de código sería:

### Antes
```php
<?php

namespace Test\Listener;

use PHPUnit\Framework\BaseTestListener;

class IntegrationTestsFixturesLoaderListener extends BaseTestListener
{
    public function startTest(Test $test): void
    {
        ...
    }
}
```

### Después
```php
<?php

namespace Test\Listener;

use PHPUnit\Framework\TestListener;

class IntegrationTestsFixturesLoaderListener implements TestListener
{
    use TestListenerDefaultImplementation;

    public function startTest(Test $test): void
    {
        ...
    }
}
```
