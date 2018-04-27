---
layout: post
title: 'Configurar Name Servers al migrar un dominio a Amazon con Route 53'
date: '2017-03-11'
author: Miguel G. Flores
tags:
- amazon
- aws
---

Recientemente migré el dominio de éste blog, miguelg.com, a Amazon con Route 53. Cuando migras un dominio
que ya existe, Amazon te trae los `Name servers` que tuviese el proveedor de DNS anteriormente. Ésto hará
que tengas el dominio inactivo por unas horas o días hasta que los modifiques por los adecuados.

La documentación de como encontrar los Name Servers adecuados cuando es un dominio recién migrado
y no uno registrado desde cero con Amazon es muy escasa.

Los `Name Server` que deben especificar dentro de la configuración del dominio se encuentran dentro
de la "Hosted Zone" del dominio.

![Localización nombres de servidor DNS para un dominio en Route 53]({{ site.url }}/assets/images/2017-03-11/route53_nameservers.png)

Esos cuatro nombres de servidor DNS son los que se deben configurar en la configuración de los dominios
registrados
