---
layout: post
title: Añadir cambios al commit anterior en git
date: '2016-09-30'
author: Miguel González
tags:
- git
---

En muchísimas ocasiones, trabajando con git, ocurre que finalizas un cambio y sigues el flujo de agregar esoscambios a un commit.

```
git add .
git commit -m "Mensaje de mi commit"
```

Después al continuar trabajando te encuentras con que has dejado algo que no está del todo bien, que uno de tus test no pasa o que cambias algo muy simple como la identación y organización del código para respetar el [PSR-2](http://www.php-fig.org/psr/psr-2/). En estos casos algunos crean un nuevo commit (que no aporta nada al proyecto) con un nuevo mensaje, otros hacen otro commit con el mismo nombre que el anterior, y la mejor solución en estos casos es añadir los nuevos cambios al commit anterior.

El comando para realizar esto es:

```
git commit --amend
```

De esta manera los cambios se añaden al commit anterior. Te preguntará el nombre del commit por si quieres modificarlo.

