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
ms.date: 2/5/2018
ms.author: subramar;chackdan
ms.openlocfilehash: 0b0ca553fb96b0a54f3b76d306ed98d95026dcd9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Actualización de la aplicación de Service Fabric: temas avanzados
## <a name="adding-or-removing-service-types-during-an-application-upgrade"></a>Adición o eliminación de tipos de servicio durante la actualización de una aplicación
Si se agrega un nuevo tipo de servicio a una aplicación publicada como parte de una actualización, el nuevo tipo de servicio se agrega a la aplicación implementada. Esta actualización no afecta a ninguna de las instancias de servicio que ya formaban parte de la aplicación, pero debe crearse una instancia del tipo de servicio que se ha agregado para que el nuevo tipo de servicio esté activo (consulte [New-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)).

De manera similar, los tipos de servicio pueden quitarse de una aplicación como parte de una actualización. Sin embargo, todas las instancias de servicio del tipo de servicio que va a quitarse se deben quitar antes de continuar con la actualización (consulte [Remove-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)).

## <a name="manual-upgrade-mode"></a>Modo de actualización manual
> [!NOTE]
> El modo de actualización *Monitored* se recomienda para todas las actualizaciones de Service Fabric.
> El modo de actualización *UnmonitoredManual* debe considerarse solo si la actualización presenta errores o si se suspende. 
>
>

En el modo *Monitored*, Service Fabric aplica directivas de mantenimiento para asegurarse de que la aplicación esté en un estado correcto a medida que avance la actualización. Si se infringen las directivas de mantenimiento, la actualización se suspende o se revierte automáticamente según la *FailureAction* especificada.

En modo *UnmonitoredManual*, el administrador de la aplicación tiene un control total sobre el avance de la actualización. Este modo resulta útil al aplicar las directivas de evaluación de mantenimiento personalizadas o realizar actualizaciones no convencional al omitir completamente la supervisión de estado (por ejemplo, la aplicación ya está en pérdida de datos). Una actualización que se ejecuta en este modo se suspenderá después de completar cada UD y se debe reanudar de forma explícita mediante [Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps). Cuando se suspende una actualización y está lista para que el usuario la reanude, su estado de actualización mostrará *RollforwardPending* (consulte [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Por último, el modo *UnmonitoredAuto* es útil para realizar iteraciones rápidas de actualización durante el desarrollo o las pruebas del servicio, ya que no se necesitan entradas del usuario y no se evalúa ninguna directiva de mantenimiento de la aplicación.

## <a name="upgrade-with-a-diff-package"></a>Actualización con un paquete de diferencias
En lugar de aprovisionar un paquete de aplicación completo, las actualizaciones también pueden realizarse mediante el aprovisionamiento de paquetes de diferencias que contienen solo los paquetes actualizados de código, de configuración y de datos, junto con el manifiesto de aplicación completo y los manifiestos de servicio completos. Los paquetes de aplicación completos se requieren únicamente para la instalación inicial de una aplicación en el clúster. Las actualizaciones subsiguientes pueden provenir de paquetes de aplicación completos o de diferencias.  

Toda referencia en el manifiesto de aplicación o los manifiestos de servicio de un paquete de diferencias, que no se encuentre en el paquete de aplicación se reemplazará automáticamente por la versión aprovisionada actualmente.

Los escenarios de uso de un paquete de diferencias son:

* Cuando tiene un paquete de aplicación grande que hace referencia a varios archivos del manifiesto de servicio o varios paquetes de código, de configuración o de datos.
* Cuando dispone de un sistema de implementación que genera el diseño de compilación directamente desde el proceso de compilación de la aplicación. En este caso, aunque el código no ha cambiado, los ensamblados recién creados obtienen una suma de comprobación diferente. Para usar un paquete de aplicación completo, sería necesario que actualizara la versión de todos los paquetes de código. Con un paquete de diferencias, solo proporciona los archivos que cambiaron y los archivos de manifiesto cuya versión ha cambiado.

Cuando se actualiza una aplicación mediante Visual Studio, un paquete de diferencias se publica automáticamente. Para crear manualmente un paquete de diferencias, se deben actualizar el manifiesto de aplicación y los manifiestos de servicio, pero solo los paquetes modificados deben incluirse en el paquete de aplicación final.

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

Supongamos que desea actualizar solo el paquete de código de service1 mediante un paquete de diferencias. La aplicación actualizada tiene los cambios de versión siguientes:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

En este caso, actualice el manifiesto de aplicación a 2.0.0 y el manifiesto de servicio de service1 para que refleje la actualización del paquete de código. La estructura de carpetas para el paquete de aplicación sería similar a la siguiente:

```text
app1/
  service1/
    code/
```

En otras palabras, cree un paquete de aplicación completo normalmente, a continuación, quite las carpetas de paquetes de datos, de configuración y de código para las que no haya cambiado la versión.

## <a name="rolling-back-application-upgrades"></a>Reversión de actualizaciones de aplicaciones

Si bien las actualizaciones se pueden poner al día en uno de tres modos (*Monitored*, *UnmonitoredAuto* o *UnmonitoredManual*), solo se pueden revertir en el modo *UnmonitoredAuto* o *UnmonitoredManual*. La reversión en modo *UnmonitoredAuto* funciona del mismo modo que la puesta al día, con la excepción de que el valor predeterminado de *UpgradeReplicaSetCheckTimeout* es diferente; consulte [Parámetros de actualización de la aplicación](service-fabric-application-upgrade-parameters.md). La reversión en el modo *UnmonitoredManual* funciona del mismo modo que la puesta al día: la reversión se suspenderá después de completar cada UD y se debe reanudar de forma explícita mediante [ Resume-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) para continuar con la reversión.

Las reversiones pueden activarse automáticamente cuando se infringen las directivas de mantenimiento de una actualización en modo *Monitored* con una *FailureAction* de *Rollback* (consulte [Parámetros de actualización de la aplicación](service-fabric-application-upgrade-parameters.md)), o explícitamente con [Start-ServiceFabricApplicationRollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps).

Durante la reversión, el valor de *UpgradeReplicaSetCheckTimeout* y el modo se pueden cambiar en cualquier momento con [Update-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps).

## <a name="next-steps"></a>pasos siguientes
[actualización de aplicaciones usando Visual Studio](service-fabric-application-upgrade-tutorial.md) ofrece información para actualizar una aplicación mediante Visual Studio.

[actualización de aplicaciones mediante PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) se explica en detalle lo que tiene que hacer para actualizar una aplicación mediante PowerShell.

Puede controlar cómo se actualiza una aplicación usando [parámetros de actualización](service-fabric-application-upgrade-parameters.md).

Consiga que sus actualizaciones de aplicaciones sean compatibles aprendiendo a usar la [serialización de datos](service-fabric-application-upgrade-data-serialization.md).

Solucione problemas habituales en las actualizaciones de aplicaciones consultando los pasos que figuran en [Solución de problemas de las actualizaciones de aplicaciones](service-fabric-application-upgrade-troubleshooting.md).
