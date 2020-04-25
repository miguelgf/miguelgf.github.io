---
layout: post
title: 'Usar Spot Instances de AWS en Jenkins'
date: '2020-04-19'
author: Miguel G. Flores
tags:
- devops
- ci/cd
- jenkins
- aws
---

![Usar Spot Instances de AWS en Jenkins]({{ site.url }}/assets/images/2020-04-19/spot-instances-aws.png)

En un proceso de integración continua y despliegue continuo, tenemos que tener una capacidad de procesamiento para hacer frente a todas las builds de todos los proyectos. Herramientas como CircleCI, Codeship o TravisCI te cobran directamente por estos minutos de uso. Pero si estás usando algún servicio self-hosted, el procesamiento es lo que te cueste tener la máquina levantada, ya sea como una instancia en AWS, servidor dedicado o cualquier otro tipo. Esta infraestructura hay que mantenerla y pagar por ella.

Existen distintas maneras de optimizar este coste, centrándonos en Amazon Web Services, podemos reservar instancias durante un año, tres años o cinco años con un precio especial. O podemos usar Jenkins para levantar instancias bajo demanda para correr las ejecuciones y luego apagar las máquinas, pagando por la tarificación de horas o minutos que tengas (pero no por 24horas al día de la máquina funcionando)

En este caso vamos a ver como configurar Jenkins para levantar instancias bajo de manda y además utilizar Spot Instances para ahorrar costes.

# Spot Instances vs On-demand instances
La diferencia entre las instancias bajo demanda y las Spot instances es su precio. En comportamiento son iguales, instancias que se levantan en base a una AMI y se pueden integrar con otros servicios (EMR, ECS, CloudFormation, ...) igualmente. Estas instancias las provee AWS para cubrir la capacidad de EC2 que tienen en desuso. El precio puede ser hasta de un 90% más barato en el mejor de los casos. De manera habitual va a ser entre un 60/70% más barata.

Por ejemplo, hoy día 19 de Abril de 2020, el precio por hora de una instancia m5.large EC2 bajo demanda es 0.1070$ por hora, mientras que esa misma instancia en Spot Instances, es de 0.0036$ por hora.

![Ejemplo de descuento Spot Instance vs On-demand]({{ site.url }}/assets/images/2020-04-19/ejemplo-precio-spot-instance.png)

# Configurar Jenkins para que use spot instances
### Configuración en Jenkins
En Jenkins, esta configuración se realiza en los Nodos y Clouds, en el menú Manage Jenkins > Manage Nodes and Clouds > Configure Clouds. En el apartado AMIs, donde configuremos los tipos de máquinas que queremos levantar, habrá una opción "Use Spot Instance" para poder marcar. Otro checkbox aparecerá de "Fallback to on-demand instances" para usar una instancia bajo de manda común si no hay spot disponibles. Si el proceso que se debe ejecutar no puede esperar, es aconsejable marcarla.

### Permisos IAM
Para que Jenkins sea capaz de levantar instancias de este tipo hay que concederle los permisos necesarios. En el rol o usuario IAM que se use como credentials de EC2, hay que especificar en su política los permisos sobre spot instances:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "miguelg-com-stmt123",
            "Action": [
                "ec2:DescribeSpotInstanceRequests",
                "ec2:CancelSpotInstanceRequests",
                "ec2:RequestSpotInstances",
                "ec2:RunInstances",
                "ec2:StartInstances",
                "ec2:StopInstances",
                "ec2:TerminateInstances",
                "ec2:CreateTags",
                "ec2:DeleteTags",
                "ec2:DescribeInstances",
                "ec2:DescribeKeyPairs",
                "ec2:DescribeRegions",
                "ec2:DescribeImages",
                "ec2:DescribeAvailabilityZones",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeSubnets",
            ],
            "Effect": "Allow",
            "Resource": "*"
        }
    ]
}
```

### Precios máximos
En el propio menú de Jenkins se pueden especificar el precio máximo que queremos pagar.

Tenemos opción de obtenerlos de AWS con un botón (para esto se requieres permisos adicionales, debeis añadir a la política anterior `ec2:DescribeSpotPriceHistory`)

También podemos consultar estos precios en la consola de AWS, bajo EC2 > Spot Requests > Pricing History, para ver la evolución de precio de hasta los últimos 3 meses
