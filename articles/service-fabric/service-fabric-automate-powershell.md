---
title: "Automatización de la administración de aplicaciones de Azure Service Fabric | Microsoft Docs"
description: "Implementación, actualización, prueba y eliminación de aplicaciones de Service Fabric con PowerShell."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: f03f4294-571d-4262-8a77-cc8b481b959d
ms.service: service-fabric
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 59133d11d2d66daa44f28ef5b9aa3aae92e56a1e
ms.lasthandoff: 03/15/2017


---
# <a name="automate-the-application-lifecycle-using-powershell"></a>Automatización del ciclo de vida de las aplicaciones con PowerShell
Muchos aspectos del [ciclo de vida de la aplicación de Service Fabric](service-fabric-application-lifecycle.md) pueden automatizarse.  En este artículo se muestra cómo usar PowerShell para automatizar las tareas comunes para implementar, actualizar, quitar y probar aplicaciones de Service Fabric.  También cuenta con API HTTP y administradas para la administración de aplicaciones. Consulte el [ciclo de vida de las aplicaciones](service-fabric-application-lifecycle.md) para más información.  

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar con las tareas del artículo, no olvide de hacer lo siguiente:

* Familiarícese con los conceptos de Service Fabric descritos en [Introducción técnica a Service Fabric](service-fabric-technical-overview.md).
* [Instale el motor en tiempo de ejecución, el SDK y las herramientas](service-fabric-get-started.md), lo que también instala el módulo PowerShell de **ServiceFabric** .
* [Habilite la ejecución del script de PowerShell](service-fabric-get-started.md#enable-powershell-script-execution).
* Inicie un clúster local.  Abra una nueva ventana de PowerShell como administrador y después ejecute el script de configuración de clúster desde la carpeta del SDK: `& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"`
* Antes de ejecutar los comandos de PowerShell en este artículo, conéctese al clúster de Service Fabric local mediante [Connect-ServiceFabricCluster](/powershell/servicefabric/vlatest/connect-servicefabriccluster): `Connect-ServiceFabricCluster localhost:19000`
* Las tareas siguientes requieren un paquete de aplicación v1 para la implementación y uno v2 para la actualización. Descargue la aplicación de ejemplo [WordCount****](http://aka.ms/servicefabricsamples) (que se encuentra en los ejemplos de introducción). Compile y empaquete la aplicación en Visual Studio (haga clic con el botón derecho en **WordCount** en el Explorador de soluciones y seleccione **Paquete**). Copie el paquete v1 de `C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug` a `C:\Temp\WordCount`. Copie `C:\Temp\WordCount` a `C:\Temp\WordCountV2`, lo que crea el paquete de aplicación v2 para la actualización. Abra `C:\Temp\WordCountV2\ApplicationManifest.xml` en un editor de texto. En el elemento **ApplicationManifest**, cambie el atributo **ApplicationTypeVersion** de "1.0.0" a "2.0.0" para actualizar el número de versión de la aplicación. Guarde el archivo ApplicationManifest.xml modificado.

## <a name="task-deploy-a-service-fabric-application"></a>Tarea: Implementar una aplicación de Service Fabric
Una vez que compile y empaquete la aplicación (o descargue el paquete de aplicación), puede implementar esta aplicación en un clúster de Service Fabric local. La implementación significa cargar el paquete de aplicación, registrar el tipo de aplicación y crear la instancia de la aplicación. Use las instrucciones que aparecen en esta sección para implementar una aplicación nueva en un clúster.

### <a name="step-1-upload-the-application-package"></a>Paso 1: cargar el paquete de aplicación
Cargar el paquete de aplicación en el almacén de imágenes lo pone en una ubicación a la que pueden tener acceso los componentes internos de Service Fabric.  El paquete de aplicación contiene el manifiesto de aplicación, los manifiestos de servicio y los paquetes de código, configuración y datos necesarios para crear las instancias de servicio y aplicación. Si quiere comprobar el paquete de la aplicación de forma local, use el cmdlet [ServiceFabricApplicationPackage prueba](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage).  El comando [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) carga el paquete. Por ejemplo:

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCount\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### <a name="step-2-register-the-application-type"></a>Paso 2: registrar el tipo de aplicación
El registro del paquete de aplicación hace que la versión y el tipo de la aplicación declarados en el manifiesto de aplicación esté disponible para su uso. El sistema lee el paquete cargado en el paso 1, lo comprueba (lo que equivale a ejecutar [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) localmente), procesa su contenido y copia el paquete procesado en una ubicación interna del sistema.  Ejecute el cmdlet [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype):

```powershell
Register-ServiceFabricApplicationType WordCount
```
Para ver todos los tipos de aplicación registrados en el clúster, ejecute el cmdlet [Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype):

```powershell
Get-ServiceFabricApplicationType
```

### <a name="step-3-create-the-application-instance"></a>Paso 3: crear la instancia de aplicación
Se pueden crear instancias de una aplicación mediante cualquier versión del tipo de aplicación que se ha registrado correctamente mediante el comando [New-ServiceFabricApplication](/powershell/servicefabric/vlatest/new-servicefabricapplication). El nombre de cada aplicación se declara en el momento de la implementación; debe empezar con el esquema **fabric:** y ser único para cada instancia de la aplicación. El nombre y la versión del tipo de aplicación se declaran en el archivo **ApplicationManifest.xml** del paquete de aplicación. Si se definieron servicios predeterminados en el manifiesto de aplicación del tipo de aplicación de destino, también se crean en este momento.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

El comando [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) enumera todas las instancias de aplicación que se crearon correctamente junto con su estado general. El comando [Get-ServiceFabricService](/powershell/servicefabric/vlatest/get-servicefabricservice) enumera todas las instancias de servicio que se crearon correctamente dentro de una instancia de aplicación determinada. Se enumeran los servicios predeterminados (en caso de haberlos).

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## <a name="task-upgrade-a-service-fabric-application"></a>Tarea: Actualizar una aplicación de Service Fabric
Puede actualizar una aplicación de Service Fabric ya implementada con un paquete de aplicación actualizado. Esta tarea actualiza la aplicación WordCount que se implementó en "Tarea: Implementar una aplicación de Service Fabric". Lea el [Tutorial de actualización de aplicación de Service Fabric](service-fabric-application-upgrade.md) para obtener más información.

Para simplificar este ejemplo, solo se actualizó el número de versión de aplicación en el paquete de aplicación WordCountV2 creado en los requisitos previos. En un escenario más realista, se actualizarían los archivos de código, configuración o datos del servicio y después se volvería a generar y se empaquetaría la aplicación con los números de la versión actualizada.  

### <a name="step-1-upload-the-updated-application-package"></a>Paso 1: Carga del paquete de aplicación actualizado
La aplicación WordCount v1 está lista para actualizarse. Si abre una ventana de PowerShell como administrador y escribe [**Get-ServiceFabricApplication**](/powershell/servicefabric/vlatest/get-servicefabricapplication), verá que está implementando la versión 1.0.0 del tipo de aplicación de WordCount.  

Ahora, copie el paquete de aplicación actualizada en el almacén de imágenes de Service Fabric (donde Service Fabric almacena los paquetes de aplicación). El parámetro **ApplicationPackagePathInImageStore** informa a Service Fabric sobre dónde puede encontrar el paquete de la aplicación. El siguiente comando copiará el paquete de aplicación en **WordCountV2** en el almacén de imágenes:  

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCountV2\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2

```
### <a name="step-2-register-the-updated-application-type"></a>Paso 2: Registro del tipo de aplicación actualizado
El siguiente paso es registrar la versión nueva de la aplicación con Service Fabric, que se puede realizar con el cmdlet [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype):

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

### <a name="step-3-start-the-upgrade"></a>Paso 3: inicie la actualización
Varios parámetros de actualización, tiempos de expiración y los criterios de estado se pueden aplicar a las actualizaciones de la aplicación. Consulte los [parámetros de actualización de la aplicación](service-fabric-application-upgrade-parameters.md) y los documentos del [proceso de actualización](service-fabric-application-upgrade.md) para más información. El estado de todos los servicios y las instancias debe ser *correcto* después de la actualización.  Establezca el valor de **HealthCheckStableDuration** en 60 segundos (para que los servicios conserven el estado correcto al menos 20 segundos antes de que la actualización pase al siguiente dominio de actualización).  Establezca también **UpgradeDomainTimeout** en 1200 segundos y **UpgradeTimeout** en 3000 segundos. Por último, establezca **UpgradeFailureAction** en **revertir**, que solicita a Service Fabric revertir a aplicación a la versión anterior si se encuentran errores durante la actualización.

Ahora puede iniciar la actualización de la aplicación con el cmdlet [Start-ServiceFabricApplicationUpgrade](/powershell/servicefabric/vlatest/start-servicefabricapplicationupgrade):

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Tenga en cuenta que el nombre de la aplicación es el mismo nombre de la aplicación v1.0.0 implementada anteriormente (fabric:/WordCount). Service Fabric usa este nombre para identificar qué aplicación se está actualizando. Si los tiempos de espera que estableció son demasiado breves, es probable que reciba un mensaje de error de tiempo de espera que indica el problema. Consulte [Solución de problemas de actualizaciones de aplicaciones](service-fabric-application-upgrade-troubleshooting.md)o aumente los tiempos de expiración.

### <a name="step-4-check-upgrade-progress"></a>Paso 4: Comprobación del progreso de la actualización
Puede supervisar el progreso de la actualización de la aplicación mediante [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) o con el cmdlet [Get-ServiceFabricApplicationUpgrade](/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade):

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

En unos minutos, el cmdlet [Get-ServiceFabricApplicationUpgrade](/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade) muestra que se actualizaron (completaron) todos los dominios de actualización.

## <a name="task-test-a-service-fabric-application"></a>Tarea: Probar una aplicación de Service Fabric
Para poder escribir servicios de alta calidad, los desarrolladores deben poder inducir errores en infraestructuras no confiables para probar la estabilidad de los servicios. Service Fabric brinda a los desarrolladores la capacidad de inducir acciones de error y probar los servicios en caso de errores mediante el uso de escenarios de prueba en caso de caos y conmutación por error.  Consulte [Introducción al servicio de análisis de errores](service-fabric-testability-overview.md) para más información.

### <a name="step-1-run-the-chaos-test-scenario"></a>Paso 1: ejecute el escenario de prueba de caos
El escenario de caos genera errores en todo el clúster de Service Fabric. El escenario comprime los errores que se ven por lo general durante meses o años en unas pocas horas. Esta combinación de errores intercalados con una elevada tasa de errores encuentra casos excepcionales que de otra manera pasan desapercibidos. El ejemplo siguiente ejecuta el escenario de prueba de caos durante 60 segundos.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### <a name="step-2-run-the-failover-test-scenario"></a>Paso 2: ejecute el escenario de prueba de conmutación por error
El escenario de prueba de conmutación por error tiene como destino una partición específica del servicio, en lugar de todo el clúster, y no afecta los demás servicios. El escenario se itera por una secuencia de errores simulados en la validación de servicios mientras se ejecuta la lógica de negocios. Un error en la validación de servicio indica un problema que necesita más investigación. La prueba de conmutación por error solo induce un error a la vez, en oposición a lo que ocurre con el escenario de prueba de caos, el que puede inducir varios errores. En el ejemplo siguiente se ejecuta la prueba de conmutación por error durante 60 minutos en el servicio fabric:/WordCount/WordCountService.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## <a name="task-remove-a-service-fabric-application"></a>Tarea: Quitar una aplicación de Service Fabric
Puede eliminar una instancia de una aplicación implementada, quitar el tipo de aplicación aprovisionada del clúster y quitar el paquete de aplicación del almacén de imágenes.

### <a name="step-1-remove-an-application-instance"></a>Paso 1: quite una instancia de aplicación
Cuando ya no se necesite una instancia de aplicación, use el cmdlet [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication) para quitarla de manera permanente. De esta manera también se quitarán automáticamente todos los servicios que pertenecen a la aplicación, eliminando de forma permanente todos los estados de servicio. No se puede deshacer esta operación y no se puede recuperar el estado de la aplicación.

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### <a name="step-2-unregister-the-application-type"></a>Paso 2: anule el registro del tipo de aplicación
Cuando ya no se necesite una versión determinada de un tipo de aplicación, use el cmdlet [Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype) para anular el registro. Anular el registro de tipos no usados libera espacio de almacenamiento que usa el paquete de aplicación en el almacén de imágenes. No se puede anular el registro de un tipo de aplicación ya que no hay ninguna aplicación con instancias con él o hay actualizaciones de aplicaciones pendientes que hacen referencia a él.

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### <a name="step-3-remove-the-application-package"></a>Paso 3: quite el paquete de aplicación
Una vez que se anula el registro del tipo de aplicación, es posible utilizar el cmdlet [Remove-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/remove-servicefabricapplicationpackage) para quitar el paquete de aplicación del almacén de imágenes.

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## <a name="next-steps"></a>Pasos siguientes
[Ciclo de vida de la aplicación de Service Fabric](service-fabric-application-lifecycle.md)

[Implementar una aplicación](service-fabric-deploy-remove-applications.md)

[Actualización de aplicaciones](service-fabric-application-upgrade.md)

[Cmdlets de Azure Service Fabric](/powershell/servicefabric/vlatest/servicefabric)


