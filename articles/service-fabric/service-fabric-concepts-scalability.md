---
title: Escalabilidad de los servicios de Service Fabric | Microsoft Docs
description: "Describe cómo escalar servicios de Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/30/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 52dcf341a34478bf4e800d8f1b9d44867b5feaf6


---
# <a name="scaling-service-fabric-applications"></a>Escalación de aplicaciones de Service Fabric
Azure Service Fabric facilita la creación de aplicaciones escalables, al administrar los servicios, particiones y réplicas en todos los nodos de un clúster. Esto habilita la utilización máxima de recursos.

La escala alta para aplicaciones de Service Fabric se puede lograr de dos maneras:

1. Ajuste de escala en el nivel de partición de servicio
2. Ajuste de escala en el nivel de instancia de servicio con nombre

## <a name="scaling-at-the-partition-level"></a>Ajuste de escala en el nivel de partición
Service Fabric es compatible con la creación de particiones. La creación de particiones permite dividir un servicio individual en varias particiones independientes, cada una de las cuales con una parte del estado general del servicio. En la [Información general de la creación de particiones](service-fabric-concepts-partitioning.md) se ofrece información sobre los tipos de esquemas de particiones que se admiten. Las réplicas de cada partición se extienden entre los nodos en un clúster. Considere la posibilidad de que un servicio que usa un esquema de particiones de intervalo con una clave baja de 0, una clave alta de 99 y un recuento de 4 particiones. En un clúster de&3; nodos, el servicio podría estar dispuesto con cuatro réplicas que comparten los recursos en cada nodo, como se muestra aquí.

<center>
![Diseño de partición con tres nodos](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Si aumenta el número de nodos, Service Fabric usará los recursos en los nodos nuevos moviendo aquí algunas de las réplicas existentes. Si se aumenta el número de nodos a cuatro, el servicio tendrá ahora tres réplicas ejecutándose en cada nodo (cada una perteneciente a particiones diferentes), lo que permite un mejor rendimiento y uso de recursos.

<center>
![Diseño de partición con cuatro nodos](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-at-the-service-name-level"></a>Ajuste de escala en el nivel de nombre de servicio
Una instancia de servicio es una instancia específica de un nombre de aplicación y un nombre de tipo de servicio (consulte el [Ciclo de vida de aplicaciones de Service Fabric](service-fabric-application-lifecycle.md)). Durante la creación del servicio, especifique el esquema de partición (consulte [Creación de particiones de los servicios de Service Fabric](service-fabric-concepts-partitioning.md)) que se va a usar.

El primer nivel de ajuste de escala es por nombre de servicio. Puede crear instancias de un servicio, opcionalmente con niveles diferentes de particiones, a medida que sus instancias de servicio anteriores pasen a estar ocupadas. Esto permite que los consumidores del nuevo servicio usen las instancias de servicio menos ocupadas en lugar de las más ocupadas.

Una opción para aumentar la capacidad es crear una nueva instancia de servicio con un nuevo esquema de particiones. Sin embargo, esto agrega complejidad. Los clientes de consumo necesitan saber cuándo y cómo usar un servicio con un nombre diferente. Como otra alternativa, un servicio de administración o intermediario debería tomar una determinación sobre qué servicio y partición debería controlar cada solicitud.

### <a name="example-scenario-embedded-dates"></a>Escenario de ejemplo: fechas incrustadas
Un escenario posible sería usar la información de fecha como parte del nombre del servicio. Por ejemplo, podría usar la instancia de servicio con un nombre específico para todos los clientes que se incorporaron en 2013 y otro nombre para los clientes que se incorporaron en 2014. Este esquema de nombres permite el aumento mediante programación en los nombres según la fecha (conforme se aproxima el 2014, la instancia de servicio para el 2014 puede crearse a petición).

Sin embargo, este enfoque se basa en los clientes que usan la información de nombres específica de la aplicación que está fuera del ámbito de los conocimientos de Service Fabric.

* *Uso de una convención de nomenclatura*: en 2013, cuando la aplicación se activa, se crea un servicio denominado fabric:/app/service2013. Próximo al segundo trimestre de 2013 crea otro servicio denominado fabric:/app/service2014. Ambos servicios son del mismo tipo. En este enfoque, su cliente tendrá que tener una lógica para crear el nombre de servicio adecuado según el año.
* *Uso de un servicio de búsqueda*: otro patrón es proporcionar un servicio de búsqueda secundaria, que puede proporcionar el nombre del servicio para una clave que desee. El servicio de búsqueda puede crear a continuación nuevas instancias de servicio. El propio servicio de búsqueda no conserva los datos de la aplicación, sino únicamente los datos de los nombres de servicio que crea. Así pues, en el ejemplo anterior basado en el año, el cliente se pondría en contacto primero con el servicio de búsqueda para averiguar el nombre del servicio que administra datos para un año determinado. Después, el cliente usaría ese nombre de servicio para realizar la operación real. El resultado de la primera búsqueda se puede almacenar en caché.

## <a name="putting-it-all-together"></a>Resumen
Tomemos todas las ideas que analizamos aquí y hablemos sobre otro escenario.

Considere el ejemplo siguiente: intenta crear un servicio que actúe como libreta de direcciones con nombres e información de contacto. ¿Cuántos usuarios tendrá? ¿Cuántos contactos almacenará cada usuario? Tratar de saber esta información cuando está configurado el servicio por primera vez es muy difícil. Las consecuencias de elegir un recuento de particiones incorrecto podrían significar que tenga que escalar problemas más adelante. Pero ¿por qué debería intentar elegir un esquema de una partición para todos los usuarios?

En este tipo de situaciones, considere mejor el siguiente patrón:
1. En lugar de intentar elegir un esquema de partición para todos por adelantado, cree un "servicio de administrador".
2. El trabajo del servicio de administrador es observar la información del cliente cuando se registra en el servicio. Luego, en función de esa información, crear una instancia del servicio de almacenamiento de contactos _real_ _solo para ese cliente_. Este tipo de patrón de creación de servicio dinámico presenta muchas ventajas:

    * No intenta adivinar el recuento correcto de particiones para todos los usuarios por adelantado
    * La segmentación de datos, debido a que cada cliente tiene su propia copia del servicio
    * El servicio de cada cliente se puede configurar de forma distinta, con más o menos particiones o réplicas según sea necesario en función de la escala esperada.
      * Por ejemplo, digamos que el cliente pagó por el nivel "Gold": podría obtener más réplicas o un mayor recuento de particiones.
      * O supongamos que proporcionó información que indicaba que el número de contactos que necesitaba era "pequeño". En este caso, solo recibiría unas pocas particiones.
    * No está ejecutando un grupo de instancias de servicio o réplicas mientras espera que aparezcan los clientes
    * Si se va algún cliente, quitar su información del servicio es tan simple como que el administrador elimine el servicio que se creó

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre los conceptos de Service Fabric, consulte los siguientes artículos:

* [Disponibilidad de los servicios de Service Fabric](service-fabric-availability-services.md)
* [Creación de particiones de los servicios de Service Fabric](service-fabric-concepts-partitioning.md)
* [Definición y administración del estado](service-fabric-concepts-state.md)



<!--HONumber=Jan17_HO1-->


