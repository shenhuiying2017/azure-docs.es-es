---
title: "Configuración y uso de la afinidad de servicio en Service Fabric | Microsoft Docs"
description: "Información general sobre la configuración de afinidad para servicios de Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 678073e1-d08d-46c4-a811-826e70aba6c4
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 16863217eddf0a4bbd85c52f8481c03e50dd9108


---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Configuración y uso de la afinidad de servicio en Service Fabric
La afinidad es un control que se proporciona principalmente para facilitar la transición de aplicaciones grandes y monolíticas al mundo de la nube y los microservicios. Además, también puede usarse en ciertos casos como una optimización legítima para mejorar el rendimiento de servicios, aunque hacer esto puede tener efectos secundarios.

Supongamos que desea llevar a Service Fabric una aplicación de mayor tamaño, o bien una aplicación que simplemente no se diseñó teniendo en mente los microservicios. Este tipo de transición es habitual. Para empezar, traslade toda la aplicación al entorno, empaquétela y asegúrese de que se ejecute sin problemas. A continuación, divídala en servicios más pequeños que se comuniquen entre sí.

Ahora es cuando surge un problema. Generalmente pertenece a una de estas categorías:

1. Uno de los componentes X de la aplicación monolítica tenía una dependencia sin documentar del componente Y, y acaba de convertir estos componentes en servicios independientes. Como ahora estos servicios se ejecutan en diferentes nodos del clúster, están separados.
2. Estos elementos se comunican a través de (canalizaciones con nombre local | memoria compartida | archivos en disco), pero realmente necesitan poder escribir ahora mismo en un recurso compartido por motivos de rendimiento. Esa dependencia física se quita más adelante.
3. Todo es correcto, pero resulta que estos dos componentes están realmente fragmentados o son sensibles al rendimiento. Cuando se mueven a servicios independientes, el rendimiento general de la aplicación se reduce y aumenta la latencia. Como resultado, la aplicación en su conjunto no cumple las expectativas.

En estos casos, no queremos perder nuestro trabajo de refactorización y no queremos volver a lo monolítico. Sin embargo, hasta que podamos rediseñar los componentes para que funcionen de manera natural como servicios o hasta que podamos resolver las expectativas de rendimiento de alguna otra forma, vamos a seguir necesitando algún sentido de localidad.

¿Qué debe hacer? Podemos intentar activar la afinidad.

## <a name="how-to-configure-affinity"></a>Configuración de la afinidad
Para configurar la afinidad, debe definir una relación de afinidad entre dos servicios diferentes. Es como si hiciera que un servicio "apuntara" a otro y dijera "Este servicio solo se puede ejecutar donde se esté ejecutando ese otro". A veces se denominan relaciones de tipo primario-secundario (en las que el elemento secundario apunta al elemento primario). La afinidad garantiza que las réplicas o las instancias de un servicio se colocan en los mismos nodos que las réplicas o las instancias de otro.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## <a name="different-affinity-options"></a>Diferentes opciones de afinidad
La afinidad se representa mediante uno de varios esquemas de correlación y tiene dos modos diferentes. El modo de afinidad más común es el denominado NonAlignedAffinity. En NonAlignedAffinity, las réplicas o las instancias de los diferentes servicios se colocan en los mismos nodos. El otro modo es AlignedAffinity. AlignedAffinity resulta de utilidad con servicios con estado. Configurar dos servicios con estado para tener afinidad alineada garantiza que los elementos principales de esos servicios se colocan en los mismos nodos. También hace que cada par de elementos secundarios de esos servicios se coloquen en los mismos nodos. También es posible (aunque menos frecuente) configurar NonAlignedAffinity para servicios con estado. Para NonAlignedAffinity, las diferentes réplicas de los dos servicios con estado se colocarían en los mismos nodos, pero no se realizaría ningún intento por alinear sus elementos principales o secundarios.

<center>
![Modos de afinidad y consecuencias][Image1]
</center>

### <a name="best-effort-desired-state"></a>Estado de mejor esfuerzo deseado
Existen algunas diferencias entre las arquitecturas monolíticas y las de afinidad. Muchas de ellas se deben a que la relación de afinidad es la mejor opción. Los servicios en una relación de afinidad son entidades básicamente diferentes que pueden producir error y moverse de forma independiente. También existen motivos por los que una relación de afinidad se podría romper. Por ejemplo, las limitaciones de capacidad por las que solo algunos de los objetos de servicio de la relación de afinidad pueden caber en un determinado nodo. En estos casos, aunque exista una relación de afinidad, no se puede aplicar debido a las otras restricciones. Si es posible hacerlo, la infracción se corrige automáticamente más adelante.

### <a name="chains-vs-stars"></a>Cadenas frente a estrellas
Actualmente, Cluster Resource Manager no puede crear cadenas de relaciones de afinidad. Esto significa que un servicio que es un elemento secundario de una relación de afinidad no puede ser un elemento primario de otra relación de afinidad. Si desea modelar este tipo de relación, tendrá que crear una estrella, en lugar de una cadena. Para pasar de una cadena a una estrella, el elemento secundario de la parte inferior se convertiría en el elemento primario del primer elemento secundario. En función de la disposición de los servicios, puede que tenga que repetir esto varias veces. Si no hay un servicio principal natural, puede que tenga que crear uno que actúe como marcador de posición. En función de los requisitos, puede que también desee observar los [grupos de aplicaciones](service-fabric-cluster-resource-manager-application-groups.md).

<center>
![Cadenas frente a estrellas en el contexto de las relaciones de afinidad][Image2]
</center>

Otra cuestión que debe tener en cuenta sobre las relaciones de afinidad en la actualidad es que son direccionales. Esto significa que la regla de "afinidad" solo se aplica si el elemento secundario está donde el elemento primario y no que el elemento primario está ubicado con el elemento secundario. Por ejemplo, supongamos que el elemento primario de repente conmuta por error a otro nodo. Cuando esto sucede, Cluster Resource Manager piensa que todo funciona correctamente hasta que observa que el elemento secundario no se encuentra con un elemento primario. La relación de afinidad no puede ser perfecta ni forzada de manera inmediata, debido a que se trata de servicios diferentes con ciclos de vida distintos.

### <a name="partitioning-support"></a>Compatibilidad con particiones
Lo último que debe tener en cuenta sobre la afinidad es que no se admiten relaciones de afinidad en caso de que el elemento primario esté particionado. Esto es algo que se podría admitir finalmente, pero actualmente no es el caso.

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre las otras opciones disponibles para configurar servicios, consulte el tema sobre las demás configuraciones de Cluster Resource Manager disponibles en [Más información sobre la configuración de servicios](service-fabric-cluster-resource-manager-configure-services.md)
* Para usos como limitar los servicios a un conjunto pequeño de máquinas o intentar agregar la carga de una colección de servicios, use los [grupos de aplicaciones](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png



<!--HONumber=Jan17_HO1-->


