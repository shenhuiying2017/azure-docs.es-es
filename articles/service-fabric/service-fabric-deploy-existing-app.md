---
title: Implementación de un ejecutable invitado en Azure Service Fabric | Microsoft Docs
description: Aprenda a empaquetar una aplicación existente como ejecutable invitado para implementarla en un clúster de Service Fabric.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/02/2017
ms.author: mfussell
ms.openlocfilehash: 99d34d59bb9d55ff074d454fe4544917c4e91110
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205994"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Empaquetado e implementación de un ejecutable existente en Service Fabric
Al empaquetar un ejecutable existente como [ejecutable invitado](service-fabric-guest-executables-introduction.md), puede elegir usar una plantilla de proyecto de Visual Studio o [crear el paquete de aplicación manualmente](#manually). Con Visual Studio, la estructura del paquete de aplicación y los archivos de manifiesto se crean mediante la plantilla para nuevos proyectos.

> [!TIP]
> La forma más sencilla de empaquetar un ejecutable de Windows existente en un servicio es usar Visual Studio y, en Linux, usar Yeoman.
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Uso de Visual Studio para empaquetar e implementar un ejecutable existente
Visual Studio proporciona una plantilla de servicio de Service Fabric para ayudarle a implementar un ejecutable invitado en un clúster de Service Fabric.

1. Seleccione **Archivo** > **Nuevo proyecto** y cree una aplicación de Service Fabric.
2. Elija **Ejecutable invitado** como plantilla de servicio.
3. Haga clic en **Examinar** para seleccionar la carpeta con el ejecutable y rellene el resto de los parámetros para crear el servicio.
   * *Comportamiento del paquete de código*. Se puede configurar para que copie todo el contenido de la carpeta en el proyecto de Visual Studio, lo que resulta útil si el ejecutable no cambia. En cambio, si prevee que el ejecutable cambie y desea tener la capacidad de recopilar las nuevas versiones de forma dinámica, puede vincular directamente a la carpeta. Puede usar carpetas vinculadas al crear el proyecto de aplicación en Visual Studio. Esto vincula a la ubicación de origen desde dentro del proyecto, lo que permite actualizar el ejecutable invitado en su destino de origen. Esas actualizaciones formarán parte del paquete de aplicación en la compilación.
   * *Programa* especifica el ejecutable que se debe ejecutar para iniciar el servicio.
   * *Argumentos* especifica los argumentos que se deben pasar al ejecutable. Puede ser una lista de parámetros con argumentos.
   * *WorkingFolder* especifica el directorio de trabajo para el proceso que se va a iniciar. Puede especificar tres valores:
     * `CodeBase` especifica que el directorio de trabajo se va a establecer en el directorio Code del paquete de aplicación (el directorio `Code` que se muestra en la estructura de archivos anterior).
     * `CodePackage` especifica que el directorio de trabajo se va a establecer en la raíz del paquete de aplicación (`GuestService1Pkg` en la estructura de archivos anterior).
     * `Work` especifica que los archivos se colocan en un subdirectorio denominado Work.
4. Asigne un nombre a su servicio y haga clic en **Aceptar**.
5. Si el servicio necesita un punto de conexión para establecer comunicación, ahora podrá agregar el protocolo, el puerto y el tipo al archivo ServiceManifest.xml. Por ejemplo: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Ya puede utilizar el paquete y publicar la acción en el clúster local mediante la depuración de la solución en Visual Studio. Cuando esté listo puede publicar la aplicación en un clúster remoto o comprobar la solución en el control de código fuente.
7. Lea [Comprobación de la aplicación en ejecución](#check-your-running-application) para ver el servicio ejecutable invitado que se ejecuta en Service Fabric Explorer.

Para ver un tutorial de ejemplo, consulte [Creación de la primera aplicación ejecutable invitada con Visual Studio](quickstart-guest-app.md).

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Uso de Yeoman para empaquetar e implementar un ejecutable existente en Linux

El procedimiento para crear e implementar un invitado ejecutable en Linux es el mismo que con una aplicación de csharp o Java.

1. En un terminal, escriba `yo azuresfguest`.
2. Asigne un nombre a la aplicación.
3. Asigne un nombre al servicio y especifique los detalles, lo que incluye la ruta de acceso del ejecutable y los parámetros con los que se debe invocar.

Yeoman crea un paquete de aplicación con la aplicación correspondiente y los archivos de manifiesto junto con los scripts de instalación y desinstalación.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Empaquetado e implementación manuales de un archivo ejecutable existente
El proceso de empaquetado manual de un ejecutable invitado se basa en los siguientes pasos generales:

1. Crear la estructura de directorios del paquete.
2. Agregar archivos de código y configuración de la aplicación
3. Editar el archivo de manifiesto de servicio.
4. Editar el archivo de manifiesto de aplicación.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Crear la estructura de directorios del paquete
Puede empezar por crear la estructura de directorios, como se describe en la sección anterior, "Estructura de archivo del paquete de aplicación".

### <a name="add-the-applications-code-and-configuration-files"></a>Agregar archivos de código y la configuración de la aplicación
Después de haber creado la estructura de directorios, puede agregar los archivos de código y configuración de la aplicación en los directorios de código y configuración. También puede crear directorios adicionales o subdirectorios en los directorios code o config.

Service Fabric hace una `xcopy` del contenido del directorio raíz de la aplicación para que no haya ninguna estructura predefinida para usar aparte de creación de los dos directorios principales code y config. (Puede elegir diferentes nombres si lo desea. Obtenga más detalles en la sección siguiente.)

> [!NOTE]
> Asegúrese de incluir todos los archivos y dependencias que necesite la aplicación. Service Fabric copia el contenido del paquete de aplicación en todos los nodos del clúster en los que se implementarán los servicios de la aplicación. El paquete debe contener todo el código que la aplicación necesita para ejecutarse. No dé por hecho que las dependencias ya están instaladas.
>
>

### <a name="edit-the-service-manifest-file"></a>Edición del archivo de manifiesto de servicio
El siguiente paso consiste en editar el archivo de manifiesto de servicio para incluir la siguiente información:

* El nombre del tipo de servicio Se trata de un identificador que Service Fabric usa para identificar un servicio.
* El comando que se usa para iniciar la aplicación (ExeHost)
* Cualquier script que se tenga que ejecutar para instalar y configurar la aplicación (SetupEntrypoint).

A continuación, se muestra un ejemplo de un archivo `ServiceManifest.xml` :

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

En las siguientes secciones se repasan las diferentes partes del archivo que tiene que actualizar.

#### <a name="update-servicetypes"></a>Actualización de ServiceTypes
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Puede elegir cualquier nombre que desee para `ServiceTypeName`. El valor se usa en el archivo `ApplicationManifest.xml` para identificar el servicio.
* Especifique `UseImplicitHost="true"`. Este atributo indica a Service Fabric que el servicio se basa en una aplicación independiente, por lo que lo único que necesita hacer Service Fabric es iniciarla como un proceso y supervisar su estado.

#### <a name="update-codepackage"></a>Actualización de CodePackage
El elemento CodePackage especifica la ubicación (y versión) de código del servicio.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

El elemento `Name` se usa para especificar el nombre del directorio en el paquete de aplicación que contiene el código del servicio. `CodePackage` también tiene el atributo `version`. Esto puede usarse para especificar la versión del código y también se podría usar para actualizar el código del servicio mediante la infraestructura de administración de ciclo de vida de aplicación de Service Fabric.

#### <a name="optional-update-setupentrypoint"></a>Opcional: Actualización de SetupEntryPoint
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
El elemento SetupEntryPoint se usa para especificar cualquier archivo ejecutable o por lotes que se deba ejecutar antes de iniciar el código del servicio. Se trata de un paso opcional, por lo que no necesita incluirse si no hay ninguna inicialización obligatoria. SetupEntrypoint se ejecuta cada vez que se reinicia el servicio.

Hay un solo elemento SetupEntrypoint, por lo que los scripts de configuración deben agruparse en un solo archivo por lotes si la configuración de la aplicación requiere varios scripts. SetupEntrypoint puede ejecutar cualquier tipo de archivo: archivos ejecutables, archivos por lotes o cmdlets de PowerShell. Para más información, vea cómo [configurar SetupEntryPoint](service-fabric-application-runas-security.md).

En el ejemplo anterior, SetupEntrypoint se ejecuta en un archivo por lotes llamado `LaunchConfig.cmd` que se encuentra en el subdirectorio `scripts` del directorio Code (suponiendo que el elemento WorkingFolder esté establecido en CodeBase).

#### <a name="update-entrypoint"></a>Actualización de EntryPoint
```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

El elemento `EntryPoint` del archivo de manifiesto de servicio se usa para especificar cómo se inicia el servicio. El elemento `ExeHost` especifica el archivo ejecutable (y los argumentos) que deben usarse para iniciar el servicio.

* `Program` especifica el nombre del ejecutable que debe iniciar el servicio.
* `Arguments` especifica los argumentos que se deben pasar al archivo ejecutable. Puede ser una lista de parámetros con argumentos.
* `WorkingFolder` especifica el directorio de trabajo para el proceso que se va a iniciar. Puede especificar tres valores:
  * `CodeBase` especifica que el directorio de trabajo se va a establecer en el directorio Code del paquete de aplicación (directorio `Code` en la estructura de archivos anterior).
  * `CodePackage` especifica que el directorio de trabajo se va a establecer en la raíz del paquete de aplicación (`GuestService1Pkg` en la estructura de archivos anterior).
    * `Work` especifica que los archivos se colocan en un subdirectorio denominado Work.

WorkingFolder resulta útil para establecer el directorio de trabajo correcto, de modo que tanto los scripts de aplicación como los de inicialización puedan usar rutas de acceso relativas.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Actualización de puntos de conexión y registro mediante el servicio de nombres para la comunicación
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
En el ejemplo anterior, el elemento `Endpoint` especifica los puntos de conexión en los que puede escuchar la aplicación. En este ejemplo, la aplicación Node.js escucha en http en el puerto 3000.

Además, puede pedir a Service Fabric que publique este punto de conexión en el servicio de nombres para que otros servicios puedan detectar la dirección del punto de conexión para este servicio. Esto le permite comunicarse entre los servicios que sean ejecutables invitados.
La dirección del punto de conexión publicada tiene el formato `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme` y `PathSuffix` son atributos opcionales. `IPAddressOrFQDN` es la dirección IP o el nombre de dominio completo del nodo en el que se coloca este ejecutable y que se calcula automáticamente.

En el ejemplo siguiente, una vez implementado el servicio, en Service Fabric Explorer verá un punto de conexión similar a `http://10.1.4.92:3000/myapp/` publicado para la instancia de servicio. Si se trata de un equipo local, verá `http://localhost:3000/myapp/`.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Puede usar estas direcciones con el [proxy inverso](service-fabric-reverseproxy.md) para comunicarse entre servicios.

### <a name="edit-the-application-manifest-file"></a>Edición del archivo de manifiesto de aplicación
Una vez que haya configurado el archivo `Servicemanifest.xml`, tendrá que realizar algunos cambios en el archivo `ApplicationManifest.xml` para asegurarse de que se use el tipo de servicio y el nombre correctos.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport
En el elemento `ServiceManifestImport` , puede especificar uno o varios servicios para incluirlos en la aplicación. Se hace referencia a los servicios con `ServiceManifestName`, que especifica el nombre del directorio donde se encuentra el archivo `ServiceManifest.xml`.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Configuración de los registros
Para los ejecutables invitados, resulta útil poder ver los registros de la consola para averiguar si los scripts de la configuración y la aplicación muestran algún error.
Se puede configurar el redireccionamiento de la consola en el archivo `ServiceManifest.xml` usando el elemento `ConsoleRedirection`.

> [!WARNING]
> No use nunca la directiva de redirección de la consola en una aplicación implementada en producción, ya que esto puede afectar a la capacidad de conmutación por error de la aplicación. *Solo* debe usarla con fines de depuración y desarrollo local.  
>
>

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

`ConsoleRedirection` puede utilizarse para redirigir las salidas de consola (stdout y stderr) a un directorio de trabajo. Esto permite comprobar que no hay ningún error durante la configuración o la ejecución de la aplicación en el clúster de Service Fabric.

`FileRetentionCount` determina cuántos archivos se guardan en el directorio de trabajo. Un valor de 5 por ejemplo, significa que se almacenarán los archivos de registro de las cinco ejecuciones anteriores en el directorio de trabajo.

`FileMaxSizeInKb` especifica el tamaño máximo de los archivos de registro.

Los archivos de registro se guardan en uno de los directorios de trabajo del servicio. Para saber dónde se encuentran los archivos, utilice Service Fabric Explorer para determinar cuál es el nodo en el que se está ejecutando el servicio y cuál es el directorio de trabajo que se está usando. Este proceso se trata más adelante en este artículo.

## <a name="deployment"></a>Implementación
El último paso consiste en [implementar la aplicación](service-fabric-deploy-remove-applications.md). El siguiente script de PowerShell muestra cómo implementar la aplicación en el clúster de desarrollo local e iniciar un nuevo servicio de Service Fabric.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> [Comprima el paquete](service-fabric-package-apps.md#compress-a-package) antes de copiarlo en el almacén de imágenes en caso de que el primero sea grande o tenga muchos archivos. Obtenga más información [aquí](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

Se puede implementar un servicio Service Fabric en diversas "configuraciones". Por ejemplo, puede implementarse como una o varias instancias o puede implementarse de manera que haya una instancia del servicio en cada nodo del clúster de Service Fabric.

El parámetro `InstanceCount` del cmdlet `New-ServiceFabricService` se usa para especificar cuántas instancias del servicio deben iniciarse en el clúster de Service Fabric. Puede establecer el valor `InstanceCount` según el tipo de aplicación que se vaya a implementar. Los dos escenarios más comunes son:

* `InstanceCount = "1"`. En este caso, solo se implementa una instancia del servicio en el clúster. El programador de Service Fabric determina en qué nodo se va a implementar el servicio.
* `InstanceCount ="-1"`. En este caso, se implementa una instancia del servicio en cada nodo del clúster de Service Fabric. El resultado final tiene una instancia (y solo una) del servicio para cada nodo del clúster.

Se trata de una configuración útil para las aplicaciones front-end (por ejemplo, un punto de conexión REST) porque las aplicaciones cliente necesitan "conectarse" a cualquiera de los nodos del clúster para usar el punto de conexión. También se puede usar esta configuración cuando, por ejemplo, todos los nodos del clúster de Service Fabric están conectados a un equilibrador de carga. A continuación, se puede distribuir el tráfico del cliente en el servicio que se ejecuta en todos los nodos del clúster.

## <a name="check-your-running-application"></a>Comprobación de la aplicación en ejecución
En el explorador de Service Fabric, identifique el nodo en el que se está ejecutando el servicio. En este ejemplo, se ejecuta en el nodo 1:

![Nodo donde se ejecuta el servicio](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Si navega hasta el nodo y accede a la aplicación, ve la información esencial del nodo, incluida su ubicación en el disco.

![Ubicación en el disco](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Si va al directorio mediante el Explorador de servidores, puede encontrar el directorio de trabajo y la carpeta de registros del servicio, tal y como se muestra en la captura de pantalla siguiente: 

![Ubicación del registro](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido a empaquetar un ejecutable invitado y a implementarlo en Service Fabric. Consulte los artículos siguientes para obtener información relacionada y tareas.

* [Ejemplo para empaquetar e implementar un ejecutable invitado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), que incluye un vínculo a la versión preliminar de la herramienta de empaquetado
* [Ejemplo de dos ejecutables invitados (C# y Node.js) que se comunican a través del servicio de nombres con REST](https://github.com/Azure-Samples/service-fabric-containers)
* [Implementación de varios ejecutables invitados](service-fabric-deploy-multiple-apps.md)
* [Creación de la primera aplicación de Service Fabric en Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
