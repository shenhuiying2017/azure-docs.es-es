---
title: "Temas avanzados de actualización de aplicación | Microsoft Docs"
description: "En este artículo se tratan algunos temas avanzados relacionados con la actualización de una aplicación de Service Fabric."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: e29585ff-e96f-46f4-a07f-6682bbe63281
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar;chackdan
ms.openlocfilehash: 8d3b922f3d50b645ac9db2cc879a319df1262e0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Actualización de la aplicación de Service Fabric: temas avanzados
## <a name="adding-or-removing-services-during-an-application-upgrade"></a>Adición o eliminación de servicios durante la actualización de una aplicación
Si se agrega un nuevo servicio a una aplicación que ya está implementada y publicada como una actualización, el nuevo servicio se agrega a la aplicación implementada.  Dicha actualización no afecta a ninguno de los servicios que ya formaban parte de la aplicación. Sin embargo, tendrá que iniciarse una instancia del servicio que se agregó para que el nuevo servicio esté activo (mediante el cmdlet `New-ServiceFabricService` ).

También se pueden quitar servicios de una aplicación como parte de una actualización. Sin embargo, se deben detener todas las instancias actuales del servicio se va a eliminar antes de continuar con la actualización (mediante el cmdlet `Remove-ServiceFabricService` ).

## <a name="manual-upgrade-mode"></a>Modo de actualización manual
> [!NOTE]
> El modo manual no supervisado debe considerarse solo si la actualización presenta errores o si se suspende. El modo supervisado es el modo de actualización recomendado para las aplicaciones de Service Fabric.
>
>

Azure Service Fabric ofrece varios modos de actualización para admitir clústeres de desarrollo y producción. Las opciones de implementación elegidas pueden ser diferentes para distintos entornos.

La actualización gradual de la aplicación en modo supervisado es la actualización más típica que se usa en el entorno de producción. Cuando se especifica la directiva de actualización, Service Fabric garantiza que la aplicación sea correcta antes de que la actualización continúe.

 El Administrador de aplicaciones puede utilizar el modo de actualización de aplicación gradual manual para tener control total sobre el progreso de la actualización a través de los distintos dominios de actualización. Este modo es útil cuando se necesita una directiva de evaluación de estado personalizada o compleja, o tiene lugar una actualización poco convencional (por ejemplo, la aplicación ya tiene pérdida de datos).

Por último, la actualización de la aplicación gradual automatizada es útil para que los entornos de desarrollo o prueba proporcionen un ciclo de iteración rápido durante el desarrollo de servicios.

## <a name="change-to-manual-upgrade-mode"></a>Cambio al modo de actualización manual
**Manual**: detenga la actualización de la aplicación en el dominio de actualización actual y cambie el modo de actualización a manual no supervisado. El administrador debe llamar manualmente a **MoveNextApplicationUpgradeDomainAsync** para continuar con la actualización o desencadenar una reversión iniciando una nueva actualización. Una vez que la actualización adopte el modo manual, permanecerá en dicho modo hasta que se inicie una nueva actualización. El comando **GetApplicationUpgradeProgressAsync** devuelve FABRIC\_APPLICATION\_UPGRADE\_STATE\_ROLLING\_FORWARD\_PENDING.

## <a name="upgrade-with-a-diff-package"></a>Actualización con un paquete de diferencias
Puede actualizarse una aplicación de Service Fabric mediante el aprovisionamiento de un paquete de aplicación completo y autocontenido. También se puede actualizar una aplicación mediante un paquete de diferencias que contenga solo los archivos de la aplicación actualizados y los archivos de los manifiestos de aplicación y servicio actualizados.

Un paquete de aplicación completo contiene todos los archivos necesarios para iniciar y ejecutar una aplicación de Service Fabric. Un paquete de diferencias contiene solo los archivos que cambiaron entre el último aprovisionamiento y la actualización actual, además de los archivos de los manifiestos de aplicación y servicio completos. Cualquier referencia en el manifiesto de aplicación o de servicio que no se encuentra en el diseño de compilación se busca en el almacén de imágenes.

Los paquetes de aplicación completos se requieren para la instalación inicial de una aplicación en el clúster. Las actualizaciones posteriores pueden ser un paquete de aplicación completo o un paquete de diferencias.

Casos en los que usar un paquete de diferencias sería una buena opción:

* Se prefiere un paquete de diferencias cuando tiene un paquete de aplicación grande que hace referencia a varios archivos del manifiesto de servicio o varios paquetes de código, configuración o datos.
* Asimismo, se prefiere un paquete de diferencias cuando dispone de un sistema de implementación que genera el diseño de compilación directamente desde el proceso de compilación de su aplicación. En este caso, aunque el código no ha cambiado, los ensamblados recién creados obtienen una suma de comprobación diferente. Para usar un paquete de aplicación completo, sería necesario que actualizara la versión de todos los paquetes de código. Con un paquete de diferencias, solo proporciona los archivos que cambiaron y los archivos de manifiesto cuya versión ha cambiado.

Cuando se actualiza una aplicación mediante Visual Studio, el paquete de diferencias se publica automáticamente. Para crear manualmente un paquete de diferencias, se deben actualizar el manifiesto de aplicación y los manifiestos de servicio, pero solo los paquetes modificados deben incluirse en el paquete de aplicación final.

Por ejemplo, comencemos con la siguiente aplicación (se proporcionan los números de versión para facilitar la comprensión):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Ahora, supongamos que desea actualizar solo el paquete de código de service1 mediante un paquete de diferencias con PowerShell. Ahora, la aplicación actualizada tiene la siguiente estructura de carpetas:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

En este caso, actualice el manifiesto de aplicación 2.0.0 y el manifiesto de servicio de service1 para reflejar la actualización del paquete de código. La estructura de carpetas para el paquete de aplicación sería similar a la siguiente:

```text
app1/
  service1/
    code/
```

## <a name="next-steps"></a>Pasos siguientes
[actualización de aplicaciones usando Visual Studio](service-fabric-application-upgrade-tutorial.md) ofrece información para actualizar una aplicación mediante Visual Studio.

[actualización de aplicaciones mediante PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) se explica en detalle lo que tiene que hacer para actualizar una aplicación mediante PowerShell.

Puede controlar cómo se actualiza una aplicación usando [parámetros de actualización](service-fabric-application-upgrade-parameters.md).

Consiga que sus actualizaciones de aplicaciones sean compatibles aprendiendo a usar la [serialización de datos](service-fabric-application-upgrade-data-serialization.md).

Solucione problemas habituales en las actualizaciones de aplicaciones consultando los pasos que figuran en [Solución de problemas de las actualizaciones de aplicaciones](service-fabric-application-upgrade-troubleshooting.md).
