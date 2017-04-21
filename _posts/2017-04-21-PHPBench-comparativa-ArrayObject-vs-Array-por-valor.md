---
layout: post
title: 'PHPBench: Comparativa ArrayObject vs Array por valor en PHP'
date: '2017-04-21'
author: Miguel González
tags:
- php
- phpbench
---

Por curiosidad, hice un pequeño test usando [PHPBench]() para comparar el uso de un ArrayObject cuando debe ser modificado por otra función
contra pasar el array original por valor, modificarlo y devolverlo. Aquí están los resultados:

## Código PHP del test:
```
<?php

/**
 * @Revs(1000)
 */
class ArrayObjectvsArrayByValueBench
{
	private function getArray() {
		$array = [];

		for ($i = 0; $i < 1000; $i++) {
			$array[] = $i;
		}

		return $array;
	}

    public function benchArrayObject()
    {
        $arrayObject = new \ArrayObject($this->getArray());

        for ($i = 0; $i < 1000; $i++) {
        	$this->transformArrayObject($arrayObject);
        }
    }

    private function transformArrayObject(\ArrayObject $arrayObject) {
    	$arrayObject[] = 7;
    }


    public function benchArray()
    {
        $array = $this->getArray();

        for ($i = 0; $i < 1000; $i++) {
        	$array = $this->transformArray($array);
        }
    }

    private function transformArray(array $array): array {
    	$array[] = 7;

    	return $array;
    }
}
```

## Resultados:
```
➜  phpbench phpbench run ArrayObjectVsArrayByValueBench.php --report=default
PhpBench 0.14-dev (@git_version@). Running benchmarks.

\ArrayObjectvsArrayByValueBench

    benchArrayObject              I0 P0         [μ Mo]/r: 1,330.161 1,330.161 (μs)      [μSD μRSD]/r: 0.000μs 0.00%
    benchArray                    I0 P0         [μ Mo]/r: 5,155.070 5,155.070 (μs)      [μSD μRSD]/r: 0.000μs 0.00%

2 subjects, 2 iterations, 2,000 revs, 0 rejects
(best [mean mode] worst) = 1,330.161 [3,242.616 3,242.616] 1,330.161 (μs)
⅀T: 6,485.231μs μSD/r 0.000μs μRSD/r: 0.000%
suite: 133c6b56d794387d152b5cff4ef89e8a1af7a086, date: 2017-04-21, stime: 16:27:27
+--------------------------------+------------------+--------+--------+------+------+----------+-------------+--------------+----------------+
| benchmark                      | subject          | groups | params | revs | iter | mem_peak | time_rev    | comp_z_value | comp_deviation |
+--------------------------------+------------------+--------+--------+------+------+----------+-------------+--------------+----------------+
| ArrayObjectvsArrayByValueBench | benchArrayObject |        | []     | 1000 | 0    | 464,576b | 1,330.161μs | 0.00σ        | 0.00%          |
| ArrayObjectvsArrayByValueBench | benchArray       |        | []     | 1000 | 0    | 529,976b | 5,155.070μs | 0.00σ        | 0.00%          |
+--------------------------------+------------------+--------+--------+------+------+----------+-------------+--------------+----------------+
```

Podemos ver como ArrayObject es más rápido como cabía esperar, pues los objetos se pasan siempre por referencia mientras que el array se pasa
por valor. Otra comparativa interesante podría ser pasar el mismo array por referencia también y ver la micro optimización.