<properties
   pageTitle="Administrador de recursos de clúster de Service Fabric: afinidad | Microsoft Azure"
   description="Información general sobre la configuración de afinidad para servicios de Service Fabric"
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
   ms.date="08/19/2016"
   ms.author="masnider"/>

# Configuración y uso de la afinidad de servicio en Service Fabric

La afinidad es un control que se proporciona principalmente para facilitar la transición de aplicaciones grandes y monolíticas al mundo de la nube y los microservicios. Además, también puede usarse en ciertos casos como una optimización legítima para mejorar el rendimiento de servicios, aunque esto puede tener efectos secundarios.

Supongamos que desea llevar a Service Fabric una aplicación de mayor tamaño, o bien una aplicación que simplemente no se diseñó teniendo en mente los microservicios. Esta transición es bastante habitual, y ya ha habido varios clientes (internos y externos) en esta situación. Para empezar, traslade toda la aplicación al entorno y haga que se empaquete y se ejecute. A continuación, divídala en servicios más pequeños que se comuniquen entre sí.

Ahora es cuando surge un problema. Generalmente pertenece a una de estas categorías:

1. Uno de los componentes X de la aplicación monolítica tenía una dependencia sin documentar del componente Y, y acabamos de convertirlos en servicios independientes. Como ahora se ejecutan en diferentes nodos del clúster, están separados.
2.	Estos elementos se comunican a través de (canalizaciones con nombre local | memoria compartida | archivos en disco), pero se necesita poder realizar la actualización de forma independiente para acelerar el proceso. Eliminaré la dependencia física más adelante.
3.	Todo es correcto, pero resulta que estos dos componentes están muy fragmentados o son muy sensibles al rendimiento. Cuando se mueven a servicios independientes, el rendimiento general de la aplicación se reduce y aumenta la latencia. Como resultado, la aplicación en su conjunto no cumple las expectativas.

En estos casos, no queremos perder nuestro trabajo de refactorización, y no queremos volver a lo monolítico, pero necesitamos algún sentido de localidad. Esta situación se mantendrá hasta que podamos rediseñar los componentes para que funcionen de manera natural como servicios o hasta que podamos resolver las expectativas de rendimiento de alguna otra forma, si es posible.

¿Qué debe hacer? Podemos intentar activar la afinidad.

## Configuración de la afinidad
Para configurar la afinidad, debe definir una relación de afinidad entre dos servicios diferentes. Es como si hiciera que un servicio "apuntara" a otro y dijera "Este servicio solo se puede ejecutar donde se esté ejecutando ese otro". A veces se denominan relaciones de tipo primario-secundario (en las que el elemento secundario apunta al elemento primario). La afinidad garantiza que las réplicas o las instancias de un servicio se colocan en los mismos nodos que las réplicas o las instancias de otro.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## Diferentes opciones de afinidad
La afinidad se representa mediante uno de varios esquemas de correlación y tiene dos modos diferentes. El modo de afinidad más común es el denominado NonAlignedAffinity. En NonAlignedAffinity, las réplicas o las instancias de los diferentes servicios se colocan en los mismos nodos. El otro modo es AlignedAffinity. AlignedAffinity resulta de utilidad con servicios con estado. Configurar dos servicios con estado para tener afinidad alineada garantiza que los elementos principales de esos servicios se colocan en los mismos nodos. También hace que cada par de elementos secundarios de esos servicios se coloquen en los mismos nodos. También es posible (aunque menos frecuente) configurar NonAlignedAffinity para servicios con estado. Para NonAlignedAffinity, las diferentes réplicas de los dos servicios con estado se colocarían en los mismos nodos, pero no se realizaría ningún intento por alinear sus elementos principales o secundarios.

![Modos de afinidad y consecuencias][Image1]

### Estado de mejor esfuerzo deseado
Existen algunas diferencias entre las arquitecturas monolíticas y las de afinidad. Muchas de ellas se deben a que la relación de afinidad es la mejor opción. Los servicios en una relación de afinidad son entidades básicamente diferentes que pueden producir error y moverse de forma independiente. También existen motivos por los que una relación de afinidad se podría romper. Por ejemplo, las limitaciones de capacidad por las que solo algunos de los objetos de servicio de la relación de afinidad pueden caber en un determinado nodo. En estos casos, aunque exista una relación de afinidad, no se puede aplicar debido a las otras restricciones. Si más adelante es posible aplicar todas las demás restricciones y la afinidad, la infracción de la restricción de afinidad se corregirá automáticamente.

### Cadenas frente a estrellas
Actualmente no es posible crear cadenas de relaciones de afinidad. Esto significa que un servicio que es un elemento secundario de una relación de afinidad no puede ser un elemento primario de otra relación de afinidad. Si desea modelar este tipo de relación, tendrá que crear una estrella, en lugar de una cadena. Para ello, el elemento secundario de la parte inferior se convertiría en el elemento primario del elemento secundario del medio. Dependiendo de la disposición de los servicios, puede que sea necesario crear un servicio "marcador de posición" que actúe como el elemento primario de varios elementos secundarios.

![Cadenas frente a estrellas en el contexto de las relaciones de afinidad][Image2]

Otra cuestión que debe tener en cuenta sobre las relaciones de afinidad en la actualidad es que son direccionales. Esto significa que la regla de "afinidad" solo se aplica si el elemento secundario está donde el elemento primario. Si, por ejemplo, el elemento primario conmuta por error de forma repentina a otro nodo, Cluster Resource Manager no cree que algo vaya mal hasta que se da cuenta de que el elemento secundario no está con el primario; la relación no se aplica inmediatamente.

### Compatibilidad con particiones
Lo último que debe tener en cuenta sobre la afinidad es que no se admiten relaciones de afinidad en caso de que el elemento primario esté particionado. Esto es algo que se podría admitir finalmente, pero actualmente no es el caso.

## Pasos siguientes
- Para más información sobre las otras opciones disponibles para configurar servicios, consulte el tema sobre las demás configuraciones de Cluster Resource Manager disponibles en [Más información sobre la configuración de servicios](service-fabric-cluster-resource-manager-configure-services.md).
- Muchas de las razones por las que la gente usa la afinidad, por ejemplo, para limitar los servicios a un conjunto pequeño de máquinas o para intentar agregar la carga a una colección de servicios, se sostienen mejor mediante grupos de aplicaciones. Consulte [Application Groups](service-fabric-cluster-resource-manager-application-groups.md) (Grupos de aplicaciones).

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png

<!---HONumber=AcomDC_0824_2016-->