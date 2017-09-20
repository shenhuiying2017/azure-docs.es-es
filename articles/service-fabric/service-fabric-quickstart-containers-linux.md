---
title: "Creación de una aplicación contenedora de Azure Service Fabric en Linux | Microsoft Docs"
description: "Cree la primera aplicación contenedora en Linux en Azure Service Fabric.  Cree una imagen de Docker con la aplicación, inserte la imagen en un registro de contenedor y compile e implemente una aplicación contenedora en Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: 78306672e812745fd1902ae264c2adea196ab721
ms.contentlocale: es-es
ms.lasthandoff: 09/20/2017

---

# <a name="deploy-a-service-fabric-linux-container-application-on-azure"></a>Implementación de una aplicación contenedora Linux de Service Fabric en Azure
Azure Service Fabric es una plataforma de sistemas distribuidos para implementar y administrar microservicios y contenedores escalables y confiables. 

La ejecución de una aplicación existente en un contenedor de Linux en un clúster de Service Fabric no requiere que se realice ningún cambio en la aplicación. Esta guía de inicio rápido muestra cómo implementar una imagen de contenedor Docker creada previamente en una aplicación de Service Fabric. Cuando haya terminado, tendrá un contenedor nginx en ejecución.  Esta guía de inicio rápido describe la implementación de un contenedor de Linux; lea [esta otra guía](service-fabric-quickstart-containers.md) para implementar un contenedor de Windows.

![Nginx][nginx]

En esta guía de inicio rápido, ha aprendido a hacer lo siguiente:
> [!div class="checklist"]
> * Empaquetado de un contenedor de imagen de Docker
> * Configuración de la comunicación
> * Creación y empaquetamiento de la aplicación de Service Fabric
> * Implementación de la aplicación contenedora en Azure

## <a name="prerequisites"></a>Requisitos previos
Instale el [SDK de Service Fabric, la CLI de Service Fabric y los generadores de plantilla yeoman de Service Fabric](service-fabric-get-started-linux.md).
  
## <a name="package-a-docker-image-container-with-yeoman"></a>Empaquetamiento de un contenedor de imagen de Docker con Yeoman
El SDK de Service Fabric para Linux incluye un generador [Yeoman](http://yeoman.io/) que permite crear fácilmente la aplicación y agregar una imagen de contenedor. 

Para crear una aplicación contenedora de Service Fabric, abra una ventana de terminal y ejecute `yo azuresfcontainer`.  

Asigne el nombre "MyFirstContainer" a la aplicación y el nombre del servicio de aplicación "MyContainerService".

Proporcione el nombre de la imagen del contenedor "nginx:latest" (la [imagen de contenedor de nginx](https://hub.docker.com/r/_/nginx/) en Docker Hub). 

Esta imagen tiene definido un punto de entrada a la carga de trabajo, por lo que debe especificar explícitamente los comandos de entrada. 

Especifique un número de instancias de "1".

![Generador Yeoman de Service Fabric para contenedores][sf-yeoman]

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Configuración de la comunicación y asignación del puerto "puerto a host" del contenedor
Configure un punto de conexión HTTP para que los clientes puedan comunicarse con el servicio.  Abra el archivo *./MyFirstContainer/MyContainerServicePkg/ServiceManifest.xml* y declare un recurso de punto de conexión en el elemento **ServiceManifest**.  Agregue el protocolo, el puerto y el nombre. En esta guía de inicio rápido, el servicio realiza escuchas en el puerto 80: 

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
    <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```
Si se especifica `UriScheme`, se registra automáticamente el punto de conexión del contenedor con el servicio de nombres de Service Fabric para facilitar la detección. Al final de este artículo se proporciona un archivo ServiceManifest.xml de ejemplo. 

Asigne un puerto de contenedor a un servicio `Endpoint` con una directiva `PortBinding` en el valor `ContainerHostPolicies` del archivo ApplicationManifest.xml.  En esta guía de inicio rápido, el valor de `ContainerPort` es 80 (el contenedor expone el puerto 80) y `EndpointRef` es "myserviceTypeEndpoint" (el punto de conexión definido antes en el manifiesto de servicio).  Las solicitudes entrantes para el servicio en el puerto 80 se asignan al puerto 80 del contenedor.  

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="build-and-package-the-service-fabric-application"></a>Creación y empaquetamiento de la aplicación de Service Fabric
Las plantillas de Yeoman de Service Fabric incluyen un script de compilación para [Gradle](https://gradle.org/), que se puede usar para compilar la aplicación desde el terminal. Guarde todos los cambios.  Para compilar y empaquetar la aplicación, ejecute el siguiente código:

```bash
cd MyFirstContainer
gradle
```
## <a name="create-a-cluster"></a>Crear un clúster
Para implementar la aplicación en un clúster de Azure, puede crear su propio clúster o usar un clúster de entidad.

Los clústeres de entidad son clústeres de Service Fabric gratuitos y de duración limitada, hospedados en Azure y ejecutados por el equipo de Service Fabric, donde cualquier usuario puede implementar aplicaciones y obtener información sobre la plataforma. Para obtener acceso a un clúster de entidad, [siga estas instrucciones](http://aka.ms/tryservicefabric).  

Para obtener información sobre cómo crear su propio clúster, vea [Creación del primer clúster de Service Fabric en Azure](service-fabric-get-started-azure-cluster.md).

Tome nota del punto de conexión, que se utiliza en el paso siguiente.

## <a name="deploy-the-application-to-azure"></a>Implementación de la aplicación en Azure
Una vez compilada la aplicación, puede implementarla en el clúster de Azure mediante la CLI de Service Fabric.

Conéctese al clúster de Service Fabric en Azure.

```bash
sfctl cluster select --endpoint http://lnxt10vkfz6.westus.cloudapp.azure.com:19080
```

Use el script de instalación proporcionado en la plantilla para copiar el paquete de aplicación en el almacén de imágenes del clúster, registrar el tipo de aplicación y crear una instancia de la aplicación.

```bash
./install.sh
```

Abra un explorador y navegue hasta Service Fabric Explorer en http://lnxt10vkfz6.westus.cloudapp.azure.com:19080/Explorer. Expanda el nodo Applications y observe que ahora hay una entrada para su tipo de aplicación y otra para la primera instancia de ese tipo.

![Service Fabric Explorer][sfx]

Conéctese al contenedor en ejecución.  Abra un explorador web que apunte a la dirección IP que se devuelve en el puerto 80; por ejemplo, "lnxt10vkfz6.westus.cloudapp.azure.com:80". Debería ver la página de bienvenida de nginx en el explorador.

![Nginx][nginx]

## <a name="clean-up"></a>Limpieza
Para eliminar la instancia de aplicación del clúster y anular el registro del tipo de aplicación, utilice el script de desinstalación proporcionado en la plantilla.

```bash
./uninstall.sh
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Manifiestos de servicio y de aplicación de Service Fabric de ejemplo completos
Estos son los manifiestos de servicio y de aplicación completos que se usan en esta guía de inicio rápido.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>nginx:latest</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables> 
      </EnvironmentVariables> 
   </CodePackage>
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>

```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest  ApplicationTypeName="MyFirstContainerType" ApplicationTypeVersion="1.0.0"
                      xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
   
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
   <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
   
   <DefaultServices>
      <Service Name="MyContainerService">
        <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
        cluster, set InstanceCount to -1 for the container service to run on every node in 
        the cluster.
        -->
        <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="1">
            <SingletonPartition />
        </StatelessService>
      </Service>
   </DefaultServices>
   
</ApplicationManifest>

```

## <a name="next-steps"></a>Pasos siguientes
En esta guía de inicio rápido, ha aprendido a hacer lo siguiente:
> [!div class="checklist"]
> * Empaquetado de un contenedor de imagen de Docker
> * Configuración de la comunicación
> * Creación y empaquetamiento de la aplicación de Service Fabric
> * Implementación de la aplicación contenedora en Azure

* Más información acerca de cómo ejecutar [contenedores en Service Fabric](service-fabric-containers-overview.md).
* Consulte el tutorial [Implementación de una aplicación .NET en un contenedor](service-fabric-host-app-in-a-container.md).
* Más información acerca del [ciclo de vida de aplicaciones](service-fabric-application-lifecycle.md) de Service Fabric.
* Consulte [los ejemplos de código de contenedor de Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-containers) en GitHub.

[sfx]: ./media/service-fabric-quickstart-containers-linux/SFX.png
[nginx]: ./media/service-fabric-quickstart-containers-linux/nginx.png
[sf-yeoman]: ./media/service-fabric-quickstart-containers-linux/YoSF.png

