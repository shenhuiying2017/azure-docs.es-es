---
title: Implementación de la aplicación de Service Fabric | Microsoft Docs
description: Cómo implementar y quitar aplicaciones de Service Fabric
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/25/2016
ms.author: ryanwi

---
# Implementación y eliminación de aplicaciones con PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> 
> 

<br/>

Una vez que un [tipo de aplicación se ha empaquetado][10], está listo para la implementación en un clúster de Azure Service Fabric. La implementación implica los tres pasos siguientes:

1. Cargar el paquete de la aplicación
2. Cargar el tipo de aplicación
3. Crear la instancia de aplicación

> [!NOTE]
> Si usa Visual Studio para implementar y depurar aplicaciones en el clúster de desarrollo local, todos los pasos siguientes se controlan automáticamente mediante un script de PowerShell que se encuentra en la carpeta Scripts del proyecto de la aplicación. En este artículo se ofrecen antecedentes acerca de qué hacen esos scripts para que pueda realizar las mismas operaciones fuera de Visual Studio.
> 
> 

## Cargar el paquete de la aplicación
Cargar el paquete de aplicación lo pone en una ubicación a la que pueden tener acceso los componentes internos de Service Fabric. Puede usar PowerShell para realizar la carga. Antes de ejecutar los comandos de PowerShell en este artículo, empiece siempre usando [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) para conectarse al clúster de Service Fabric.

Supongamos que tiene una carpeta denominada *MyApplicationType* que contiene el manifiesto de aplicación necesario y los manifiestos de servicio, así como paquetes de código, configuración y datos. El comando [Copy-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/mt125905.aspx) cargará el paquete al almacén de imágenes del clúster. El cmdlet **Get-ImageStoreConnectionStringFromClusterManifest**, que forma parte del módulo de PowerShell correspondiente al SDK de Service Fabric, se utiliza para obtener la cadena de conexión del almacén de imágenes. Para importar el módulo de SDK, ejecute el siguiente código:

```
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

Puede copiar un paquete de aplicación de *C:\\users\\ryanwi\\Documents\\Visual Studio 2015\\Projects\\MyApplication\\myapplication\\pkg\\debug* a *c:\\temp\\MyApplicationType* (cambie el nombre del directorio "debug" a "MyApplicationType"). El siguiente ejemplo carga el paquete:

~~~
PS C:\temp> dir

    Directory: c:\temp


Mode                LastWriteTime         Length Name                                                                                   
----                -------------         ------ ----                                                                                   
d-----        8/12/2016  10:23 AM                MyApplicationType                                                                          

PS C:\temp> tree /f .\Stateless1Pkg

C:\TEMP\MyApplicationType
│   ApplicationManifest.xml
|
└───Stateless1Pkg
    |   ServiceManifest.xml
    │
    └───Code
    │   │  Microsoft.ServiceFabric.Data.dll
    │   │  Microsoft.ServiceFabric.Data.Interfaces.dll
    │   │  Microsoft.ServiceFabric.Internal.dll
    │   │  Microsoft.ServiceFabric.Internal.Strings.dll
    │   │  Microsoft.ServiceFabric.Services.dll
    │   │  ServiceFabricServiceModel.dll
    │   │  MyService.exe
    │   │  MyService.exe.config
    │   │  MyService.pdb
    │   │  System.Fabric.dll
    │   │  System.Fabric.Strings.dll
    │   │
    │   └───es-ES
    |         Microsoft.ServiceFabric.Internal.Strings.resources.dll
    |         System.Fabric.Strings.resources.dll
    |
    ├───Config
    │     Settings.xml
    │
    └───Data
          init.dat

PS C:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath MyApplicationType -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
Copy application package succeeded

PS D:\temp>
~~~

## Registrar el paquete de la aplicación
El registro del paquete de aplicación hace que la versión y el tipo de la aplicación declarados en el manifiesto de aplicación esté disponible para su uso. El sistema leerá el paquete cargado en el paso anterior, comprobará dicho paquete (lo que equivale a ejecutar [Test-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/mt125950.aspx) localmente), procesará su contenido y copiará el paquete ya procesado en una ubicación del sistema interno.

~~~
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
~~~

El comando [Register-ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125958.aspx) devuelve solo después de que el sistema haya copiado correctamente el paquete de aplicación. El tiempo que tarde dependerá del contenido del paquete de la aplicación. En caso necesario, el parámetro **- TimeoutSec** se puede usar para proporcionar un tiempo de espera más largo. (El tiempo de espera predeterminado es de 60 segundos).

El comando [Get-ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125871.aspx) enumera todas las versiones del tipo de aplicación registradas correctamente.

## Creación de la aplicación
Se puede crear instancias de una aplicación mediante cualquier versión del tipo de aplicación que se ha registrado correctamente mediante el comando [New-ServiceFabricApplication](https://msdn.microsoft.com/library/mt125913.aspx). El nombre de cada aplicación debe empezar con el esquema *fabric:* y ser únicos para cada instancia de la aplicación. En este momento se crean los servicios predeterminados que se hayan definido en el manifiesto de aplicación del tipo de aplicación de destino.

~~~
PS D:\temp> New-ServiceFabricApplication fabric:/MyApp MyApplicationType AppManifestVersion1

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationStatus      : Ready
HealthState            : OK
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/MyService
ServiceKind            : Stateless
ServiceTypeName        : MyServiceType
IsServiceGroup         : False
ServiceManifestVersion : SvcManifestVersion1
ServiceStatus          : Active
HealthState            : Ok

PS D:\temp>
~~~

El comando [Get-ServiceFabricApplication](https://msdn.microsoft.com/library/mt163515.aspx) enumera todas las instancias de aplicación que se crearon correctamente junto con su estado general.

El comando [ServiceFabricService Get](https://msdn.microsoft.com/library/mt125889.aspx) enumera todas las instancias de servicio que se crearon correctamente dentro de una instancia de aplicación determinada. Aquí se enumeran los servicios predeterminados (en caso de haberlos).

Pueden crearse varias instancias de aplicación para cualquier versión concreta de un tipo de aplicación registrado. Cada instancia de la aplicación se ejecuta de forma aislada, con su propio proceso y directorio de trabajo.

## Eliminación de una aplicación
Cuando ya no se necesita una instancia de aplicación, use el comando [Remove-ServiceFabricApplication](https://msdn.microsoft.com/library/mt125914.aspx) para quitarla de manera permanente. Este comando también quita automáticamente todos los servicios que pertenecen a la aplicación, con lo que se eliminan de forma permanente todos los estados de servicio. No se puede deshacer esta operación y no se puede recuperar el estado de la aplicación.

~~~
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
~~~

Cuando ya no se necesita una versión concreta de un tipo de aplicación, debe anularse su registro mediante el comando [Unregister-ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125885.aspx). Con la anulación del registro de los tipos no utilizados, se libera el espacio de almacenamiento que usa el contenido del paquete de aplicación de dicho tipo en el almacén de imágenes. No se puede anular el registro de un tipo de aplicación mientras no haya ninguna aplicación con instancias con él o no haya actualizaciones de aplicaciones pendientes que hagan referencia a él.

~~~
PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp> Unregister-ServiceFabricApplicationType MyApplicationType AppManifestVersion1
Unregister application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

PS D:\temp>
~~~

## Solución de problemas
### Copy-ServiceFabricApplicationPackage pide una ImageStoreConnectionString
El entorno del SDK de Service Fabric ya debe tener configurados los valores predeterminados correctos. Pero si es necesario, ImageStoreConnectionString para todos los comandos debe coincidir con el valor que usa el clúster de Service Fabric. Puede encontrarlo en el manifiesto de clúster recuperado a través del comando [Get-ServiceFabricClusterManifest](https://msdn.microsoft.com/library/mt126024.aspx):

~~~
PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType

cmdlet Copy-ServiceFabricApplicationPackage at command pipeline position 1
Supply values for the following parameters:
ImageStoreConnectionString:

PS D:\temp> Get-ServiceFabricClusterManifest
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]

PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType -ImageStoreConnectionString file:D:\ServiceFabric\Data\ImageStore
Copy application package succeeded

PS D:\temp>
~~~

## Pasos siguientes
[Actualización de la aplicación de Service Fabric](service-fabric-application-upgrade.md)

[Introducción al estado de Service Fabric](service-fabric-health-introduction.md)

[Diagnosticar y solucionar problemas de un servicio de Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelar una aplicación en Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md

<!---HONumber=AcomDC_0928_2016-->