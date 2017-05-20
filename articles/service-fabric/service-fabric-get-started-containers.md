---
title: "Creación de una aplicación contenedora en Azure Service Fabric | Microsoft Docs"
description: "Cree la primera aplicación contenedora en Azure Service Fabric.  Cree una imagen de Docker con una aplicación, inserte la imagen en un registro de contenedor y compile e implemente una aplicación contenedora en Service Fabric."
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
ms.date: 05/08/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: acb68b274228aa647dc7be5d36b2b077bd213c1b
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---

# <a name="create-your-first-service-fabric-container-app"></a>Creación de la primera aplicación contenedora en Service Fabric
La ejecución de una aplicación existente de un contenedor de Windows en un clúster de Service Fabric no requiere que se realice ningún cambio en la aplicación. Este manual de inicio rápido le guiará por la creación de una imagen de Docker que contenga una aplicación web, la inserción de una nueva imagen en Azure Container Registry, la creación de una aplicación contenedora en Service Fabric y la implementación de la aplicación contenedora en un clúster de Service Fabric.  Este artículo supone que el usuario tiene un conocimiento básico de Docker. Para obtener información acerca de Docker, lea la [introducción a Docker](https://docs.docker.com/engine/understanding-docker/).

## <a name="prerequisites"></a>Requisitos previos
Un equipo de desarrollo en el que se ejecute:
* Visual Studio 2015 o Visual Studio 2017.
* [SDK y herramientas de Service Fabric](service-fabric-get-started.md).
*  Docker para Windows.  [Obtener Docker CE para Windows (estable)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Después de instalar e iniciar Docker, haga clic con el botón derecho en el icono de la bandeja y seleccione **Switch to Windows containers** (Conmutar a contenedores de Windows), algo que es necesario para ejecutar imágenes de Docker basadas en Windows.

Un clúster de Windows con tres, o más, nodos que se ejecute en Windows Server 2016 con contenedores: [Creación del primer clúster de Service Fabric en Azure](service-fabric-get-started-azure-cluster.md) o [Prueba gratis de Service Fabric](http://tryazureservicefabric.westus.cloudapp.azure.com/). 

Un registro de Azure Container Registry: [cree un registro de contenedor](../container-registry/container-registry-get-started-portal.md) en la suscripción de Azure. 

## <a name="create-a-simple-web-app"></a>Creación de una aplicación web simple
Recopile todos los recursos que necesita cargar en una imagen de Docker en un solo lugar. Para esta guía de inicio rápido, cree una aplicación web "Hola mundo" en el equipo de desarrollo.

1. Cree un directorio, como *c:\temp\helloworldapp*.
2. Cree un subdirectorio *c:\temp\helloworldapp\content*.
3. Cree el archivo *index.html* en *c:\temp\helloworldapp\content*.
4. Edite *index.html* y agregue la línea siguiente:
    ```
    <h1>Hello World!</h1>
    ```
5. Guarde los cambios en *index.html*.

## <a name="build-the-docker-image"></a>Compilación de la imagen de Docker
Compile una imagen basada en la [imagen de microsft/iis](https://hub.docker.com/r/microsoft/iis/) ubicada en Docker Hub. La imagen de microsoft/iis deriva de la imagen del sistema operativo base Windows Server Core y contiene Internet Information Services (IIS).  La ejecución de esta imagen en el contenedor inicia automáticamente IIS y los sitios web instalados.

Defina la imagen de Docker en un Dockerfile. El Dockerfile contiene instrucciones para crear la imagen y cargar la aplicación que desea ejecutar. El Dockerfile es la entrada para el comando ```docker build```, que crea la imagen. 

1. Cree un archivo *Dockerfile* (sin extensión de archivo) en *c:\temp\helloworldapp* y agregue lo siguiente:

    ```
    # The `FROM` instruction specifies the base image. You are
    # extending the `microsoft/iis` image.
    FROM microsoft/iis

    # Create a directory to hold the web app in the container.
    RUN mkdir C:\site

    # Create a new IIS site.
    RUN powershell -NoProfile -Command \
        Import-module IISAdministration; \
        New-IISSite -Name "Site" -PhysicalPath C:\site -BindingInformation "*:8000:"

    # Opens port 8000 on the container.
    EXPOSE 8000

    # The final instruction copies the web app you created earlier into the container.
    ADD content/ /site
    ```

    No hay ningún comando ```ENTRYPOINT``` en este Dockerfile. No lo necesita. Cuando se ejecuta Windows Server con IIS, el proceso de IIS es el punto de partida, que está configurado para iniciarse en la imagen base.

    Para más información, lea la [referencia de Dockerfile](https://docs.docker.com/engine/reference/builder/).

2. Ejecute el comando ```docker build``` para crear la imagen que ejecuta la aplicación web. Abra una ventana de PowerShell y navegue hasta *c:\temp\helloworldapp*. Ejecute el siguiente comando:

    ```
    docker build -t helloworldapp .
    ```
    Este comando crea la imagen nueva con las instrucciones del Dockerfile y asígnele el nombre (etiqueta -t) "helloworldapp" a la imagen. La creación de una imagen extrae la imagen base de Docker Hub y crea una nueva imagen que agrega la aplicación sobre la imagen base.  La [imagen de microsft/iis](https://hub.docker.com/r/microsoft/iis/) y las imágenes base del sistema operativo ocupan 10,5 GB y tardan un tiempo en descargarse y extraerse en el equipo de desarrollo.  Mientras tanto, puede realizar otras tareas.  La descarga tarda menos si previamente ha extraído la imagen del sistema operativo base al equipo de desarrollo.

3. Una vez que se complete el comando de compilación, ejecute el comando `docker images` para ver información sobre la nueva imagen:

    ```
    docker images
    
    REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
    helloworldapp              latest              86838648aab6        2 minutes ago       10.1 GB
    ```

## <a name="verify-the-image-runs-locally"></a>Comprobación de que la imagen se ejecuta localmente
Compruebe la imagen localmente antes de insertarla en el registro de contenedor.  

1. Inicie el contenedor con ```docker run```:

    ```
    docker run -d -p 8000:8000 --name my-web-site helloworldapp
    ```

    *name* asigna un nombre al contenedor en ejecución (en lugar del identificador del contenedor).

2. Una vez que el contenedor se inicia, busque su dirección IP para que pueda conectarse a su contenedor en ejecución desde un explorador:
    ```
    docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
    ```

3. Conéctese al contenedor en ejecución.  Abra un explorador web que apunte a la dirección IP que se devuelve en el puerto 8000; por ejemplo, "http://172.31.194.61:8000". Debería ver que el título "¡Hola mundo!" se muestra en el explorador.

4. Para detener el contenedor, ejecute:

    ```
    docker stop my-web-site
    ```

5. Elimine el contenedor del equipo de desarrollo:

    ```
    docker rm my-web-site
    ```

## <a name="push-the-image-to-the-container-registry"></a>Inserción de la imagen en el registro de contenedor
Después de comprobar que el contenedor se ejecuta en el equipo de desarrollo, inserte la imagen en el registro de Azure Container Registry.

1. Ejecute ``docker login`` para iniciar sesión en el registro de contenedor con sus [credenciales de registro](../container-registry/container-registry-authentication.md).

    En el ejemplo siguiente se pasa el identificador y la contraseña de una [entidad de servicio](../active-directory/active-directory-application-objects.md) de Azure Active Directory. Por ejemplo, puede que haya asignado una entidad de servicio al registro para ver un escenario de automatización.

    ```
    docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
    ```

2. El siguiente comando crea una etiqueta, o alias, de la imagen, con una ruta de acceso completa al registro. Este ejemplo coloca la imagen en el espacio de nombres ```samples``` para evitar el desorden en la raíz del registro.

    ```
    docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
    ```

3.  Inserte la imagen en el registro de contenedor:

    ```
    docker push myregistry.azurecr.io/samples/helloworldapp
    ```

## <a name="create-and-package-the-containerized-service-in-visual-studio"></a>Creación y empaquetado del servicio en contenedor en Visual Studio
Las herramientas y el SDK de Service Fabric proporcionan una plantilla de servicio que le ayuda a implementar un contenedor en un clúster de Service Fabric.

1. Inicie Visual Studio.  Seleccione **Archivo** > **Nuevo** > **Proyecto**.
2. Seleccione **Aplicación de Service Fabric**, asígnele el nombre "MyFirstContainer" y haga clic en **Aceptar**.
3. Seleccione **Contenedor de invitado**  en la lista de **plantillas del servicio**.
4. En **Nombre de la imagen** escriba "myregistry.azurecr.io/samples/helloworldapp", la imagen que insertó en el repositorio de contenedor. 
5. Asigne un nombre a su servicio y haga clic en **Aceptar**.
6. Si el servicio en contenedor necesita un punto de conexión para la comunicación, ahora puede agregar el protocolo, puerto y tipo a un ```Endpoint``` en el archivo ServiceManifest.xml. Para esta guía de inicio rápido, el servicio en contenedor realiza escuchas en el puerto 80: 

    ```xml
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    ```
    Si se especifica ```UriScheme```, se registra automáticamente el punto de conexión del contenedor con el servicio de nombres de Service Fabric para facilitar la detección. Al final de este artículo se proporciona un archivo ServiceManifest.xml de ejemplo. 
7. Configure la asignación de puertos de puerto a host del contenedor, para lo que debe especificar una directiva de ```PortBinding``` en ```ContainerHostPolicies``` del archivo ApplicationManifest.xml.  Para esta guía de inicio rápido, ```ContainerPort``` es 8000 (el contenedor expone el puerto 8000, como se especifica en el Dockerfile) y ```EndpointRef``` es "Guest1TypeEndpoint" (el punto de conexión definido en el manifiesto de servicio).  Las solicitudes entrantes para el servicio en el puerto 80 se asignan al puerto 8000 del contenedor.  Si el contenedor necesita autenticarse con un repositorio privado, agregue ```RepositoryCredentials```.  Para esta guía de inicio rápido, agregue el nombre de cuenta y la contraseña del registro de contenedor myregistry.azurecr.io. 

    ```xml
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ```

    Al final de este artículo se proporciona un archivo ApplicationManifest.xml de ejemplo.
8. Configure el punto de conexión del clúster para que puede publicar la aplicación en el clúster.  El punto de conexión del cliente se puede encontrar en la hoja de información general del clúster en [Azure Portal](https://portal.azure.com). En el Explorador de soluciones, abra *Cloud.xml* en **MyFirstContainer**->**PublishProfiles**.  Agregue el nombre del clúster y el puerto de la conexión a **ClusterConnectionParameters**.  Por ejemplo:
    ```xml
    <ClusterConnectionParameters ConnectionEndpoint="containercluster.westus2.cloudapp.azure.com:19000" />
    ```
    
9. Guarde todos los archivos y compile el proyecto.  

10. Para empaquetar la aplicación, haga clic con el botón derecho en **MyFirstContainer** en el Explorador de soluciones y seleccione **Empaquetar**. 

## <a name="deploy-the-container-app"></a>Implementación de la aplicación contenedora
1. Para publicar una aplicación, haga clic con el botón derecho en **MyFirstContainer** en el Explorador de soluciones y seleccione **Publicar**.

2. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) es una herramienta web para inspeccionar y administrar aplicaciones y nodos en un clúster de Service Fabric. Abra un explorador, navegue hasta http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/ y siga la implementación de la aplicación.  La aplicación se implementa, pero está en estado de error hasta que la imagen se descarga en los nodos del clúster (lo que pueden tardar un tiempo, según el tamaño de imagen):  ![Error][1]

3. La aplicación está lista cuando está en el estado ```Ready```:  ![Ready][2]

4. Abra un explorador, navegue hasta http://containercluster.westus2.cloudapp.azure.com. Debería ver que el título "¡Hola mundo!" se muestra en el explorador.

## <a name="clean-up"></a>Limpieza
Mientras el clúster esté en ejecución seguirá generando cargos, así que debería considerar la posibilidad de [eliminar el clúster](service-fabric-get-started-azure-cluster.md#remove-the-cluster).  Los [clústeres de la entidad](http://tryazureservicefabric.westus.cloudapp.azure.com/) se eliminan automáticamente a las pocas horas.

Después de insertar la imagen en el registro de contenedor puede eliminar la imagen local del equipo de desarrollo:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Manifiestos de servicio y de aplicación de Service Fabric de ejemplo completos
Estos son los manifiestos de servicio y de aplicación completos que se usan en esta guía de inicio rápido.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    <!--
    <EnvironmentVariables>
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
    </EnvironmentVariables>
    -->
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="next-steps"></a>Pasos siguientes
* Más información acerca de cómo ejecutar [contenedores en Service Fabric](service-fabric-containers-overview.md).
* Más información acerca del [ciclo de vida de aplicaciones](service-fabric-application-lifecycle.md) de Service Fabric.
* Consulte [los ejemplos de código de contenedor de Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-containers) en GitHub.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png

