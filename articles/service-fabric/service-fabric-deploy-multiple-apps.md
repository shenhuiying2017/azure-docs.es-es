---
title: "Implementación de una aplicación de Node.js que usa MongoDB | Microsoft Docs"
description: "Tutorial sobre cómo empaquetar varios ejecutables invitados para implementarlos en un clúster de Azure Service Fabric"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: b76bb756-c1ba-49f9-9666-e9807cf8f92f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/02/2017
ms.author: msfussell;mikhegn
ms.openlocfilehash: d7a37d7c04f85e9031cab52fa86026e56315c882
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="deploy-multiple-guest-executables"></a>Implementación de varios ejecutables invitados
En este artículo se muestra cómo empaquetar e implementar varios ejecutables invitados en Azure Service Fabric. Para crear e implementar un solo paquete de Service Fabric, lea el artículo sobre cómo [implementar un ejecutable invitado en Service Fabric](service-fabric-deploy-existing-app.md).

Aunque este tutorial muestra cómo implementar una aplicación con un front-end de Node.js que usa MongoDB como almacén de datos, puede aplicar estos pasos en cualquier aplicación que tenga dependencias en otra aplicación.   

Puede utilizar Visual Studio para generar el paquete de aplicación que contiene varios ejecutables invitados. Consulte [Uso de Visual Studio para empaquetar un ejecutable existente](service-fabric-deploy-existing-app.md). Después de haber agregado el primer ejecutable invitado, haga clic con el botón derecho en el proyecto de aplicación y seleccione el **Agregar->Nuevo servicio de Service Fabric** para agregar el segundo proyecto ejecutable invitado a la solución. Nota: Si decide vincular el origen en el proyecto de Visual Studio, mediante la compilación de la solución de Visual Studio, se asegurará de que el paquete de aplicación esté actualizado con los cambios en el origen. 

## <a name="samples"></a>Muestras
* [Ejemplo para empaquetar e implementar un archivo ejecutable invitado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ejemplo de dos ejecutables invitados (C# y Node.js) que se comunican a través del servicio de nombres con REST](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>Empaquetar manualmente la aplicación del ejecutable invitado múltiple
También puede empaquetar manualmente el ejecutable invitado. Para el empaquetado manual, este artículo usa la herramienta de empaquetado de Service Fabric, que está disponible en [http://aka.ms/servicefabricpacktool](http://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>Empaquetado de la aplicación Node.js
Este artículo asume que no tiene instalado Node.js en los nodos del clúster de Service Fabric. Por tanto, es preciso agregar Node.exe en el directorio raíz de la aplicación de nodo antes del empaquetado. La estructura del directorio de la aplicación Node.js (con marco web Express y un motor de plantillas Jade) debe ser similar a la siguiente:

```
|-- NodeApplication
    |-- bin
        |-- www
    |-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
    |-- public
        |-- images
        |-- etc.
    |-- routes
        |-- index.js
        |-- users.js
    |-- views
        |-- index.jade
        |-- etc.
    |-- app.js
    |-- package.json
    |-- node.exe
```

El siguiente paso consiste en crear un paquete de aplicación para la aplicación Node.js. El código siguiente crea un paquete de aplicación de Service Fabric que contiene la aplicación Node.js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

A continuación se muestra una descripción de los parámetros usados:

* **/source** señala al directorio de la aplicación que se debe empaquetar.
* **/target** define el directorio en el que se debe crear el paquete. Este directorio tiene que ser diferente del directorio de origen.
* **/appname** define el nombre de aplicación de la aplicación existente. Es importante comprender que esto se convierte en el nombre del servicio del manifiesto y no en el nombre de la aplicación de Service Fabric.
* **/exe** define el archivo ejecutable que debe iniciar Service Fabric, en este caso`node.exe`.
* **/ma** define el argumento que se usa para iniciar el archivo ejecutable. Como no está instalado Node.js, Service Fabric debe iniciar el servidor web de Node.js mediante la ejecución de `node.exe bin/www`.  `/ma:'bin/www'` indica a la herramienta de empaquetado que use `bin/www` como argumento para node.exe.
* **/AppType** define el nombre del tipo de aplicación de Service Fabric.

Si examina el directorio especificado en el parámetro /target, podrá ver que la herramienta ha creado un paquete de Service Fabric totalmente operativo, como se muestra a continuación:

```
|--[yourtargetdirectory]
    |-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
El archivo ServiceManifest.xml generado tiene ahora una sección que describe cómo se debe iniciar el servidor web de Node.js, tal como se muestra en el fragmento de código siguiente:

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
En este ejemplo, el servidor web de Node.js escucha el puerto 3000, por lo que tiene que actualizar la información del punto de conexión del archivo ServiceManifest.xml, tal como se muestra a continuación.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Empaquetado de la aplicación MongoDB
Ahora que ha empaquetado la aplicación Node.js, puede continuar y empaquetar MongoDB. Como se mencionó antes, los pasos que realizará ahora no son específicos de Node.js y MongoDB. De hecho, se aplican a todas las aplicaciones que están diseñadas para empaquetarse como una aplicación de Service Fabric.  

Para empaquetar MongoDB, debe asegurarse de que empaqueta Mongod.exe y Mongo.exe. Ambos archivos binarios se encuentran en el directorio `bin` del directorio de instalación de MongoDB. La estructura del directorio es similar a la siguiente.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Service Fabric debe iniciar MongoDB con un comando similar al siguiente, por lo que se debería usar el parámetro `/ma` al empaquetar MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Los datos no se conservan en caso de que se produzca un error de nodo si coloca el directorio de datos de MongoDB en el directorio local del nodo. Debe usar en este caso un almacenamiento duradero o implementar el conjunto de réplicas de MongoDB para evitar la pérdida de datos.  
>
>

En PowerShell o el shell de comandos se ejecuta la herramienta de empaquetado con los parámetros siguientes:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Para agregar MongoDB al paquete de aplicación de Service Fabric, deberá asegurarse de que el parámetro /target apunta al mismo directorio que ya contiene el manifiesto de la aplicación junto con la aplicación de Node.js. También debe asegurarse de que está utilizando el mismo nombre de ApplicationType.

Examinemos el directorio y la herramienta que se ha creado.

```
|--[yourtargetdirectory]
    |-- MyNodeApplication
    |-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Como se puede ver, la herramienta agregó una nueva carpeta MongoDB al directorio que contiene los archivos binarios de MongoDB. Si se abre el archivo `ApplicationManifest.xml` , puede ver que el paquete contiene ahora la aplicación de Node.js y MongoDB. El código siguiente muestra el contenido del manifiesto de aplicación.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

### <a name="publishing-the-application"></a>Publicación de la aplicación
El último paso consiste en publicar la aplicación en el clúster de Service Fabric local mediante los scripts de PowerShell siguientes:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Cuando la aplicación se publica correctamente en el clúster local, puede acceder a la aplicación de Node.js en el puerto que hemos especificado en el manifiesto de servicio de la aplicación de Node.js, por ejemplo http://localhost:3000.

En este tutorial, ha visto cómo empaquetar fácilmente dos aplicaciones existentes como una aplicación de Service Fabric. También ha aprendido cómo implementarla en Service Fabric para que pueda beneficiarse de algunas de las características de Service Fabric, como la alta disponibilidad y la integración del sistema de mantenimiento.


## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Adición de más archivos ejecutables de invitado a una aplicación existente mediante Yeoman en Linux

Para agregar otro servicio a una aplicación ya creada mediante `yo`, realice los pasos siguientes: 
1. Cambie el directorio al directorio raíz de la aplicación existente.  Por ejemplo, `cd ~/YeomanSamples/MyApplication`, si `MyApplication` es la aplicación creada por Yeoman.
2. Ejecute `yo azuresfguest:AddService` y proporcione la información necesaria.

## <a name="next-steps"></a>Pasos siguientes
* Obtener información acerca de la implementación de contenedores con [información general de Service Fabric y contenedores](service-fabric-containers-overview.md)
* [Ejemplo para empaquetar e implementar un archivo ejecutable invitado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Ejemplo de dos ejecutables invitados (C# y Node.js) que se comunican a través del servicio de nombres con REST](https://github.com/Azure-Samples/service-fabric-containers)
