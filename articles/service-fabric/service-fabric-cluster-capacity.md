<properties
   pageTitle="Planeación de capacidad del clúster de Service Fabric | Microsoft Azure"
   description="Consideraciones de planeación de capacidad del clúster de Service Fabric Tipos de nodos, durabilidad y niveles de confiabilidad"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# Consideraciones de planeación de capacidad del clúster de Service Fabric

En cualquier implementación de producción, la planeación de capacidad es un paso importante. Estos son algunos de los elementos que se deben tener en cuenta como parte de ese proceso.

- Número de tipos de nodos con los que el clúster tiene que empezar
- Propiedades de cada tipo de nodo (tamaño, principal, accesible desde Internet, número de máquinas virtuales, etc.)
- Características de confiabilidad y durabilidad del clúster

Repasemos brevemente cada uno de estos elementos.

## Número de tipos de nodos con los que el clúster tiene que empezar

En primer lugar, tiene que averiguar para qué se va a usar el clúster que está creando y qué tipos de aplicaciones piensa implementar en este clúster. Si no tiene clara la finalidad del clúster, es muy probable que todavía no está listo para especificar el proceso de planeación de capacidad.

Establezca el número de tipos de nodos con los que el clúster tiene que empezar. Cada tipo de nodo se asigna a un conjunto de escalado de máquina virtual. Cada tipo de nodo se puede escalar o reducir verticalmente de forma independiente. Cada uno tiene diferentes conjuntos de puertos abiertos y puede tener distintas métricas de capacidad. Por lo que la decisión del número de tipos de nodo depende esencialmente de las consideraciones siguientes:

- ¿Tiene la aplicación varios servicios y alguno de ellos ha de ser público o accesible desde Internet? Las aplicaciones típicas contienen un servicio front-end de puerta de enlace que recibe entrada de un cliente, así como uno o varios servicios back-end que se comunican con los servicios front-end. Por lo que en este caso, acaba teniendo al menos dos tipos de nodo.

- ¿Tienen los servicios (que componen la aplicación) distintos requisitos de infraestructura, como, por ejemplo, una RAM mayor o un número más alto de ciclos de CPU? Por ejemplo, supongamos que la aplicación que desea implementar contiene un servicio front-end y un servicio back-end. El servicio front-end se puede ejecutar en máquinas virtuales más pequeñas (tamaños de VM como D2) que tienen puertos abiertos a Internet. Pero el servicio back-end es de cálculo intensivo y tiene que ejecutarse en máquinas virtuales más grandes (con tamaños de VM como D4, D6 o D15) que no son accesibles desde Internet.

 En este ejemplo, aunque puede optar por colocar todos los servicios en un tipo de nodo, se recomienda colocarlos en un clúster con dos tipos de nodos. Esto permite que cada tipo de nodo pueda tener distintas propiedades, como por ejemplo, conectividad a Internet o tamaño de la máquina virtual. El número de máquinas virtuales también se puede escalar de forma independiente.

- Puesto que no puede predecir el futuro, parta de hechos que conozca y decida el número de tipos de nodo que con el que deben empezar las aplicaciones. Siempre puede agregar o quitar tipos de nodos más adelante. Un clúster de Service Fabric debe tener como mínimo un tipo de nodo.

## Las propiedades de cada tipo de nodo.

El **tipo de nodo** puede considerarse similar a los roles de Servicios en la nube. Los tipos de nodos definen los tamaños de máquina virtual, el número de máquinas virtuales y sus propiedades. Cada tipo de nodo que se define en un clúster de Service Fabric está configurado como un conjunto de escalado de máquina virtual independiente. Los conjuntos de escalado de máquina virtual son un recurso de proceso de Azure que se pueden usar para implementar y administrar una colección de máquinas virtuales de forma conjunta. Al definirse como diferentes conjuntos de escalado de máquina virtual, cada tipo de nodo se puede escalar o reducir verticalmente de forma independiente, tiene diferentes conjuntos de puertos abiertos y puede tener distintas métricas de capacidad.

El clúster puede tener más de un tipo de nodo, pero el tipo de nodo principal (el primero que se define en el portal) debe tener al menos cinco máquinas virtuales para los clústeres que se usan en cargas de trabajo de producción (o al menos tres máquinas virtuales en clústeres de prueba). Si va a crear el clúster con una plantilla de Resource Manager, encontrará un atributo **Es principal** en la definición de tipo de nodo. El tipo de nodo principal es el tipo de nodo en que se colocan los servicios del sistema de Service Fabric.

### Tipo de nodo principal
En el caso de un clúster con varios tipos de nodo, tendrá elegir uno de ellos como principal. Estas son las características de un tipo de nodo principal:

- El tamaño mínimo de las máquinas virtuales del tipo de nodo principal se determina mediante el nivel de durabilidad que se elija. El valor predeterminado del nivel de durabilidad es Bronze. Desplácese hacia abajo para más información sobre el nivel de durabilidad y los valores que puede adoptar.

- El número mínimo de máquinas virtuales del tipo de nodo principal se determina mediante el nivel de confiabilidad que se elija. El valor predeterminado del nivel de confiabilidad es Silver. Desplácese hacia abajo para más información sobre el nivel de confiabilidad y los valores que puede adoptar.

- Los servicios del sistema de Service Fabric (por ejemplo, el servicio Administrador de clústeres o el servicio de almacén de imágenes) se colocan en el tipo de nodo principal y así la confiabilidad y la durabilidad del clúster se determinan mediante el valor de nivel de confiabilidad y el valor de nivel de durabilidad que se seleccionen para el tipo de nodo principal.

![Captura de pantalla de un clúster que tiene dos tipos de nodos][SystemServices]


### Tipo de nodo no principal
Para un clúster con varios tipos de nodos, hay un tipo de nodo principal y los demás son de tipo no principal. Estas son las características de un tipo de nodo no principal:

- El tamaño mínimo de las máquinas virtuales para este tipo de nodo se determina mediante el nivel de durabilidad que se elija. El valor predeterminado del nivel de durabilidad es Bronze. Desplácese hacia abajo para más información sobre el nivel de durabilidad y los valores que puede adoptar.

- El número mínimo de máquinas virtuales para este tipo de nodo puede ser uno. Pero este número se debe elegir en función del número de réplicas de la aplicación o de servicios que desea ejecutar en este tipo de nodo. Después de implementar el clúster, se puede aumentar el número de máquinas virtuales de un tipo de nodo.


## Características de durabilidad del clúster

El nivel de durabilidad se usa para indicar al sistema los privilegios que tienen las máquinas virtuales con la infraestructura subyacente de Azure. En el tipo de nodo principal, este privilegio permite que Service Fabric pause cualquier solicitud de la infraestructura de nivel de máquina virtual (por ejemplo, reinicio de máquina virtual, restablecimiento de la imagen inicial de máquina virtual o migración de máquina virtual) que afecte a los requisitos de quórum de los servicios del sistema y los servicios con estado. En el tipo de nodo no principal, este privilegio permite que Service Fabric pause cualquier solicitud de la infraestructura de nivel de máquina virtual (por ejemplo, reinicio de máquina virtual, restablecimiento de la imagen inicial de máquina virtual o migración de máquina virtual) que afecte a los requisitos de quórum de los servicios con estado que se ejecuten en él.

Este privilegio se expresa con los siguientes valores:

- Gold: los trabajos de infraestructura se pueden pausar durante 2 horas por cada UD.

- Silver: los trabajos de infraestructura se puede pausar durante 30 minutos por UD (este valor no se puede usar actualmente. Una vez habilitado, estará disponible en todas las máquinas virtuales estándar de un solo núcleo y superiores).

- Bronze: sin privilegios. Este es el valor predeterminado.

## Características de confiabilidad del clúster

El nivel de confiabilidad se usa para establecer el número de réplicas de los servicios del sistema que se desea ejecutar en el tipo de nodo principal de este clúster. Cuanto mayor sea el número de réplicas, más confiables son los servicios del sistema en el clúster.

El nivel de confiabilidad puede adoptar los siguientes valores.

- Platinum: los servicios del sistema se ejecutan con un recuento de conjunto de réplicas de 9

- Gold: los servicios del sistema se ejecutan con un recuento de conjunto de réplicas de 7

- Silver: los servicios del sistema se ejecutan con un recuento de conjunto de réplicas de 5

- Bronze: los servicios del sistema se ejecutan con un recuento de conjunto de réplicas de 3

>[AZURE.NOTE] El nivel de confiabilidad que elija determina el número mínimo de nodos que debe tener el tipo de nodo principal. El nivel de confiabilidad no incide en el tamaño máximo del clúster. Por lo que puede tener un clúster de 20 nodos, que se ejecuta con una confiabilidad Bronze.

 Siempre puede actualizar la confiabilidad del clúster de un nivel a otro. Esto desencadenará las actualizaciones de clúster necesarias para cambiar el recuento de conjunto de réplicas de los servicios del sistema. Espere a que finalice la actualización en curso antes de realizar otros cambios en el clúster, como agregar nodos etc. Puede supervisar el progreso de la actualización en Service Fabric Explorer o mediante la ejecución de [Get ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt126012.aspx).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

Después de finalizar la planeación de capacidad y configurar un clúster, lea lo siguiente:
- [Seguridad de los clústeres de Service Fabric](service-fabric-cluster-security.md)
- [Introducción al modelo de mantenimiento de Service Fabric](service-fabric-health-introduction.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png

<!---HONumber=AcomDC_0921_2016-->