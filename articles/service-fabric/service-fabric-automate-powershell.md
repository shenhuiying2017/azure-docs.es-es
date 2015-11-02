<properties
	pageTitle="Automatizar la administración de aplicaciones de Service Fabric con PowerShell | Microsoft Azure"
	description="Implemente, actualice, pruebe y quite aplicaciones de Service Fabric con PowerShell."
	services="service-fabric"
	documentationCenter=".net"
	authors="rwike77"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-fabric"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="10/16/2015"
	ms.author="ryanwi"/>

# Implementación, actualización, prueba y eliminación de aplicaciones de Service Fabric con PowerShell

En este artículo se muestra cómo usar PowerShell para automatizar las tareas comunes para implementar, actualizar, quitar y probar aplicaciones de Service Fabric.

## Requisitos previos

Antes de pasar a las tareas que aparecen en el artículo, asegúrese de [Instalar el tiempo de ejecución, el SDK y las herramientas](service-fabric-get-started.md) que también instalan los módulos ServiceFabric y ServiceFabricTestability de PowerShell. [Habilite la ejecución de scripts de PowerShell](service-fabric-get-started.md#enable-powershell-script-execution) y, además, [instale e inicie un clúster local](service-fabric-get-started.md#install-and-start-a-local-cluster) para poder ejecutar los ejemplos que aparecen en el artículo.

Los ejemplos de este artículo usan la [aplicación de ejemplo HelloWorldStateful](https://github.com/Azure/servicefabric-samples/tree/master/samples/Services/VS2015/HelloWorldStateful). Descargue y compile la aplicación de ejemplo.

Antes de ejecutar los comandos de PowerShell en este artículo, primero conéctese al clúster de Service Fabric local mediante [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/azure/mt125938.aspx):

```powershell
Connect-ServiceFabricCluster
```

## TAREA: Implementar una aplicación de Service Fabric

Una vez que compile la aplicación y que se empaquete el tipo de aplicación, puede implementar esta aplicación en un clúster de Service Fabric. En primer lugar, empaquete la aplicación HelloWorldStateful en Visual Studio. Para ello, haga clic con el botón derecho en **HelloWorldStatefulApplication** en el Explorador de soluciones y seleccione **Empaquetar**. Consulte [Modelar una aplicación en Service Fabric](service-fabric-application-model.md) para obtener información sobre los manifiestos del servicio y la aplicación y el diseño del paquete. La implementación significa cargar el paquete de aplicación, registrar el tipo de aplicación y crear la instancia de la aplicación. Use las instrucciones que aparecen en esta sección para implementar una aplicación nueva en un clúster.

### Paso 1: cargar el paquete de aplicación
Cargar el paquete de aplicación en el almacén de imágenes lo pone en una ubicación a la que pueden tener acceso los componentes internos de Service Fabric. El paquete de aplicación contiene el manifiesto de aplicación, los manifiestos de servicio y los paquetes de código/configuración/datos necesarios para crear las instancias de servicio y aplicación. El comando [Copy-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/azure/mt125905.aspx) cargará el paquete. Por ejemplo:

```powershell
Copy-ServiceFabricApplicationPackage C:\ServiceFabricSamples\Services\VS2015\HelloWorldStateful\HelloWorldStatefulApplication\pkg\Debug -ImageStoreConnectionString fabric:ImageStore -ApplicationPackagePathInImageStore HelloWorldStateful
```

### Paso 2: registrar el tipo de aplicación
El registro del paquete de aplicación hace que la versión y el tipo de la aplicación declarados en el manifiesto de aplicación esté disponible para su uso. El sistema leerá el paquete cargado en el paso anterior, comprobará el paquete (equivalente a ejecutar [Test-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/azure/mt125950.aspx) localmente), procesará el contenido del paquete y copiará el paquete procesado en una ubicación del sistema interno. Ejecute el cmdlet [Register-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125958.aspx):

```powershell
Register-ServiceFabricApplicationType HelloWorldStateful
```
Para ver los tipos de aplicación registrados en el clúster, ejecute el cmdlet:

```powershell
Get-ServiceFabricApplicationType
```

### Paso 3: crear la instancia de aplicación
Se puede crear instancias de una aplicación mediante cualquier versión del tipo de aplicación que se ha registrado correctamente mediante el comando [New-ServiceFabricApplication](https://msdn.microsoft.com/library/azure/mt125913.aspx). El nombre de cada aplicación debe empezar con el esquema **fabric:** y ser únicos para cada instancia de la aplicación. El nombre y la versión del tipo de aplicación se declaran en el archivo ApplicationManifest.xml. Si había servicios predeterminados definidos en el manifiesto de aplicación del tipo de aplicación de destino, también se crearán en este momento.

```powershell
New-ServiceFabricApplication fabric:/HelloWorldStatefulApplication HelloWorldStatefulApplication 1.0.0.0
```

El comando [ServiceFabricApplication Get](https://msdn.microsoft.com/library/azure/mt163515.aspx) enumera todas las instancias de las aplicaciones que se crearon correctamente junto con su estado general. El comando [ServiceFabricService Get](https://msdn.microsoft.com/library/azure/mt125889.aspx) enumera todas las instancias de servicio que se crearon correctamente dentro de una instancia de aplicación determinada. Se enumerarán los servicios predeterminados (si los hay).

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## TAREA: Actualizar una aplicación de Service Fabric

Puede actualizar una aplicación de Service Fabric implementada anteriormente. Esta tarea actualiza la aplicación HelloWorldStateful que se implementó en TAREA: Implementar una aplicación de Service Fabric. Consulte [Actualización de la aplicación](service-fabric-application-upgrade.md) para obtener información adicional.

### Paso 1: actualice la aplicación

Realizar cambios en el código del servicio HelloWorldStateful.

Después de actualizar el código del servicio, deberá aumentar el número de versión del servicio en el archivo ServiceManifest.xml (ubicado en el directorio PackageRoot del proyecto HelloWorldStateful). Encuentre el elemento **CodePackage** del manifiesto y cambie la versión del servicio a 2.0.0.0. Las líneas correspondientes en el archivo ServiceManifest.xml deben tener el aspecto siguiente:

```xml
<ServiceManifest Name="HelloWorldStatefulPkg"
                 Version="2.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0.0.0">
```

Ahora, deberá actualizar el archivo ApplicationManifest.xml (que se encuentra en el proyecto HelloWorldStatefulApplication bajo la solución HelloWorldStateful). Actualice el elemento **ServiceManifestRef** para usar la versión 2.0.0.0 del proyecto HelloWorldStatefulPkg. Actualice también **ApplicationTypeVersion** de 2.0.0.0 a 1.0.0.0. Las líneas correspondientes en el archivo ApplicationManifest.xml deben tener el aspecto siguiente:

```xml
<ApplicationManifest ApplicationTypeName="HelloWorldStatefulApplication" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<ServiceManifestRef ServiceManifestName="HelloWorldStatefulPkg" ServiceManifestVersion="2.0.0.0" />
```

Después de realizar estos cambios, guarde los archivos y recompile el proyecto HelloWorldStateful. Ahora empaquete la aplicación actualizada. Para ello, haga clic con el botón derecho en el proyecto HelloWorldStatefulApplication, seleccione el menú de Service Fabric y elija Empaquetar. Así se debe crear un paquete de aplicación que se puede implementar. Su aplicación actualizada está lista para implementarse ahora.

### Paso 2: copie y registre el paquete de aplicación actualizada

La aplicación ahora está compilada, empaquetada y lista para su actualización. Si abre una ventana de PowerShell como administrador y escribe [Get-ServiceFabricApplication](https://msdn.microsoft.com/library/azure/mt163515.aspx), debe ver que está implementado el tipo de aplicación 1.0.0.0 de **HelloWorldStatefulApplication**. En el caso del ejemplo de HelloWorldStateful, el paquete de aplicación se encuentra en: *C:\\ServiceFabricSamples\\Services\\VS2015\\HelloWorldStateful\\HelloWorldStatefulApplication\\pkg\\Debug*.

Ahora, copie el paquete de aplicación actualizada en el almacén de imágenes de Service Fabric (donde Service Fabric almacena los paquetes de aplicación). El parámetro *ApplicationPackagePathInImageStore* informa a Service Fabric sobre dónde puede encontrar el paquete de la aplicación. El siguiente comando copiará el paquete de aplicación a *HelloWorldStatefulV2* en el almacén de imágenes:

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath C:\ServiceFabricSamples\Services\VS2015\HelloWorldStateful\HelloWorldStatefulApplication\pkg\Debug -ImageStoreConnectionString fabric:ImageStore -ApplicationPackagePathInImageStore HelloWorldStatefulV2
```

El siguiente paso es registrar la versión nueva de la aplicación con Service Fabric, que se puede realizar con el cmdlet [Register-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125958.aspx):

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore HelloWorldStatefulV2
```

Si este comando no se completa correctamente, es posible que deba recompilar el servicio, tal como se mencionó en el paso 1.

### Paso 3: inicie la actualización
Varios parámetros de actualización, tiempos de expiración y el criterio de estado se pueden aplicar a las actualizaciones de la aplicación. Consulte los [parámetros de actualización de la aplicación](service-fabric-application-upgrade-parameters.md) y el [proceso de actualización](service-fabric-application-upgrade.md) para obtener más información. Para los fines de este tutorial, deje el criterio de evaluación del estado del servicio definido en el valor predeterminado (y los valores recomendados). El estado de todos los servicios y las instancias debe ser _correcto_ después de la actualización. Sin embargo, aumente el valor de *HealthCheckStableDuration* a 60 segundos (para que los servicios conserven el estado correcto por, al menos, 20 segundos antes de que la actualización pase al siguiente dominio de actualización). Establezca también *UpgradeDomainTimeout* en 1200 segundos y *UpgradeTimeout* en 3000 segundos. Por último, establezca *UpgradeFailureAction* en revertir, el que solicita a Service Fabric revertir a la aplicación a la versión anterior si se encuentran errores durante la actualización.

Ahora puede iniciar la actualización de la aplicación con el cmdlet [Start-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125975.aspx):

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/HelloWorldStatefulApplication -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Observe que el nombre de la aplicación es el mismo nombre de la aplicación v.1.0.0.0 implementada anteriormente (fabric:/HelloWorldStatefulApplication). Service Fabric usa este nombre para identificar qué aplicación se está actualizando. Si los tiempos de expiración que estableció son demasiado breves, es probable que reciba un mensaje de error de tiempo de expiración que indica el problema. Consulte [Solución de problemas de actualizaciones de aplicaciones](service-fabric-application-upgrade-troubleshooting.md) o aumente los tiempos de expiración.

Puede supervisar el progreso de la actualización de la aplicación mediante el [Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md) o el cmdlet [Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx):

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/HelloWorldStatefulApplication
```

En unos minutos, el cmdlet [Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) debería mostrar que se actualizaron (completaron) todos los dominios de actualización.

## TAREA: Probar una aplicación de Service Fabric

Para poder escribir servicios de alta calidad, los desarrolladores deben poder inducir errores en infraestructuras no confiables para probar la estabilidad de los servicios. Service Fabric brinda a los desarrolladores la capacidad de inducir acciones de error y probar los servicios en caso de errores mediante el uso de escenarios de prueba en caso de caos y conmutación por error. Consulte [Información general sobre Testability](service-fabric-testability-overview.md) para obtener información adicional.

### Paso 1: ejecute el escenario de prueba de caos
El escenario de caos genera errores en todo el clúster de Service Fabric. El escenario comprime los errores que se ven por lo general durante meses o años en unas pocas horas. Esta combinación de errores intercalados con una elevada tasa de errores encuentra casos excepcionales que de otra manera pasan desapercibidos. El ejemplo siguiente ejecuta el escenario de prueba de caos durante 60 segundos.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### Paso 2: ejecute el escenario de prueba de conmutación por error
El escenario de prueba de conmutación por error tiene como destino una partición específica del servicio, en lugar de todo el clúster, y no afecta los demás servicios. El escenario se itera por una secuencia de errores simulados y una validación de servicios mientras se ejecuta la lógica de negocios. Un error en la validación de servicio indica un problema que necesita más investigación. La prueba de conmutación por error solo induce un error a la vez, en oposición a lo que ocurre con el escenario de prueba de caos, el que puede inducir varios errores. En el ejemplo siguiente, la prueba de conmutación por error se ejecuta durante 60 segundos en el servicio fabric:/HelloWorldStatefulApplication/HelloWorldStateful.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/HelloWorldStatefulApplication/HelloWorldStateful"

Connect-ServiceFabricCluster

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## TAREA: Quitar una aplicación de Service Fabric
Puede eliminar una instancia de una aplicación implementada, quitar el tipo de aplicación aprovisionada del clúster y quitar el paquete de aplicación del almacén de imágenes.

### Paso 1: quite una instancia de aplicación
Cuando ya no se necesita una instancia de aplicación, utilice el cmdlet [Remove-ServiceFabricApplication](https://msdn.microsoft.com/library/azure/mt125914.aspx) para quitarla de manera permanente. De esta manera se quitarán automáticamente todos los servicios que pertenecen a la aplicación, quitando de forma permanente todos los estados de servicio. No se puede deshacer esta operación y no se puede recuperar el estado de la aplicación.

```powershell
Remove-ServiceFabricApplication fabric:/HelloWorldStatefulApplication
```

### Paso 2: anule el registro del tipo de aplicación
Cuando ya no se necesita una versión determinada de un tipo de aplicación, utilice el cmdlet [Unregister-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125885.aspx) para anularle el registro. Anular el registro de tipos no usados liberará espacio de almacenamiento que utiliza el paquete de aplicación en el almacén de imágenes. No se puede anular el registro de un tipo de aplicación ya que no hay ninguna aplicación con instancias con él o hay actualizaciones de aplicaciones pendientes que hacen referencia a él.

```powershell
Unregister-ServiceFabricApplicationType HelloWorldStatefulApplication 1.0.0.0
```

### Paso 3: quite el paquete de aplicación
Una vez que se anula el registro del tipo de aplicación, es posible utilizar el cmdlet [Remove-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/azure/mt163532.aspx) para quitar el paquete de aplicación del almacén de imágenes.

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString fabric:ImageStore -ApplicationPackagePathInImageStore HelloWorldStateful
```

## Recursos adicionales
[Ciclo de vida de la aplicación de Service Fabric](service-fabric-application-lifecycle.md)

[Administración de un servicio de Service Fabric](service-fabric-manage-your-service-index.md)

[Cmdlets de Azure Service Fabric](https://msdn.microsoft.com/library/azure/mt125965.aspx)

[Cmdlets de Testability de Azure Service Fabric](https://msdn.microsoft.com/library/azure/mt125844.aspx)

<!---HONumber=Oct15_HO4-->