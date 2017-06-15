---
title: Ciclo de vida de aplicaciones de Service Fabric | Microsoft Docs
description: "Describe el desarrollo, la implementación, las pruebas, la actualización, el mantenimiento y la eliminación de aplicaciones de Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 08837cca-5aa7-40da-b087-2b657224a097
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/14/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: a33d25f3806090307f6fb14dbb821264f2e4f28b
ms.contentlocale: es-es
ms.lasthandoff: 04/27/2017


---
# <a name="service-fabric-application-lifecycle"></a>Ciclo de vida de la aplicación de Service Fabric
Al igual que sucede con otras plataformas, una aplicación en Azure Service Fabric normalmente pasa las siguientes fases: diseño, desarrollo, prueba, implementación, actualización, mantenimiento y eliminación. Service Fabric ofrece compatibilidad de primera clase para todo el ciclo de vida de aplicación de las aplicaciones de nube: desde el desarrollo hasta la implementación, la administración diaria, el mantenimiento y, finalmente, la retirada. El modelo de servicio habilita varios roles distintos para participar de manera independiente en el ciclo de vida de la aplicación. Este artículo proporciona información general de las API y cómo son usadas por los distintos roles durante todas las fases del ciclo de vida de aplicación de Service Fabric.

En el siguiente vídeo de Microsoft Virtual Academy describe cómo administrar el ciclo de vida de la aplicación: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-application-lifecycle/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="service-model-roles"></a>Roles del modelo de servicio
Los roles del modelo de servicio son:

* **Desarrollador del servicio**: desarrolla servicios modulares y genéricos que se pueden volver a definir y usar en varias aplicaciones del mismo tipo o de tipos distintos. Por ejemplo, un servicio de cola puede usarse para crear una aplicación de incidencias (departamento de soporte técnico) y una aplicación de comercio electrónico (carro de la compra).
* **Desarrollador de la aplicación**: crea aplicaciones mediante la integración de una recopilación de servicios para satisfacer ciertos escenarios o requisitos específicos. Por ejemplo, un sitio web de comercio electrónico podría integrar "Servicio front-end sin estado JSON", "Servicio con estado de subasta" y "Servicio con estado de cola" para crear una solución de subasta.
* **Administrador de aplicaciones**: toma decisiones sobre la configuración de las aplicaciones (relleno de los parámetros de la plantilla de configuración), implementación (asignación a recursos disponibles) y calidad de servicio. Por ejemplo, un administrador de aplicaciones decide la configuración regional de idioma (por ejemplo, inglés para Estados Unidos o japonés para Japón) de la aplicación. Una aplicación implementada diferente puede tener configuraciones distintas.
* <seg>
  **Operador**: implementa aplicaciones basadas en la configuración de la aplicación y los requisitos especificados por el administrador de aplicaciones..</seg> Por ejemplo, un operador aprovisiona e implementa la aplicación y garantiza que se ejecuta en Azure. Los operadores supervisan la información de rendimiento y estado de la aplicación y mantiene la infraestructura física, según corresponda.

## <a name="develop"></a>Desarrollo
1. Un *desarrollador de servicios* desarrolla los distintos tipos de servicios con el modelo de programación [Reliable Actors](service-fabric-reliable-actors-introduction.md) o [Reliable Services](service-fabric-reliable-services-introduction.md).
2. Un *desarrollar de servicio* describe, mediante declaración, los tipos de servicio desarrollados en un archivo de manifiesto de servicio que consta de uno o más paquetes de código, configuración y datos.
3. Un *desarrollador de aplicaciones* luego crea una aplicación con distintos tipos de servicio.
4. Un *desarrollador de aplicaciones* describe, mediante declaración, el tipo de aplicación en un manifiesto de aplicación a través de la referencia a los manifiestos de servicio de los servicios constituyentes y anulan y parametrizan de manera adecuada las distintas configuraciones de ajuste e implementación de los servicios constituyentes.

Consulte [Introducción a Reliable Actors](service-fabric-reliable-actors-get-started.md) e [Introducción a Reliable Services](service-fabric-reliable-services-quick-start.md) para ver ejemplos.

## <a name="deploy"></a>Implementación
1. Un *administrador de aplicaciones* adapta el tipo de aplicación a una aplicación específica para su implementación en un clúster de Service Fabric mediante la especificación de los parámetros adecuados del elemento **ApplicationType** en el manifiesto de aplicación.
2. Un *operador* carga el paquete de aplicación en el almacén de imágenes del clúster mediante el método [**CopyApplicationPackage** o el cmdlet ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CopyApplicationPackage_System_String_System_String_System_String_)[**Copy-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). El paquete de aplicación contiene el manifiesto de aplicación y la recopilación de los paquetes de servicio. Service Fabric implementa aplicaciones desde el paquete de aplicación almacenado en ImageStore, que puede ser un almacenamiento de blobs de Azure o el servicio de sistema de Service Fabric.
3. Luego el *operador* aprovisiona el tipo de aplicación en el clúster de destino desde el paquete de aplicación cargado mediante el método [**ProvisionApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_ProvisionApplicationAsync_System_String_System_TimeSpan_System_Threading_CancellationToken_), el cmdlet [**Register-ServiceFabricApplicationType**](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) o la operación de REST [**Aprovisionar una aplicación**](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
4. Después de realizar el aprovisionamiento de la aplicación, un *operador* inicia la aplicación con los parámetros que suministra el *administrador de aplicaciones* mediante el método [**CreateApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CreateApplicationAsync_System_Fabric_Description_ApplicationDescription_System_TimeSpan_System_Threading_CancellationToken_), el cmdlet [**New-ServiceFabricApplication**](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricapplication)[ **o la operación de REST** Crear aplicación](https://docs.microsoft.com/rest/api/servicefabric/create-an-application).
5. Una vez implementada la aplicación, un *operador* usa el método [**CreateServiceAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient#System_Fabric_FabricClient_ServiceManagementClient_CreateServiceAsync_System_Fabric_Description_ServiceDescription_System_TimeSpan_System_Threading_CancellationToken_), el cmdlet [**New-ServiceFabricService**](https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricservice) o la operación de REST [**Crear servicio**](https://docs.microsoft.com/rest/api/servicefabric/create-a-service) para crear nuevas instancias de servicio de la aplicación basándose en tipos de servicio disponibles.
6. La aplicación ahora se ejecuta en el clúster de Service Fabric.

Consulte [Implementación de una aplicación](service-fabric-deploy-remove-applications.md) para ver ejemplos.

## <a name="test"></a>Prueba
1. Después de realizar la implementación en el clúster de desarrollo local o en un clúster de prueba, un *desarrollador de servicio* ejecuta el escenario de prueba de conmutación por error integrado mediante las clases [**FailoverTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters#System_Fabric_Testability_Scenario_FailoverTestScenarioParameters) y [**FailoverTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario#System_Fabric_Testability_Scenario_FailoverTestScenario) o el cmdlet [**Invoke-ServiceFabricFailoverTestScenario**](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps). El escenario de prueba de conmutación por error ejecuta un servicio especificado a través de importantes transiciones y conmutadores por error para asegurarse de que sigue disponible y en funcionamiento.
2. El *desarrollador de servicio* luego ejecuta el escenario de prueba de caos integrado mediante el uso de las clases [**ChaosTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters#System_Fabric_Testability_Scenario_ChaosTestScenarioParameters) y [**ChaosTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario#System_Fabric_Testability_Scenario_ChaosTestScenario) o el cmdlet [**Invoke-ServiceFabricChaosTestScenario**](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps). El escenario de prueba de caos induce de manera aleatoria varios errores de nodo, paquete de código y réplica en el clúster.
3. El *desarrollador del servicio* [comprueba la comunicación del servicio a servicio](service-fabric-testability-scenarios-service-communication.md) mediante la creación de escenarios de prueba que mueven las réplicas principales en torno al clúster.

Consulte [Introducción al servicio de análisis de errores](service-fabric-testability-overview.md) para obtener más información.

## <a name="upgrade"></a>Actualizar
1. Un *desarrollador de servicio* actualiza los servicios constituyentes de la aplicación con instancias y/o corrige errores y proporciona una versión nueva del manifiesto de servicio.
2. Un *desarrollador de aplicaciones* anula y parametrizan las configuraciones de ajuste e implementación de los servicios constituyentes y proporciona una versión nueva del manifiesto de aplicación. A continuación, el desarrollador de aplicaciones incorpora las versiones nuevas de los manifiestos de servicio a la aplicación y proporciona una versión nueva del tipo de aplicación en un paquete de aplicación actualizado.
3. Un *administrador de aplicaciones* incorpora la versión nueva del tipo de aplicación a la aplicación de destino mediante la actualización de los parámetros adecuados.
4. Un *operador* carga el paquete de aplicación actualizado en el almacén de imágenes del clúster mediante el método [**CopyApplicationPackage**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_CopyApplicationPackage_System_String_System_String_System_String_) o el cmdlet [**Copy-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). El paquete de aplicación contiene el manifiesto de aplicación y la recopilación de los paquetes de servicio.
5. Un *operador* aprovisiona la versión nueva de la aplicación en el clúster de destino mediante el método [**ProvisionApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_ProvisionApplicationAsync_System_String_System_TimeSpan_System_Threading_CancellationToken_), el cmdlet [**Register-ServiceFabricApplicationType** o la operación de REST ](https://docs.microsoft.com/powershell/servicefabric/vlatest/register-servicefabricapplicationtype)[**Aprovisionar una aplicación**](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application).
6. Un *operador* actualiza la aplicación de destino a la versión nueva mediante el método [**UpgradeApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UpgradeApplicationAsync_System_Fabric_Description_ApplicationUpgradeDescription_System_TimeSpan_System_Threading_CancellationToken_), el cmdlet [**Start-ServiceFabricApplicationUpgrade**](https://docs.microsoft.com/powershell/servicefabric/vlatest/start-servicefabricapplicationupgrade) o la operación de REST [**Actualizar una aplicación**](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application).
7. Un *operador* controla el progreso de la actualización mediante el método [**GetApplicationUpgradeProgressAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_GetApplicationUpgradeProgressAsync_System_Uri_System_TimeSpan_System_Threading_CancellationToken_), el cmdlet [**Get-ServiceFabricApplicationUpgrade** o la operación de REST ](https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade)[**Obtener progreso de la actualización de la aplicación**](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. En caso de ser necesario, el *operador* modifica y vuelve a aplicar los parámetros de la actualización de la aplicación actual mediante el método [**UpdateApplicationUpgradeAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UpdateApplicationUpgradeAsync_System_Fabric_Description_ApplicationUpgradeUpdateDescription_System_TimeSpan_System_Threading_CancellationToken_), el cmdlet [**Update-ServiceFabricApplicationUpgrade**](https://docs.microsoft.com/powershell/servicefabric/vlatest/update-servicefabricapplicationupgrade) o la operación de REST [**Actualizar la actualización de la aplicación**](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade).
9. En caso de ser necesario, el *operador* revierte la actualización de la aplicación actual mediante el método [**RollbackApplicationUpgradeAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_RollbackApplicationUpgradeAsync_System_Uri_System_TimeSpan_System_Threading_CancellationToken_), el cmdlet [**Start-ServiceFabricApplicationRollback** o la operación de REST ](https://docs.microsoft.com/powershell/servicefabric/vlatest/start-servicefabricapplicationrollback)[**Revertir actualización de la aplicación**](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade).
10. Service Fabric actualiza la aplicación de destino que se ejecuta en el clúster sin perder la disponibilidad de ninguno de sus servicios constituyentes.

Consulte [Tutorial de actualización de la aplicación](service-fabric-application-upgrade-tutorial.md) para obtener ejemplos.

## <a name="maintain"></a>Mantenimiento
1. Para obtener actualizaciones y revisiones del sistema operativo, Service Fabric interactúa con la infraestructura de Azure para garantizar la disponibilidad de todas las aplicaciones que se ejecutan en el clúster.
2. En el caso de las actualizaciones y revisiones de la plataforma de Service Fabric, Service Fabric realiza actualizaciones sin perder la disponibilidad de ninguna de las aplicaciones que se ejecutan en el clúster.
3. Un *administrador de aplicaciones* aprueba la adición o eliminación de nodos de un clúster después de analizar los datos históricos de utilización de capacidad y la demanda futura prevista.
4. Un *operador* agrega y quita nodos especificados por el *administrador de aplicaciones*.
5. Cuando se agregan nodos nuevos al clúster o se quitan nodos existentes, Service Fabric equilibra automáticamente la carga de las aplicaciones en ejecución en todos los nodos del clúster para lograr un rendimiento óptimo.

## <a name="remove"></a>Remove
1. Un *operador* puede eliminar una instancia específica de un servicio en ejecución en el clúster sin quitar la aplicación completa mediante el método [**DeleteServiceAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient#System_Fabric_FabricClient_ServiceManagementClient_DeleteServiceAsync_System_Fabric_Description_DeleteServiceDescription_System_TimeSpan_System_Threading_CancellationToken_), el cmdlet [**Remove-ServiceFabricService**](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricservice) o la operación de REST [**Eliminar servicio**](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service).  
2. Un *operador* también puede eliminar una instancia de aplicación y todos sus servicios mediante el método [**DeleteApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_DeleteApplicationAsync_System_Fabric_Description_DeleteApplicationDescription_System_TimeSpan_System_Threading_CancellationToken_), el cmdlet [**Remove-ServiceFabricApplication**](https://docs.microsoft.com/powershell/servicefabric/vlatest/remove-servicefabricapplication) o la operación de REST [**Eliminar aplicación**](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application).
3. Una vez detenidos los servicios y la aplicación, el *operador* puede deshacer el aprovisionamiento del tipo de aplicación mediante el método [**UnprovisionApplicationAsync**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_UnprovisionApplicationAsync_System_String_System_String_System_TimeSpan_System_Threading_CancellationToken_), el cmdlet [**Unregister-ServiceFabricApplicationType**](https://docs.microsoft.com/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype) o la operación de REST [**Deshacer aprovisionamiento de una aplicación**](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application). Deshacer el aprovisionamiento del tipo de aplicación no quita el paquete de aplicación del ImageStore. Debe quitar el paquete de aplicación manualmente.
4. Un *operador* quita el paquete de aplicación de ImageStore mediante el método [**RemoveApplicationPackage**](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient#System_Fabric_FabricClient_ApplicationManagementClient_RemoveApplicationPackage_System_String_System_String_) o el cmdlet [**Remove-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps).

Consulte [Implementación de una aplicación](service-fabric-deploy-remove-applications.md) para ver ejemplos.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre cómo desarrollar, probar y administrar aplicaciones y servicios de Service Fabric, consulte:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Implementar una aplicación](service-fabric-deploy-remove-applications.md)
* [Actualización de aplicaciones](service-fabric-application-upgrade.md)
* [Información general sobre Testability](service-fabric-testability-overview.md)

