<properties
   pageTitle="Administrador de recursos de clúster de Service Fabric: afinidad | Microsoft Azure"
   description="Información general sobre las reglas y directivas de colocación adicionales para los servicios de Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/10/2016"
   ms.author="masnider"/>

# Configuración y uso de la afinidad de servicio en Service Fabric

La afinidad es una de esas cosas que, al menos a primera vista, no parece muy lógico en un entorno de microservicios. Esto se debe a que no tiene mucho sentido en un entorno de microservicios. La afinidad es un control que se proporciona principalmente para facilitar la transición de aplicaciones grandes y monolíticas al mundo de la nube y los microservicios.

Supongamos que desea llevar a Service Fabric una aplicación de mayor tamaño, o bien una aplicación que simplemente no se diseñó teniendo en mente los microservicios. Esto es bastante habitual, y ya ha habido varios clientes (internos y externos) en esta situación. Para empezar, traslade toda la aplicación al entorno y haga que se empaquete y se ejecute. A continuación, divídala en servicios más pequeños que se comuniquen entre sí.

Ahora es cuando surge un problema. Generalmente pertenece a una de estas categorías:

1. El componente X de la aplicación monolítica tenía una dependencia sin documentar en el componente Y que acabamos de convertir en un servicio y mover por el clúster. Ahora se ha roto.
2.	Estos elementos se comunican a través de (canalizaciones con nombre local | memoria compartida | archivos en disco), pero se necesita poder realizar la actualización de forma independiente para acelerar el proceso.
3.	Todo es correcto, pero estos dos componentes están muy fragmentados o son muy sensibles al rendimiento y, por eso, al moverlos a servicios separados, el rendimiento se reduce.

En todos estos casos, no nos interesa perder nuestro trabajo de refactorización ni volver al monolito, pero nos conviene recomponer las cosas para parezcan locales hasta que podemos arreglarlo todo.

¿Qué debe hacer? Podemos intentar activar la afinidad.

## Cómo funciona la afinidad
Para configurar la afinidad, debe definir una relación de afinidad entre dos servicios diferentes. Es como si hiciera que un servicio "apuntara" a otro y dijera "Este servicio solo se puede ejecutar donde se esté ejecutando ese otro". A veces se denominan relaciones de tipo primario-secundario (en las que el elemento secundario apunta al elemento primario). Lo que esto hace es garantizar que las réplicas o las instancias de un servicio se colocan en los mismos nodos que las réplicas o las instancias del servicio con las que tiene afinidad.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## Diferentes opciones de afinidad
La afinidad se representa mediante uno de varios esquemas de correlación y tiene dos modos diferentes. El modo de afinidad más común es el denominado NonAlignedAffinity. En NonAlignedAffinity, las réplicas o las instancias de los diferentes servicios se colocan en los mismos nodos. El otro modo es AlignedAffinity. AlignedAffinity se usa con los servicios con estado. La configuración de un servicio con estado de modo que tenga afinidad alineada con otro servicio con estado garantiza que los elementos primarios de esos servicios se colocan en los mismos nodos y que cada par de elementos secundarios también se colocan en los mismos nodos. También es posible (aunque menos frecuente) configurar NonAlignedAffinity para servicios con estado. En este caso, obtendrá en gran medida el mismo comportamiento que para los servicios sin estado. Es decir, las diferentes réplicas de los dos servicios con estado se colocarán en los mismos nodos, pero no se realizará ningún intento para garantizar que los elementos primarios y secundarios se encuentren en los mismos nodos.

![Modos de afinidad y consecuencias][Image1]

### Estado de mejor esfuerzo deseado
Existen algunas diferencias entre las arquitecturas monolíticas y las de afinidad. Casi todas se reducen al hecho de que una relación de afinidad es la mejor opción: como son fundamentalmente servicios diferentes, pueden producir errores de forma independiente, por poner un ejemplo. Otras cosas pueden hacer que las diferentes réplicas del servicio se separen, como las limitaciones de capacidad.


### Cadenas frente a estrellas
Actualmente no es posible crear cadenas de relaciones de afinidad. Esto significa que un servicio que es un elemento secundario de una relación de afinidad no puede ser un elemento primario de otra relación de afinidad. Como resultado, si desea crear este tipo de relación, debe crearla como una estrella, en vez de como una cadena. Para ello, haga que el elemento secundario más bajo se convierta en el elemento primario del elemento secundario "central".

![Cadenas frente a estrellas en el contexto de las relaciones de afinidad][Image2]

Otra cuestión que debe tener en cuenta sobre las relaciones de afinidad en la actualidad es que son direccionales. Se trata de algo sutil, pero en realidad significa que la regla de "afinidad" solo requiere que el elemento secundario se encuentre en el mismo lugar que el elemento primario. En caso de que el elemento primario deba conmutar por error repentinamente a otro nodo (o cualquier otra acción limitada que obligue a mover solo el elemento primario), Resource Manager no pensará que se ha producido un error hasta que se dé cuenta de que el elemento secundario no está ubicado junto a un elemento primario; la relación no se aplicará inmediatamente.

### Compatibilidad con particiones
Lo último que debe tener en cuenta sobre la afinidad es que no se admiten relaciones de afinidad en caso de que el elemento primario esté particionado. Esto es algo que se podría admitir finalmente, pero actualmente no es el caso.

## Pasos siguientes
- Para más información sobre las otras opciones disponibles para configurar servicios, consulte el tema sobre las demás configuraciones del Administrador de recursos de clúster disponibles. [Más información sobre cómo configurar los servicios](service-fabric-cluster-resource-manager-configure-services.md).

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png

<!---HONumber=AcomDC_0316_2016-->