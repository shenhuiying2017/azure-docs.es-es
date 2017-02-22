---
title: "Recuperación ante desastres de Service Fabric de Azure | Microsoft Docs"
description: "Service Fabric de Azure ofrece las funciones necesarias para tratar con todo tipo de desastre. En este artículo se describen los tipos de desastres que pueden surgir y cómo resolverlos."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/29/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 6dc2a6dbf4b26363f1ad714baec8d48045aa97b6
ms.openlocfilehash: 81d818afb1a15db646a20b4001493d9df7e24d27


---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Recuperación ante desastres en Service Fabric de Azure
Una parte fundamental de la entrega de una aplicación de nube de alta disponibilidad consiste en garantizar su supervivencia a los distintos tipos de errores, incluidos los que están fuera de su control. En este artículo se describe el diseño físico de un clúster de Service Fabric de Azure en el contexto de posibles desastres y se proporciona orientación sobre cómo tratar tales desastres para limitar o eliminar el riesgo de pérdida de datos o tiempo de inactividad.

## <a name="physical-layout-of-service-fabric-clusters-in-azure"></a>Diseño físico de clústeres de Service Fabric de Azure
Para comprender el riesgo que presentan los diferentes tipos de errores, es útil saber cómo los clústeres están dispuestos físicamente en Azure.

Al crear un clúster de Service Fabric en Azure, se le pide que elija una región donde se hospedará. La infraestructura de Azure aprovisiona luego los recursos de ese clúster dentro de la región, en particular el número de máquinas virtuales (VM) solicitadas. Echemos un vistazo más detenidamente a cómo y dónde se aprovisionan esas máquinas virtuales.

### <a name="fault-domains"></a>Dominios de error
De forma predeterminada, las máquinas virtuales del clúster se distribuyen uniformemente entre grupos lógicos conocidos como "dominios de error" (FD), que segmentan las máquinas en función de los posibles errores en el hardware del host. En concreto, si dos máquinas virtuales residen en dos FD distintos, puede estar seguro de que no compartirán la misma fuente de alimentación o el mismo conmutador de red. Como resultado, un error de alimentación o de la red local que afecte a una de las máquinas virtuales no afectará a la otra, lo que permitirá a Service Fabric restablecer el equilibrio de la carga de trabajo de la máquina que no responde dentro del clúster.

Puede visualizar el diseño del clúster en los dominios de error mediante el mapa de clúster que se proporciona en el [Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md):

![Distribución de nodos entre dominios de error en el Explorador de Service Fabric][sfx-cluster-map]

> [!NOTE]
> El otro eje del mapa de clúster muestra los dominios de actualización, que agrupen nodos de forma lógica según actividades de mantenimiento planeadas. Los clústeres de Service Fabric en Azure siempre se distribuyen entre cinco dominios de actualización.
> 
> 

### <a name="geographic-distribution"></a>Distribución geográfica
Actualmente, [hay 30 regiones de Azure en todo el mundo][azure-regions], y se han anunciado varias más. Una región individual puede contener uno o varios centros de datos lógicos según la demanda y la disponibilidad de las ubicaciones adecuadas, entre otros factores. Sin embargo, tenga en cuenta que incluso en regiones que contienen varios centros de datos físicos, no existe garantía de que las máquinas virtuales del clúster se vayan a distribuir uniformemente entre esas ubicaciones físicas. De hecho, actualmente, todas las máquinas virtuales de un clúster determinado se aprovisionan dentro de un único sitio físico.

## <a name="dealing-with-failures"></a>Gestión de los errores
Hay varios tipos de errores que pueden afectar al clúster, cada uno con su propia solución. Los veremos en orden de probabilidad de que ocurran.

### <a name="individual-machine-failures"></a>Errores de máquinas individuales
Como se ha mencionado, los errores de máquinas individuales, bien dentro de la máquina virtual o en el hardware o el software que la hospeda en un dominio de error, no presentan riesgo por sí solos. Service Fabric normalmente detectará el error en cuestión de segundos y responderá en consecuencia según el estado del clúster. Por ejemplo, si el nodo hospedaba las réplicas principales de una partición, se elige un nuevo elemento principal de las réplicas secundarias de la partición. Cuando Azure vuelva a activar la máquina con error, unirá de nuevo el clúster automáticamente y una vez más se hará cargo de su parte de la carga de trabajo.

### <a name="multiple-concurrent-machine-failures"></a>Varios errores de máquina simultáneos
Si bien los dominios de error reducen considerablemente el riesgo de errores de máquina simultáneos, existe siempre la posibilidad de que varios errores aleatorios desactiven varias máquinas de un clúster a la vez.

En general, mientras la mayoría de los nodos permanezcan disponibles, el clúster seguirá funcionando, no obstante con menos capacidad, dado que las réplicas con estado se empaquetan en un conjunto más pequeño de máquinas y hay menos instancias sin estado disponibles para repartir la carga.

#### <a name="quorum-loss"></a>Pérdida de quórum
Si la mayoría de las réplicas de la partición de un servicio con estado deja de funcionar, esa partición pasa a un estado conocido como "pérdida de cuórum". En este punto, Service Fabric deja de permitir que se escriba en esa partición para asegurarse de que conserva un estado coherente y confiable. De hecho, hemos elegido aceptar un período de falta de disponibilidad para tener la seguridad de que no se informa a los clientes de que sus datos se han guardado cuando no es el caso. Tenga en cuenta que si ha optado por permitir lecturas desde réplicas secundarias para ese servicio con estado, puede seguir realizando esas operaciones de lectura mientras se encuentre en este estado. Una partición permanece en pérdida de cuórum hasta que esté disponible un número suficiente de réplicas o hasta que el administrador del clúster fuerce al sistema a seguir adelante mediante la [API Repair-ServiceFabricPartition][repair-partition-ps].

> [!WARNING]
> Si se realiza una acción de reparación mientras la réplica principal está inactiva, se producirá una pérdida de datos.
> 
> 

Los servicios del sistema también pueden sufrir pérdida de quórum, afectando específicamente al servicio en cuestión. Por ejemplo, la pérdida de quórum en el servicio de nombres afectará a la resolución de nombres, mientras que la pérdida de quórum en el administrador de conmutación por error bloqueará la creación de nuevos servicios y las conmutaciones por error. Tenga en cuenta que, a diferencia de sus propios servicios, *no* se recomienda reparar los servicios del sistema. Es preferible simplemente esperar a que las réplicas inactivas vuelvan a estar disponibles.

#### <a name="minimizing-the-risk-of-quorum-loss"></a>Minimización del riesgo de pérdida de quórum
Puede minimizar el riesgo de pérdida de quórum aumentando el tamaño del conjunto de réplicas de destino para el servicio. Resulta útil considerar el número de réplicas que necesita en términos del número de nodos no disponibles que puede tolerar a la vez al tiempo que permanecen disponibles para las escrituras, teniendo en cuenta, junto con los errores de hardware, que las actualizaciones de aplicaciones o clústeres pueden dejar a los nodos temporalmente no disponibles.

Considere los siguientes ejemplos que asumen que ha configurado los servicios con un valor de MinReplicaSetSize de tres, el número más pequeño recomendado para los servicios de producción. Con un valor de TargetReplicaSetSize de tres (una principal y dos secundarias), un error de hardware durante una actualización (dos réplicas inactivas) dará lugar a pérdida de quórum y su servicio pasará a ser de solo lectura. Como alternativa, si tiene cinco réplicas, podría resistir dos errores durante la actualización (tres réplicas inactivas), dado que las dos réplicas restantes pueden seguir formando un quórum dentro del conjunto de réplicas mínimo.

### <a name="data-center-outages-or-destruction"></a>Interrupciones o destrucción del centro de datos
En raras ocasiones, los centros de datos físicos pueden dejar de estar disponibles temporalmente debido a la pérdida de conectividad de red o de alimentación. En estos casos, las aplicaciones y los clústeres de Service Fabric igualmente dejarán de estar disponibles, pero sus datos se conservarán. Para los clústeres que se ejecutan en Azure, puede ver las actualizaciones en caso de interrupciones en la [página de estado de Azure][azure-status-dashboard].

En el caso muy poco probable de que se destruyera un centro de datos físico entero, los clústeres de Service Fabric allí hospedados se perderán, junto con su estado.

Para protegerse contra esta posibilidad, es muy importante [realizar una copia de seguridad periódica de su estado](service-fabric-reliable-services-backup-restore.md) en un almacén con redundancia geográfica y asegurarse de la posibilidad de restaurarlo. La frecuencia con la que realice una copia de seguridad dependerá de su objeto de punto de recuperación (RPO). Incluso si no ha implementado aún completamente la característica de copia de seguridad y restauración, debe implementar un identificador para el evento `OnDataLoss` con el fin de poder registrarlo cuando se produzca, de la manera siguiente:

```c#
protected virtual Task<bool> OnDataLoss(CancellationToken cancellationToken)
{
  ServiceEventSource.Current.ServiceMessage(this, "OnDataLoss event received.");
  return Task.FromResult(false);
}
```


### <a name="software-failures-and-other-sources-of-data-loss"></a>Errores de software y otros orígenes de pérdida de datos
Entre las causas de pérdida de datos, los defectos en el código de los servicios, los errores de manipulación humanos y las infracciones de seguridad son más comunes que los errores generalizados de los centros de datos. Sin embargo, en todos los casos, la estrategia de recuperación es la misma: realizar copias de seguridad regulares de todos los servicios con estado y ejercer la posibilidad de restaurar dicho estado.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información sobre cómo simular varios errores mediante la [plataforma de comprobación](service-fabric-testability-overview.md)
* Lea otros recursos sobre alta disponibilidad y recuperación ante desastres. Microsoft ha publicado una gran cantidad de instrucciones sobre estos temas. Aunque algunos de estos documentos se refieren a técnicas específicas para su uso en otros productos, contienen muchos procedimientos recomendados generales que se pueden aplicar también en el contexto de Service Fabric:
  * [Lista de comprobación de disponibilidad](../best-practices-availability-checklist.md)
  * [Exploración en profundidad de la recuperación ante desastres](../sql-database/sql-database-disaster-recovery-drills.md)
  * [Recuperación ante desastres y alta disponibilidad para aplicaciones de Azure][dr-ha-guide]

<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png



<!--HONumber=Dec16_HO1-->


