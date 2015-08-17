<properties
   pageTitle="Tutorial de actualización de aplicación de Service Fabric"
   description="Este artículo le guía a través de una experiencia de actualización de una aplicación de Service Fabric."
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



# Tutorial de actualización de la aplicación

El enfoque de actualización recomendado y usado con mayor frecuencia es la actualización gradual supervisada. Service Fabric supervisa el estado de la aplicación que se está actualizando en función de un conjunto de directivas de mantenimiento. Cuando se hayan actualizado las aplicaciones en un dominio de actualización (UD), Service Fabric evalúa el estado de la aplicación y determina si se debe continuar con el siguiente dominio de actualización o se producirá un error en la actualización según las directivas de mantenimiento. Se puede realizar una actualización de la aplicación supervisada mediante las API nativas o administradas, PowerShell o REST.

La actualización gradual supervisada del Service Fabric permite al administrador de la aplicación configurar la directiva de evaluación de estado que Service Fabric usa para determinar que la aplicación sea correcta. Además, también permite al administrador configurar la acción que se realizará cuando la evaluación de estado no funciona como reversión automática. Esta sección guía por una actualización supervisada para uno de los ejemplos del SDK.

## Paso 1: Crear e implementar el ejemplo de objetos visuales

Estos pasos se pueden llevar a cabo abriendo el proyecto en Visual Studio y haciendo clic en la solución y seleccionando el comando para implementar en el elemento de menú de Service Fabric. Vea [administrar su aplicación de Service Fabric en Visual Studio](service-fabric-manage-application-in-visual-studio.md) para obtener más información. O bien, se puede usar PowerShell.

> [AZURE.NOTE]Antes de que se pueda usar cualquiera de los comandos de Service Fabric en PowerShell, hay que conectarse primero al clúster mediante el cmdlet `Connect-ServiceFabricCluster`. Del mismo modo, se supone que el clúster ya se ha configurado en la máquina local. Vea el artículo sobre [configuración del entorno de desarrollo de Service Fabric](service-fabric-get-started.md).

Después de compilar el proyecto en Visual Studio, se puede usar el comando de PowerShell **Copy-ServiceFabricApplicationPackage** para copiar el paquete de aplicación en el ImageStore, seguido del registro de la aplicación en el tiempo de ejecución de Service Fabric mediante el cmdlet **Register-ServiceFabricApplicationPackage** y por último, iniciando una instancia de la aplicación mediante el cmdlet **New-ServiceFabricApplication**. Estos tres pasos son análogos al uso del elemento de menú Implementar en Visual Studio.

Ahora, puede usar el [explorador de Service Fabric para ver el clúster y la aplicación](service-fabric-visualizing-your-cluster.md). La aplicación tiene un servicio web hasta el que se puede navegar en Internet Explorer; para ello, escriba [http://localhost:80](http://localhost:80) en la barra de direcciones. Debería ver algunos objetos visuales flotantes desplazándose por la pantalla. Además, se puede usar **Get-ServiceFabricApplication** para comprobar el estado de la aplicación.

## Paso 2: Actualizar el ejemplo de objetos visuales

Puede que observe que con la versión que se implementó en el Paso 1, los objetos visuales no giran. Vamos a actualizar esta aplicación a una donde también giren los objetos visuales.

Seleccione el proyecto VisualObjects.DataService dentro de la solución VisualObjects y abra el archivo VisualObjects.cs en ese proyecto. Dentro de ese archivo, navegue hasta el método `CreateMovedObject` y busque la línea donde se establece el giro de los objetos visuales (busque las siguientes líneas en el método `CreateMovedObject`). Comente la línea `nextObject.Rotation = 1` y quite el comentario de la línea que aparece debajo. Cuando se realicen los cambios, el código debe ser similar al siguiente:

```c#
            //nextObject.Rotation = 1;

            // Once that's deployed, uncomment this line and upgrade the application:

             nextObject.Rotation = (nextObject.Rotation + 10) % 360;
```

También necesitamos actualizar el archivo *ServiceManifest.xml* (debajo de PackageRoot) del proyecto **VisualObjects.DataService**. Actualice el *CodePackage* y la versión del servicio a 2.0 y las líneas correspondientes del archivo *ServiceManifest.xml* deben ser similares a las siguientes (las partes resaltadas muestran los cambios):

```xml
<ServiceManifestName="VisualObjects.DataService"Version="2.0"xmlns="http://schemas.microsoft.com/2011/01/fabric"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code"Version="2.0">
```

Ahora, necesitamos actualizar el archivo *ApplicationManifest.xml* (que se encuentra en el **VisualObjects** debajo de la solución **VisualObjects**) para usar la versión 2.0 del proyecto **VisualObjects.DataService** y actualizar también la versión de la aplicación de 1.0.0.0 a 2.0.0.0. Ahora, las líneas correspondientes del archivo *ApplicationManifest.xml* debe tener el siguiente aspecto:

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"ApplicationTypeName="VisualObjects"ApplicationTypeVersion="2.0.0.0"xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.DataService"ServiceManifestVersion="2.0" />
```

Ahora, para generar el proyecto, seleccione el proyecto **VisualObjects** y haga clic con el botón secundario y seleccione la compilación en Visual Studio (si selecciona Volver a generar todo, es posible que tenga que actualizar las versiones del proyecto **VisualObjects.WebService** también en su *ServiceManifest.xml* y en *ApplicationManifest.xml*, ya que el código habría cambiado). Ahora, vamos a empaquetar la aplicación actualizada y, para ello, hacemos clic con el botón secundario en el proyecto *VisualObjects* y seleccionamos el menú de Service Fabric y elegimos Paquete. Así se debe crear un paquete de aplicación que se puede implementar. Su aplicación actualizada está lista para implementarse ahora.

## Paso 3: Decidir sobre las directivas de mantenimiento y parámetros de actualización

Familiarícese con los [parámetros de actualización de la aplicación](service-fabric-application-upgrade-parameters.md) y el [proceso de actualización](service-fabric-application-upgrade.md) para obtener una buena comprensión de los distintos parámetros de actualización, y el criterio aplicado de mantenimiento y tiempos de espera. En este tutorial, se dejará el criterio de evaluación de estado de servicio para que sea el predeterminado (y los valores recomendados), lo que significa que todos los servicios e instancias deben ser _correctos_ tras la actualización. Sin embargo, vamos a aumentar la *HealthCheckStableDuration* en 60 segundos (de modo que los servicios sean correctos durante al menos 20 segundos antes de que la actualización continúe con el siguiente dominio de actualización). Vamos a configurar también *UpgradeDomainTimeout* para que sea 1200 segundos y *UpgradeTimeout* para que sea 3.000 segundos. Por último, también estableceremos *UpgradeFailureAction* para reversión, solicitando por tanto esa reversión de Service Fabric de la aplicación a la versión anterior si encuentra algún problema durante la actualización. Así pues, los parámetros de actualización que especificaremos al iniciar la actualización (en el paso 4) serán los siguientes:

FailureAction = Rollback

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## Paso 4: Preparar la aplicación para la actualización

Ahora, la aplicación se ha creado y está lista para actualizarse. Si abre una ventana de PowerShell como administrador y escribe **Get-ServiceFabricApplication**, debe indicarle que es el tipo de aplicación 1.0.0.0 de **VisualObjects** la que se ha implementado. El paquete de aplicación se almacena en la siguiente ruta de acceso relativa donde descomprimió el SDK de Service Fabric: *Samples\\Services\\Stateful\\VisualObjects\\VisualObjects\\obj\\x64\\Debug*. Debería encontrará una carpeta "Paquete" en ese directorio, que es donde se almacena el paquete de la aplicación. Compruebe las marcas de tiempo para asegurarse de que es la última compilación (y puede que necesite además modificar las rutas de acceso adecuadamente).

Ahora vamos a copiar el paquete de la aplicación actualizada a la ImageStore de Service Fabric (donde se almacenan los paquetes de la aplicación por Service Fabric). El parámetro *ApplicationPackagePathInImageStore* informa a Service Fabric sobre dónde puede encontrar el paquete de la aplicación. Hemos colocado la aplicación actualizada en "VisualObjects\_V2" con el siguiente comando (puede que tenga que modificar rutas de acceso de nuevo de manera adecuada).

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

El siguiente paso es registrar esta aplicación con Service Fabric, lo que se puede realizar mediante el comando siguiente:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Si el comando anterior no es correcto, es probable que necesite una recompilación de todos los servicios. Como se mencionó en el Paso 2, puede que tenga que actualizar además la versión de WebService.

## Paso 5: Iniciar la actualización de la aplicación

Ahora, todos estamos preparados para iniciar la actualización de la aplicación mediante el siguiente comando:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Tenga en cuenta que el nombre de aplicación es como se describió en el archivo *ApplicationManifest.xml*. Service Fabric usa este nombre para identificar qué aplicación se está actualizando. Si establece los tiempos de espera para que sean demasiado breves, puede que reciba un mensaje de error que indique el problema. Consulte la sección de solución de problemas o aumente los tiempos de espera.

Ahora, a medida que continúa la actualización de la aplicación, puede supervisarla mediante el Explorador de Service Fabric o mediante el siguiente comando de PowerShell: **Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects**.

En unos minutos, el estado que usa el comando PowerShell anterior debe indicar que se han actualizado (completado) todos los dominios de actualización. Y debería descubrir que los objetos visuales de la ventana del explorador ahora habrán empezado a girar.

Puede que desee intentar cambiar las versiones y pasar de la versión 2 a la versión 3 como ejercicio, o incluso de la versión 2 a la versión 1 (sí, puede actualizar de v2 a v1). Juegue con los tiempos de espera y las directivas de mantenimiento para familiarizarse. Cuando implemente en un clúster de Azure, los parámetros usados serán diferentes de los que funcionaban al implementar en un clúster local; se recomienda establecer los tiempos de espera de manera conservadora.


## Pasos siguientes

[Parámetros de actualización](service-fabric-application-upgrade-parameters.md)

[Serialización de datos](service-fabric-application-upgrade-data-serialization.md)

[Temas avanzados](service-fabric-application-upgrade-advanced.md)

[Solucionar problemas de una actualización de aplicación ](service-fabric-application-upgrade-troubleshooting.md)
 

<!---HONumber=August15_HO6-->