<properties
   pageTitle="Actualización de aplicación: temas avanzados | Microsoft Azure"
   description="En este artículo se tratan algunos temas avanzados relacionados con la actualización de una aplicación de Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/14/2016"
   ms.author="subramar"/>

# Actualización de la aplicación de Service Fabric: temas avanzados

## Adición o eliminación de servicios durante la actualización de una aplicación

Si se agrega un nuevo servicio a una aplicación que ya está implementada, y publicada como una actualización, se agregará el nuevo servicio a la aplicación implementada (sin la actualización que afecte a cualquiera de los servicios que ya formaban parte de la aplicación). Sin embargo, tendrá que iniciarse una instancia del servicio que se agregó para que el nuevo servicio esté activo (mediante el cmdlet `New-ServiceFabricService`).

También se pueden quitar servicios de una aplicación como parte de una actualización. Sin embargo, debe asegurarse de que todas las instancias actuales del servicio (es decir, que se quiten como parte de la actualización) se detienen antes de continuar con la actualización (mediante el cmdlet `Remove-ServiceFabricService`).

## Modo de actualización manual

> [AZURE.NOTE]  El modo manual no supervisado debe considerarse solo si la actualización presenta errores o si se suspende. El modo supervisado es el modo de actualización recomendado para las aplicaciones de Service Fabric.

Azure Service Fabric ofrece varios modos de actualización para admitir clústeres de desarrollo y producción. Cada una de las opciones de implementación son ideales para diversos entornos.

La actualización de la aplicación gradual en modo supervisado es la actualización más típica que se usa en producción. Cuando se especifica la directiva de actualización, Service Fabric garantiza que la aplicación sea correcta antes de que la actualización continúe.

 El Administrador de aplicaciones puede utilizar el modo de actualización de aplicación gradual manual para tener control total sobre el progreso de la actualización a través de los distintos dominios de actualización. Este modo es útil para determinadas situaciones en que se necesita una política de evaluación de estado más personalizada o compleja, o donde hay una actualización no convencional (por ejemplo, aplicación ya está en pérdida de datos).

Por último, la actualización de la aplicación gradual automatizada es útil para que los entornos de desarrollo o prueba proporcionen un ciclo de iteración rápido durante el desarrollo de servicios.

## Cambio al modo de actualización manual
**Manual**: detenga la actualización de la aplicación en el dominio de actualización actual y cambie el modo de actualización a manual no supervisado. El administrador debe llamar manualmente a **MoveNextApplicationUpgradeDomainAsync** para continuar con la actualización o desencadenar una reversión iniciando una nueva actualización. Una vez que la actualización adopte el modo manual, permanecerá en dicho modo hasta que se inicie una nueva actualización. El comando **GetApplicationUpgradeProgressAsync** devuelve FABRIC\_APPLICATION\_UPGRADE\_STATE\_ROLLING\_FORWARD\_PENDING.

## Actualización con un paquete de diferencias

Puede actualizarse una aplicación de Service Fabric mediante el aprovisionamiento de un paquete de aplicación completo y autocontenido. También se puede actualizar una aplicación mediante un paquete de diferencias que contenga solo los archivos de la aplicación actualizados y los archivos de los manifiestos de aplicación y servicio actualizados.

Un paquete de aplicación completo contiene todos los archivos necesarios para iniciar y ejecutar una aplicación de Service Fabric. Un paquete de diferencias contiene solo los archivos que cambiaron entre el último aprovisionamiento y la actualización actual, además de los archivos de los manifiestos de aplicación y servicio completos. Cualquier referencia en los manifiestos de aplicación o servicio que Service Fabric no pueda encontrar en el diseño de compilación, la buscará en el almacén de imágenes.

Los paquetes de aplicación completos se requieren para la instalación inicial de una aplicación en el clúster. Las actualizaciones posteriores pueden ser un paquete de aplicación completo o un paquete de diferencias.

Casos en los que usar un paquete de diferencias sería una buena opción:

* Se prefiere un paquete de diferencias cuando tiene un paquete de aplicación grande que hace referencia a varios archivos del manifiesto de servicio o varios paquetes de código, configuración o datos.

* Asimismo, se prefiere un paquete de diferencias cuando dispone de un sistema de implementación que genera el diseño de compilación directamente desde el proceso de compilación de su aplicación. En este caso, aunque no haya cambiado nada en el código, los ensamblados recién creados tendrán una suma de comprobación diferente. Para usar un paquete de aplicación completo, sería necesario que actualizara la versión de todos los paquetes de código. Con un paquete de diferencias, solo proporciona los archivos que cambiaron y los archivos de manifiesto cuya versión ha cambiado.

Cuando se actualiza una aplicación mediante Visual Studio, el paquete de diferencias se publica automáticamente. Si desea crear un paquete de diferencias manualmente (por ejemplo, para realizar la actualización con PowerShell), debe actualizar la aplicación y los manifiestos de servicio, pero solo incluir los paquetes que se han cambiado en el paquete de aplicación final.

Por ejemplo, comencemos con la siguiente aplicación (se proporcionan los números de versión para facilitar la comprensión):

```text
app1       	1.0.0
  service1 	1.0.0
    code   	1.0.0
    config 	1.0.0
  service2 	1.0.0
    code   	1.0.0
    config 	1.0.0
```

Ahora, supongamos que desea actualizar solo el paquete de código de service1 mediante un paquete de diferencias con PowerShell. Ahora, la aplicación actualizada tendrá un aspecto similar al siguiente:

```text
app1       	2.0.0      <-- new version
  service1 	2.0.0      <-- new version
    code   	2.0.0      <-- new version
    config 	1.0.0
  service2 	1.0.0
    code   	1.0.0
    config 	1.0.0
```

En este caso, actualice el manifiesto de aplicación 2.0.0 y el manifiesto de servicio de service1 para reflejar la actualización del paquete de código. La estructura de carpetas para el paquete de aplicación sería similar al siguiente:

```text
app1/
  service1/
    code/
```

## Pasos siguientes

El procedimiento de [actualización de aplicaciones usando Visual Studio](service-fabric-application-upgrade-tutorial.md) ofrece información para actualizar una aplicación mediante Visual Studio.

El procedimiento de [actualización de aplicaciones usando Powershell](service-fabric-application-upgrade-tutorial-powershell.md) ofrece información para actualizar una aplicación mediante PowerShell.

Puede controlar cómo se actualiza una aplicación usando [parámetros de actualización](service-fabric-application-upgrade-parameters.md).

Consiga que sus actualizaciones de aplicaciones sean compatibles aprendiendo a usar la [serialización de datos](service-fabric-application-upgrade-data-serialization.md).

Solucione problemas habituales en las actualizaciones de aplicaciones consultando los pasos que figuran en [Solución de problemas de las actualizaciones de aplicaciones](service-fabric-application-upgrade-troubleshooting.md).
 

<!---HONumber=AcomDC_0420_2016-->