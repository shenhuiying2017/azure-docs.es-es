---
title: "Empaquetado de una aplicación de Azure Service Fabric | Microsoft Docs"
description: "Empaquetado de una aplicación de Service Fabric antes de implementarla en un clúster."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: ryanwi
ms.openlocfilehash: 486a27d7ca576c8fe1552c02eb24ece6b8bb2ba8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="package-an-application"></a>Empaquetar una aplicación
En este artículo se describe cómo empaquetar una aplicación de Service Fabric y prepararla para su implementación.

## <a name="package-layout"></a>Diseño del paquete
El manifiesto de aplicación, uno o varios manifiestos de servicio y otros archivos de paquete necesarios deben organizarse en un diseño específico para la implementación en un clúster de Service Fabric. Los manifiestos de ejemplo en este artículo deberían organizarse con la siguiente estructura de directorios:

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

Las carpetas reciben un nombre para coincidir con los atributos **Nombre** de cada elemento correspondiente. Por ejemplo, si el manifiesto de servicio contenía dos paquetes de código con los nombres **MiCódigoA** y **MiCódigoB**, dos carpetas con los mismos nombres incluirán los archivos binarios necesarios para cada paquete de código.

## <a name="use-setupentrypoint"></a>Usar SetupEntrypoint
Los escenarios de uso de **SetupEntryPoint** habituales corresponden a cuando se necesita ejecutar un archivo ejecutable antes de iniciar el servicio o cuando necesita realizar una operación con privilegios elevados. Por ejemplo:

* configuración e inicialización de las variables de entorno que el ejecutable del servicio necesita. No se limita tan solo a los archivos ejecutables escritos a través de los modelos de programación de Service Fabric. Por ejemplo, npm.exe necesita algunas variables de entorno configuradas para implementar una aplicación node.js.
* Configuración del control de acceso mediante la instalación de certificados de seguridad.

Para obtener más información sobre cómo configurar **SetupEntryPoint**, vea [Configuración de la directiva para un punto de entrada del programa de instalación del servicio](service-fabric-application-runas-security.md).

<a id="Package-App"></a>
## <a name="configure"></a>Configuración
### <a name="build-a-package-by-using-visual-studio"></a>Crear un paquete mediante Visual Studio
Si usa Visual Studio 2015 para crear su aplicación, puede utilizar el comando Package para crear automáticamente un paquete que coincida con el diseño descrito anteriormente.

Para crear un paquete, haga clic con el botón derecho en el proyecto de aplicación del Explorador de soluciones y elegir el comando Package, como se muestra a continuación:

![Empaquetado de una aplicación con Visual Studio][vs-package-command]

Una vez completado el empaquetado, busque la ubicación del paquete en la ventana **Salida**. El paso de empaquetado se produce automáticamente al implementar o depurar la aplicación en Visual Studio.

### <a name="build-a-package-by-command-line"></a>Creación de un paquete mediante la línea de comandos
También es posible empaquetar la aplicación mediante programación usando `msbuild.exe`. En el fondo esto es lo que ejecuta Visual Studio, por lo que la salida es la misma.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Probar el paquete
Puede comprobar la estructura del paquete localmente a través de PowerShell mediante el comando [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) .
Este comando comprueba si existen problemas de análisis de manifiestos y verifica todas las referencias. Este comando solo comprueba la corrección estructural de los directorios y archivos del paquete.
No comprobará ningún contenido de código o de paquete de datos y se limitará a comprobar que todos los archivos necesarios están presentes.

```
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Este error muestra que el archivo *MySetup.bat* al que se hace referencia en el manifiesto de servicio **SetupEntryPoint** no está en el paquete de código. Después de agregar el archivo que falta, se comprueba la aplicación:

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
```

Si la aplicación tiene [parámetros de aplicación](service-fabric-manage-multiple-environment-app-configuration.md) definidos, puede pasarlos en [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) para realizar una validación adecuada.

Si conoce el clúster donde se implementará la aplicación, se recomienda pasar el parámetro `ImageStoreConnectionString`. En este caso, el paquete también se valida con las versiones anteriores de la aplicación que ya se está ejecutando en el clúster. Por ejemplo, la validación puede detectar si ya se implementó un paquete con la misma versión pero distinto contenido.  

Una vez que la aplicación se empaqueta correctamente y supera la validación, haga una evaluación según el tamaño y el número de archivos si es necesario comprimir.

## <a name="compress-a-package"></a>Compresión de un paquete
Cuando un paquete es grande o tiene muchos archivos, puede comprimirlo para acelerar la implementación. La compresión reduce el número de archivos y el tamaño del paquete.
Si se trata de un paquete de aplicación comprimido, la [carga del paquete de aplicación](service-fabric-deploy-remove-applications.md#upload-the-application-package) puede tardar más en comparación con un paquete sin comprimir, sobre todo, si se tiene en cuenta el tiempo de compresión; sin embargo, el [registro](service-fabric-deploy-remove-applications.md#register-the-application-package) y la [anulación del registro del tipo de aplicación](service-fabric-deploy-remove-applications.md#unregister-an-application-type) se realizan más rápido si el paquete está comprimido.

El mecanismo de implementación es el mismo para los paquetes comprimidos y sin comprimir. Si el paquete se comprime, se almacena como tal en el almacén de imágenes de clúster y se descomprime en el nodo antes de ejecutar la aplicación.
La compresión reemplaza el paquete de Service Fabric válido por la versión comprimida. La carpeta debe permitir permisos de escritura. La ejecución de la compresión en un paquete ya comprimido no produce ningún cambio.

Puede comprimir un paquete ejecutando el comando de Powershell [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) con el modificador `CompressPackage`. Puede descomprimir el paquete con el mismo comando, mediante el modificador `UncompressPackage`.

El siguiente comando comprime el paquete sin copiarlo en el almacén de imágenes. Puede copiar un paquete comprimido en uno o varios clústeres de Service Fabric, según sea necesario, con [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) sin la marca `SkipCopy`.
El paquete ahora incluye los archivos comprimidos para los paquetes `code`, `config` y `data`. Los manifiestos de aplicación y de servicio no se comprimen porque son necesarios para muchas operaciones internas (por ejemplo, uso compartido de paquetes, nombre de tipo de aplicación y extracción de versiones para ciertas validaciones).
La compresión de los manifiestos haría que estas operaciones resultaran ineficaces.

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy

PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

Como alternativa, puede comprimir y copiar el paquete con [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) en un solo paso.
Si el paquete es grande, proporcione un tiempo de espera lo suficientemente alto como para dar tiempo tanto para la compresión del paquete como para la carga en el clúster.
```
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Internamente, Service Fabric calcula las sumas de comprobación para los paquetes de aplicación para la validación. Cuando se utilice la compresión, las sumas de comprobación se calculan en las versiones comprimidas de cada paquete.
Si ha copiado una versión sin comprimir del paquete de aplicación y desea utilizar la compresión para el mismo paquete, debe cambiar la versión de los paquetes `code`, `config` y `data` para evitar la falta de coincidencia en la suma de comprobación. Si los paquetes no han cambiado, en lugar de modificar la versión, puede usar el [aprovisionamiento de diferencias](service-fabric-application-upgrade-advanced.md). Con esta opción, no incluya el paquete sin cambios; en su lugar, haga referencia a él desde el manifiesto de servicio.

De forma similar, si ha cargado una versión comprimida del paquete y desea usar un paquete sin comprimir, debe actualizar las versiones para evitar la falta de coincidencia en la suma de comprobación.

El paquete se empaqueta correctamente, validado y comprimido (si es necesario), por lo que está listo para la [implementación](service-fabric-deploy-remove-applications.md) en uno o varios clústeres de Service Fabric.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Compresión de paquetes al implementar con Visual Studio
Puede indicar a Visual Studio que comprima paquetes en la implementación, agregando el elemento `CopyPackageParameters` al perfil de publicación, y establezca el atributo `CompressPackage` en `true`.

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="next-steps"></a>Pasos siguientes
[Implementación y eliminación de aplicaciones][10] describe cómo usar PowerShell para administrar las instancias de aplicaciones.

[Administración de los parámetros de la aplicación en varios entornos][11] describe cómo configurar parámetros y variables de entorno para diferentes instancias de aplicación.

[Configuración de directivas de seguridad para la aplicación][12] describe cómo ejecutar los servicios a través de directivas de seguridad para restringir el acceso.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
