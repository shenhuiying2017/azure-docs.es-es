---
title: "Implementación de la aplicación de Azure Service Fabric | Microsoft Docs"
description: "Cómo implementar y quitar aplicaciones de Service Fabric con PowerShell."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: cd3c97bc95fb7ccfaa0dbf2d7ca76ae598a4b320
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/20/2018
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Implementación y eliminación de aplicaciones con PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [CLI de Service Fabric](service-fabric-application-lifecycle-sfctl.md)
> * [API de FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Una vez que un [tipo de aplicación se ha empaquetado][10], está listo para la implementación en un clúster de Azure Service Fabric. La implementación implica los tres pasos siguientes:

1. Carga del paquete de aplicación en el almacén de imágenes.
2. Registro del tipo de aplicación con la ruta relativa al almacén de imágenes.
3. Creación de la instancia de aplicación.

Una vez que ya no se requiera la aplicación implementada, puede eliminar la instancia de aplicación y el tipo de aplicación. Para quitar completamente una aplicación del clúster, realice los siguientes pasos:

1. Quitar (o eliminar) la instancia de la aplicación en ejecución.
2. Anular el registro del tipo de aplicación si ya no lo necesita.
3. Quitar el paquete de aplicación del almacén de imágenes.

Si usa Visual Studio para implementar y depurar aplicaciones en el clúster de desarrollo local, todos los pasos anteriores se controlan automáticamente mediante un script de PowerShell.  que se encuentra en la carpeta *Scripts* del proyecto de la aplicación. En este artículo se ofrece información sobre lo que hace ese script para que pueda realizar las mismas operaciones fuera de Visual Studio. 

Otra manera de implementar una aplicación es mediante el aprovisionamiento externo. El paquete de aplicación puede estar [empaquetado como `sfpkg`](service-fabric-package-apps.md#create-an-sfpkg) y cargado a un almacén externo. En este caso, no es necesario cargarlo al almacén de imágenes. La implementación requiere los siguientes pasos:

1. Cargue `sfpkg` a un almacén externo. El almacén externo puede ser cualquier almacén que exponga un punto de conexión http o https de REST.
2. Registre el tipo de aplicación mediante el URI de descarga externo y la información de tipo de aplicación.
2. Cree la instancia de aplicación.

Para realizar la limpieza, elimine las instancias de aplicación y anule el registro del tipo de aplicación. Dado que el paquete no se ha copiado al almacén de imágenes, no hay ninguna ubicación temporal para realizar la limpieza. El aprovisionamiento desde un almacén externo está disponible a partir de la versión 6.1 de Service Fabric.

>[!NOTE]
> Visual Studio no es compatible con el aprovisionamiento externo en estos momentos.

 
## <a name="connect-to-the-cluster"></a>Conexión al clúster
Antes de ejecutar los comandos de PowerShell en este artículo, empiece siempre usando [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) para conectarse al clúster de Service Fabric. Para conectarse al clúster de desarrollo local, ejecute lo siguiente:

```powershell
PS C:\>Connect-ServiceFabricCluster
```

Para obtener ejemplos de conexión a un clúster remoto o protegido con Azure Active Directory, certificados X509 o Windows Active Directory, consulte [Conexión a un clúster seguro](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Cargar el paquete de la aplicación
Cargar el paquete de aplicación lo pone en una ubicación a la que pueden tener acceso los componentes internos de Service Fabric.
Si quiere comprobar el paquete de la aplicación de forma local, use el cmdlet [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps).

El comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) cargará el paquete de aplicación en el almacén de imágenes del clúster.

Imagine que compila y empaqueta una aplicación denominada *MyApplication* en Visual Studio 2015. De forma predeterminada, el nombre del tipo de aplicación que aparece en ApplicationManifest.xml es "MyApplicationType".  El paquete de aplicación, que contiene el manifiesto de aplicación necesario, así como los manifiestos de servicio y los paquetes code/config/data, se encuentra en *C:\Users\<username\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*. 

En el siguiente comando se muestra el contenido del paquete de aplicación:

```powershell
PS C:\> $path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
PS C:\> tree /f $path
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

Si el paquete de aplicación es grande y/o tiene muchos archivos, puede [comprimirlo](service-fabric-package-apps.md#compress-a-package). La compresión reduce el tamaño y el número de archivos.
El inconveniente es que el registro y la anulación del registro del tipo de aplicación son más rápidos. El tiempo de carga puede ser más lento en la actualidad, especialmente si incluye el tiempo para comprimir el paquete. 

Para comprimir un paquete, use el mismo comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps). La compresión puede realizarse independiente de la carga, utilizando la marca `SkipCopy`, o junto con la operación de carga. Comprimir un paquete comprimido es una operación inútil.
Para descomprimir un paquete comprimido, use el mismo comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) con el modificador `UncompressPackage`.

El siguiente cmdlet comprime el paquete sin copiarlo en el almacén de imágenes. El paquete ahora incluye los archivos comprimidos para los paquetes `Code` y `Config`. La aplicación y los manifiestos de servicio no se comprimen porque son necesarios para muchas operaciones internas (por ejemplo, uso compartido de paquetes, nombre de tipo de aplicación y extracción de versiones para ciertas validaciones). La compresión de los manifiestos haría que estas operaciones resultaran ineficaces.

```
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
PS C:\> tree /f $path
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
|   ApplicationManifest.xml
|
└───Stateless1Pkg
       Code.zip
       Config.zip
       ServiceManifest.xml
```

Para paquetes de aplicación de gran tamaño, la compresión lleva tiempo. Para obtener mejores resultados, use una unidad SSD rápida. Los tiempos de compresión y el tamaño del paquete comprimido también varían en función del contenido del paquete.
Por ejemplo, a continuación figuran estadísticas de compresión para algunos paquetes, que muestran el tamaño de paquete inicial y comprimido, con el tiempo de compresión.

|Tamaño inicial (MB)|Número de archivos|Tipos de compresión|Tamaño de paquete comprimido (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Una vez que un paquete está comprimido, se puede cargar en uno o varios clústeres de Service Fabric según sea necesario. El mecanismo de implementación es el mismo para los paquetes comprimidos y sin comprimir. Los paquetes comprimidos se almacenan como tal en el almacén de imágenes de clúster. Los paquetes no están comprimidos en el nodo, antes de que se ejecute la aplicación.


En el ejemplo siguiente se carga el paquete en el almacén de imágenes en una carpeta denominada "MyApplicationV1":

```powershell
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Si no se especifica el parámetro *-ApplicationPackagePathInImageStore*, el paquete de aplicación se copia en la carpeta "Debug" del almacén de imágenes.

>[!NOTE]
>**Copy-ServiceFabricApplicationPackage** detecta automáticamente la cadena de conexión del almacén de imágenes adecuado si la sesión de PowerShell está conectada a un clúster de Service Fabric. En las versiones de Service Fabric anteriores a 5.6, el argumento **-ImageStoreConnectionString** se debe proporcionar explícitamente.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>El cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** , que forma parte del módulo de PowerShell correspondiente al SDK de Service Fabric, se utiliza para obtener la cadena de conexión del almacén de imágenes.  Para importar el módulo de SDK, ejecute el siguiente código:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Consulte [Descripción del valor ImageStoreConnectionString](service-fabric-image-store-connection-string.md) para más información sobre el almacén de imágenes y su cadena de conexión.
>
>
>

El tiempo necesario para cargar un paquete depende de varios factores. Algunos de estos factores son el número de archivos del paquete, el tamaño del paquete y los tamaños de archivo. La velocidad de la red entre la máquina de origen y el clúster de Service Fabric también afecta al tiempo de carga. El tiempo de espera predeterminado para [ServiceFabricApplicationPackage copia](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) es de 30 minutos.
Dependiendo de los factores descritos, puede que tenga que aumentar el tiempo de espera. Si va a comprimir el paquete en la llamada de copia, también debe tener en cuenta el tiempo de compresión.



## <a name="register-the-application-package"></a>Registrar el paquete de la aplicación
El tipo y la versión de la aplicación declarados en el manifiesto de aplicación estarán disponibles para usarse cuando se registre el paquete de aplicación. El sistema leerá el paquete cargado en el paso anterior, comprobará dicho paquete, procesará su contenido y copiará el paquete procesado en una ubicación del sistema interno.  

Ejecute el cmdlet [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) para registrar el tipo de aplicación en el clúster y ponerlo a disposición para la implementación:

### <a name="register-the-application-package-copied-to-image-store"></a>Registre el paquete de aplicación que se haya copiado al almacén de imágenes
Si un paquete se ha copiado anteriormente al almacén de imágenes, la operación de registro especifica la ruta relativa en el almacén de imágenes.

```powershell
PS C:\> Register-ServiceFabricApplicationType -ApplicationPackagePathInImageStore MyApplicationV1
Register application type succeeded
```

"MyApplicationV1" es la carpeta del almacén de imágenes donde se encuentra el paquete de aplicación. El tipo de aplicación con el nombre "MyApplicationType" y la versión "1.0.0" (ambos se encuentran en el manifiesto de aplicación) ya estará registrado en el clúster.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Registro del paquete de aplicación copiado a un almacén externo
A partir de la versión 6.1 de Service Fabric, el aprovisionamiento es compatible con la descarga del paquete desde un almacén externo. El URI de descarga representa la ruta al [paquete de aplicación `sfpkg`](service-fabric-package-apps.md#create-an-sfpkg) desde la que puede descargar el paquete de aplicación mediante los protocolos HTTP o HTTPS. Debe haber cargado el paquete previamente a esta ubicación externa. El URI debe permitir el acceso de lectura para que Service Fabric pueda descargar el archivo. El archivo `sfpkg` debe tener la extensión ".sfpkg". La operación de aprovisionamiento debe incluir la información de tipo de aplicación, tal y como se encuentra en el manifiesto de la aplicación.

```
PS C:\> Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

El comando [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) devuelve solo después de que el sistema haya registrado correctamente el paquete de aplicación. El tiempo que tarde en registrarse dependerá del contenido del paquete de aplicación. El parámetro **- TimeoutSec** se puede usar para proporcionar un tiempo de espera más largo (el tiempo de espera predeterminado es de 60 segundos).

Si tiene un paquete de aplicación grande o está experimentando tiempos de espera, use el parámetro **-Async**. El comando se devuelve cuando el clúster acepta el comando de registro. La operación de registro continúa según sea necesario.
El comando [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) enumera las versiones del tipo de aplicación, así como el estado de registro. Puede utilizar este comando para determinar cuándo se realiza el registro.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Eliminación de un paquete de aplicación del almacén de imágenes
Si un paquete se ha copiado al almacén de imágenes, debe eliminarlo de la ubicación temporal una vez que la aplicación se haya registrado correctamente. Al eliminar los paquetes de aplicación del almacén de imágenes, se liberan recursos del sistema. Mantener los paquetes de aplicación sin usar consume almacenamiento en disco y conduce a problemas de rendimiento de la aplicación.

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Creación de la aplicación
Se pueden crear instancias de una aplicación a partir de cualquier versión del tipo de aplicación que se haya registrado correctamente mediante el cmdlet [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps). El nombre de cada aplicación debe empezar con el esquema *"fabric:"* y ser único para cada instancia de la aplicación. También se crean los servicios predeterminados que se hayan definido en el manifiesto de aplicación del tipo de aplicación de destino.

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
Pueden crearse varias instancias de aplicación para cualquier versión concreta de un tipo de aplicación registrado. Cada instancia de la aplicación se ejecuta de forma aislada, con su propio proceso y directorio de trabajo.

Para ver el nombre de las aplicaciones y los servicios en ejecución en el clúster, ejecute los cmdlets [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) y [Get-ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps):

```powershell
PS C:\> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}

PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Eliminación de una aplicación
Cuando ya no se necesite una instancia de aplicación, use el cmdlet [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) para quitarla por nombre de manera permanente. [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) también quita automáticamente todos los servicios que pertenezcan a la aplicación, con lo que se eliminan de forma permanente todos los estados del servicio. 

> [!WARNING]
> No se puede deshacer esta operación y no se puede recuperar el estado de la aplicación.

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Anulación de un registro del tipo de aplicación
Cuando ya no se necesita una versión concreta de un tipo de aplicación, debe anularse el registro del tipo de aplicación mediante el cmdlet [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps). Anular el registro de los tipos de aplicación que no se usan permite liberar el espacio de almacenamiento que usa el almacén de imágenes, ya que se quitan los tipos de archivos de aplicaciones. Al anular el registro de un tipo de aplicación, no se eliminará el paquete de aplicación que se ha copiado a la ubicación temporal del almacén de imágenes, si se ha utilizado la copia al almacén de imágenes. No se puede anular el registro de un tipo de aplicación mientras no haya ninguna aplicación con instancias con él o no haya actualizaciones de aplicaciones pendientes que hagan referencia a él.

Ejecute el cmdlet [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) para ver los tipos de aplicación registrados actualmente en el clúster:

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Ejecute [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) para anular el registro de un tipo concreto de aplicación:

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>solución de problemas
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage pide una ImageStoreConnectionString
El entorno del SDK de Service Fabric ya debe tener configurados los valores predeterminados correctos. Pero si es necesario, ImageStoreConnectionString para todos los comandos debe coincidir con el valor que usa el clúster de Service Fabric. Puede encontrar ImageStoreConnectionString en el manifiesto del clúster, recuperado mediante los comandos [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) y Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

El cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** , que forma parte del módulo de PowerShell correspondiente al SDK de Service Fabric, se utiliza para obtener la cadena de conexión del almacén de imágenes.  Para importar el módulo de SDK, ejecute el siguiente código:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

ImageStoreConnectionString se encuentra en el manifiesto de clúster:

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Consulte [Descripción del valor ImageStoreConnectionString](service-fabric-image-store-connection-string.md) para más información sobre el almacén de imágenes y su cadena de conexión.

### <a name="deploy-large-application-package"></a>Implementación de un paquete de aplicación grande
Problema: [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) agota el tiempo de espera para un paquete de aplicación grande (del orden de GB).
Pruebe lo siguiente:
- Especifique un tiempo de espera mayor para el comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) con el parámetro `TimeoutSec`. De forma predeterminada, el tiempo de espera es de 30 minutos.
- Compruebe la conexión de red entre la máquina de origen y el clúster. Si la conexión es lenta, considere la posibilidad de usar una máquina con una conexión de red mejor.
Si la máquina cliente se encuentra en otra región diferente al clúster, considere el uso de una máquina cliente que se encuentre en una región más cercana o en la misma región que el clúster.
- Compruebe si está alcanzando la limitación externa. Por ejemplo, cuando el almacén de imágenes está configurado para usar Azure Storage, se puede limitar carga.

Problema: La carga del paquete se completó correctamente, pero [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) agota el tiempo de espera. Pruebe lo siguiente:
- [Comprima el paquete](service-fabric-package-apps.md#compress-a-package) antes de realizar la copia en el almacén de imágenes.
La compresión reduce el tamaño y el número de archivos lo que, a su vez, reduce la cantidad de tráfico y trabajo que Service Fabric debe realizar. La operación de carga puede ser más lenta (especialmente si incluyen el tiempo de compresión), pero el registro y la anulación del registro del tipo de aplicación son más rápidos.
- Especifique un tiempo de espera mayor para [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) con el parámetro `TimeoutSec`.
- Especifique el modificador `Async` para [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). El comando devuelve resultados cuando el clúster acepta dicho comando y el registro del tipo de aplicación continúa de manera asincrónica. Por esta razón, no hay ninguna necesidad de especificar un tiempo de espera mayor en este caso. El comando [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) enumera todas las versiones del tipo de aplicación registradas correctamente, así como el estado de registro. Puede utilizar este comando para determinar cuándo se realiza el registro.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Implementación del paquete de aplicación con muchos archivos
Problema: [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) agota el tiempo de espera para un paquete de aplicación con muchos archivos (del orden de miles).
Pruebe lo siguiente:
- [Comprima el paquete](service-fabric-package-apps.md#compress-a-package) antes de realizar la copia en el almacén de imágenes. La compresión reduce el número de archivos.
- Especifique un tiempo de espera mayor para [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) con el parámetro `TimeoutSec`.
- Especifique el modificador `Async` para [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). El comando devuelve resultados cuando el clúster acepta dicho comando y el registro del tipo de aplicación continúa de manera asincrónica.
Por esta razón, no hay ninguna necesidad de especificar un tiempo de espera mayor en este caso. El comando [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) enumera todas las versiones del tipo de aplicación registradas correctamente, así como el estado de registro. Puede utilizar este comando para determinar cuándo se realiza el registro.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>pasos siguientes
[Empaquetado de una aplicación](service-fabric-package-apps.md)

[Actualización de la aplicación de Service Fabric](service-fabric-application-upgrade.md)

[Introducción al estado de Service Fabric](service-fabric-health-introduction.md)

[Diagnosticar y solucionar problemas de un servicio de Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelar una aplicación en Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
