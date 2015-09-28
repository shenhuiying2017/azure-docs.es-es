<properties
   pageTitle="Actualización de la aplicación de Service Fabric: temas avanzados"
   description="En este artículo se tratan algunos temas avanzados relacionados con la actualización de una aplicación de Service Fabric."
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

# Actualización de la aplicación de Service Fabric: temas avanzados

## Modo de actualización manual

> [AZURE.NOTE]Solo en caso de que la actualización falle o se suspenda, deberá usarse e incluso considerarse el modo manual no supervisado. El modo supervisado es el modo de actualización recomendado para las aplicaciones de Service Fabric.

Service Fabric ofrece varios modos de actualización para admitir clústeres de desarrollo y producción. Cada una de las opciones de implementación son ideales para diversos entornos. La actualización de la aplicación gradual en modo supervisado es la actualización más típica que se va a usar en producción. Cuando se especifica la directiva de actualización, Service Fabric garantiza que la aplicación sea correcta antes de que la actualización continúe. En ciertas situaciones donde se requiere una política de evaluación de estado más personalizada o compleja o bien una actualización no convencional (la aplicación ya sufre la pérdida de datos, etc.), el administrador de la aplicación puede usar el modo de actualización de la aplicación gradual manual para tener control total sobre el progreso de la actualización a través de los diversos dominios de actualización. Por último, la actualización de la aplicación gradual automatizada es útil para que el entorno de desarrollo o prueba proporcione un ciclo de iteración rápido durante el desarrollo de servicios.

**Manual**: detenga la actualización de la aplicación en el dominio de actualización actual y cambie el modo de actualización a manual no supervisado. El administrador debe llamar manualmente a **MoveNextApplicationUpgradeDomainAsync** para continuar con la actualización o desencadenar una reversión iniciando una nueva actualización. Una vez que la actualización adopte el modo manual, permanecerá en dicho modo hasta que se inicie una nueva actualización. El comando **GetApplicationUpgradeProgressAsync** devuelve FABRIC\_APPLICATION\_UPGRADE\_STATE\_ROLLING\_FORWARD\_PENDING.

## Actualización con un paquete de diferencias

Puede actualizarse una aplicación de Service Fabric mediante el aprovisionamiento de un paquete de aplicación completo y autocontenido. También se puede actualizar una aplicación mediante un paquete de diferencias con solo los archivos de la aplicación actualizados y los archivos de los manifiestos de aplicación y servicio actualizados.

Un paquete de aplicación completo contiene todos los archivos necesarios para iniciar y ejecutar una aplicación de Service Fabric. Un paquete de diferencias contiene solo los archivos que cambiaron entre el último aprovisionamiento y la actualización actual, además de los archivos de los manifiestos de aplicación y servicio completos. Cualquier referencia en los manifiestos de aplicación o servicio que Service Fabric no encuentre en el diseño de compilación se buscará en ImageStore (vínculo por anunciar).

Los paquetes de aplicación completos se requieren para la instalación inicial de una aplicación en el clúster. Las actualizaciones posteriores pueden ser un paquete de aplicación completo o un paquete de diferencias.

Casos en los que usar un paquete de diferencias sería una buena opción:

* Se prefiere un paquete de diferencias cuando tiene un paquete de aplicación grande que hace referencia a varios archivos del manifiesto de servicio o varios paquetes de código, configuración o datos.

* Asimismo, se prefiere un paquete de diferencias cuando dispone de un sistema de implementación que genera el diseño de compilación directamente desde el proceso de compilación de su aplicación. En este caso, aunque no haya cambiado nada en el código, los ensamblados recién creados tendrán una suma de comprobación diferente. Para usar un paquete de aplicación completo, sería necesario que actualizara la versión de todos los paquetes de código. Con un paquete de diferencias, solo proporciona los archivos que cambiaron y los archivos de manifiesto cuya versión ha cambiado.

## Pasos siguientes

[Tutorial de actualización](service-fabric-application-upgrade-tutorial.md)

[Parámetros de actualización](service-fabric-application-upgrade-parameters.md)

[Serialización de datos](service-fabric-application-upgrade-data-serialization.md)

[Solucionar problemas de una actualización de aplicación ](service-fabric-application-upgrade-troubleshooting.md)
 

<!---HONumber=Sept15_HO3-->