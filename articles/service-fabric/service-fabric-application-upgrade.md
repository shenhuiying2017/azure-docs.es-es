<properties
   pageTitle="Actualización de la aplicación de Service Fabric"
   description="En este artículo se proporciona una introducción a la actualización de una aplicación de Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="subramar"/>


# Actualización de la aplicación de Service Fabric


Una aplicación de Service Fabric es una colección de servicios. Durante una actualización, Service Fabric compara el nuevo [manifiesto de aplicación](service-fabric-application-model.md#describe-an-application) con la versión anterior y determina qué servicios de la aplicación requieren actualizaciones. Esto se determina comparando los números de versión de los manifiestos de servicio con la versión anterior. Si un servicio no ha cambiado, no se actualiza.

## Información general de las actualizaciones graduales
En una actualización de la aplicación gradual, la actualización se realiza por etapas. En cada etapa, la actualización se aplica a un subconjunto de nodos del clúster, llamado dominio de actualización. Como resultado, la aplicación permanece disponible durante la actualización. Durante la actualización, el clúster puede contener una mezcla de las versiones anteriores y nuevas. Por esa razón, las dos versiones deben ser compatibles con las versiones anteriores y nuevas. Si no son compatibles, el administrador de la aplicación es responsable del ensayo de una actualización multifase para mantener la disponibilidad. Esto se hace realizando una actualización con una versión intermedia de la aplicación que es compatible con la versión anterior antes de actualizar a la versión final.

Los dominios de actualización se especifican en el manifiesto de clúster al configurar el clúster. No debe asumir que los dominios de actualización recibirán actualizaciones en un orden concreto. Un dominio de actualización es una unidad lógica de implementación para una aplicación. Los dominios de actualización permiten que la disponibilidad de los servicios sea alta durante una actualización.

Las actualizaciones no graduales son posibles si la actualización se aplica a todos los nodos del clúster, que es el caso cuando la aplicación tiene un solo dominio de actualización. Esto no se recomienda, ya que el servicio estará inactivo y no disponible en el momento de la actualización. Además, Azure no ofrece ninguna garantía cuando se configura un clúster con un solo dominio de actualización.

## Comprobaciones de estado durante las actualizaciones
Para realizar una actualización, las directivas de mantenimiento deben establecerse (o se pueden usar valores predeterminados). Una actualización se denomina correcta cuando se actualizan todos los dominios de actualización dentro de los tiempos de expiración especificados, y dichos dominios se consideran correctos. Un dominio de actualización correcto significa que el dominio de actualización supera todas las comprobaciones de estado especificadas en la directiva de mantenimiento. Por ejemplo, una directiva de mantenimiento puede exigir que todos los servicios dentro de una instancia de aplicación sean <em>correctos</em>, ya que Service Fabric define el estado.

Las directivas de mantenimiento y comprobaciones de estado durante la actualización de Service Fabric son independientes de los servicios y aplicaciones. Es decir, no se comprueba ninguna prueba específica del servicio. Por ejemplo, su servicio tiene un requisito de rendimiento mínimo. Sin embargo, Service Fabric no tiene la información para demostrarlo y, por tanto, no comprobará el rendimiento como se define para su aplicación. Consulte los [artículos de estado](service-fabric-health-introduction.md) para las comprobaciones que se realizan (las comprobaciones durante la actualización incluyen si el paquete de aplicación se copió correctamente y si se inició la instancia, y así sucesivamente).

El estado de la aplicación es una agregación de las entidades secundarias de la aplicación. En resumen, Service Fabric evalúa el estado de la aplicación a través del estado notificado en la aplicación, así como todo el estado de los servicios de la aplicación. El estado de los servicios de aplicación se evalúa aún más al agregar el estado de sus elementos secundarios, como la réplica de servicio. Una vez que se cumpla la directiva de mantenimiento de la aplicación, la actualización podrá continuar o, en caso de que se haya producido una infracción de dicha directiva, la actualización de la aplicación se interrumpirá.

## Modos de actualización

El modo común (y recomendado) de actualización es el modo supervisado. El modo supervisado realiza la actualización en un dominio de actualización y, si se superan todas las comprobaciones de estado (según la directiva especificada), pasa al siguiente dominio de actualización automáticamente, y así sucesivamente. Si se produce un error en las comprobaciones de estado o los tiempos de expiración llegan a su fin, la actualización se revierte para el dominio de actualización o el modo cambia a UnmonitoredManual si esa es la opción seleccionada en el momento de la actualización.

UnmonitoredManual requeriría intervención manual tras cada actualización en un dominio de actualización para iniciar la actualización en el siguiente dominio de actualización. No se ha realizado ninguna comprobación de estado de Service Fabric, dependiendo esta del interviniente antes de iniciar la actualización en el siguiente dominio de actualización.

## Diagrama de flujo de actualización de la aplicación

El siguiente diagrama de flujo ayuda a entender el proceso de actualización de una aplicación de Service Fabric. En concreto, el flujo describe cómo los tiempos de expiración, incluidos *HealthCheckStableDuration*, *HealthCheckRetryTimeout* y *UpgradeHealthCheckInterval* ayudan a controlar cuándo se considera un éxito o un fracaso la actualización en un dominio de actualización.

![El proceso de actualización de una aplicación de Service Fabric][image]


## Pasos siguientes

[Tutorial de actualización](service-fabric-application-upgrade-tutorial.md)

[Parámetros de actualización](service-fabric-application-upgrade-parameters.md)

[Serialización de datos](service-fabric-application-upgrade-data-serialization.md)

[Temas avanzados](service-fabric-application-upgrade-advanced.md)

[Solucionar problemas de una actualización de aplicación ](service-fabric-application-upgrade-troubleshooting.md)



[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
 

<!---HONumber=August15_HO6-->