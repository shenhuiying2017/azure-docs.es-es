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
ms.date: 06/28/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 35b7e0a730d73f646462b9cde3c8bbabac4d7c67
ms.contentlocale: es-es
ms.lasthandoff: 08/16/2017

---

# <a name="create-your-first-service-fabric-container-application-on-linux"></a>Cree la primera aplicación contenedora de Service Fabric en Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

La ejecución de una aplicación existente en un contenedor de Linux en un clúster de Service Fabric no requiere que se realice ningún cambio en la aplicación. Este artículo le guiará por la creación de una imagen de Docker que contiene una aplicación web [Flask](http://flask.pocoo.org/) en Python y su implementación en un clúster de Service Fabric.  También compartirá la aplicación en el contenedor mediante [Azure Container Registry](/azure/container-registry/).  Este artículo supone que el usuario tiene un conocimiento básico de Docker. Para obtener información acerca de Docker, lea la [introducción a Docker](https://docs.docker.com/engine/understanding-docker/).

## <a name="prerequisites"></a>Requisitos previos
* Un equipo de desarrollo en el que se ejecute:
  * [SDK y herramientas de Service Fabric](service-fabric-get-started-linux.md).
  * [Docker CE para Linux](https://docs.docker.com/engine/installation/#prior-releases). 

* Un registro de Azure Container Registry: [cree un registro de contenedor](../container-registry/container-registry-get-started-portal.md) en la suscripción de Azure. 

## <a name="define-the-docker-container"></a>Definición del contenedor de Docker
Compile una imagen basada en la [imagen de Python](https://hub.docker.com/_/python/) ubicada en Docker Hub. 

Defina el contenedor de Docker en un archivo Dockerfile. El archivo Dockerfile contiene instrucciones para la configuración del entorno dentro del contenedor, la carga de la aplicación que desea ejecutar y la asignación de puertos. El Dockerfile es la entrada para el comando `docker build`, que crea la imagen. 

Cree un directorio vacío y cree el archivo *Dockerfile* (sin extensión de archivo). Agregue lo siguiente al archivo *Dockerfile* y guarde los cambios:

```
# Use an official Python runtime as a base image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Para más información, lea la [referencia de Dockerfile](https://docs.docker.com/engine/reference/builder/).

## <a name="create-a-simple-web-application"></a>Creación de una aplicación web simple
Cree una aplicación web de Flask que escucha en el puerto 80 y devuelve "Hola mundo".  En el mismo directorio, cree el archivo *requirements.txt*.  Agregue lo siguiente y guarde los cambios:
```
Flask
```

Cree también el archivo *app.py* y agregue lo siguiente:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    
    return 'Hello World!'

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

## <a name="build-the-image"></a>Compilación de la imagen
Ejecute el comando `docker build` para crear la imagen que ejecuta la aplicación web. Abra una ventana de PowerShell y navegue hasta *c:\temp\helloworldapp*. Ejecute el siguiente comando:

```bash
docker build -t helloworldapp .
```

Este comando crea la imagen nueva con las instrucciones del Dockerfile y asígnele el nombre (etiqueta -t) "helloworldapp" a la imagen. La creación de una imagen extrae la imagen base de Docker Hub y crea una nueva imagen que agrega la aplicación sobre la imagen base.  

Una vez que se complete el comando de compilación, ejecute el comando `docker images` para ver información sobre la nueva imagen:

```bash
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              86838648aab6        2 minutes ago       194 MB
```

## <a name="run-the-application-locally"></a>Ejecución de la aplicación de forma local
Compruebe que la aplicación en contenedor se ejecuta localmente antes de insertarla en el registro de contenedor.  

Ejecute la aplicación, asignando el puerto 4000 del equipo al puerto 80 expuesto del contenedor:

```bash
docker run -d -p 4000:80 --name my-web-site helloworldapp
```

*name* asigna un nombre al contenedor en ejecución (en lugar del identificador del contenedor).

Conéctese al contenedor en ejecución.  Abra un explorador web que apunte a la dirección IP que se devuelve en el puerto 4000; por ejemplo, "http://localhost:4000". Debería ver que el título "¡Hola mundo!" se muestra en el explorador.

!["¡Hola mundo!"][hello-world]

Para detener el contenedor, ejecute:

```bash
docker stop my-web-site
```

Elimine el contenedor del equipo de desarrollo:

```bash
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>Inserción de la imagen en el registro de contenedor
Después de comprobar que la aplicación se ejecuta en Docker, inserte la imagen en el registro de Azure Container Registry.

Ejecute `docker login` para iniciar sesión en el registro de contenedor con sus [credenciales de registro](../container-registry/container-registry-authentication.md).

En el ejemplo siguiente se pasa el identificador y la contraseña de una [entidad de servicio](../active-directory/active-directory-application-objects.md) de Azure Active Directory. Por ejemplo, puede que haya asignado una entidad de servicio al registro para ver un escenario de automatización.  O bien, puede iniciar sesión con su nombre de usuario y contraseña del registro.

```bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

El siguiente comando crea una etiqueta, o alias, de la imagen, con una ruta de acceso completa al registro. Este ejemplo coloca la imagen en el espacio de nombres `samples` para evitar el desorden en la raíz del registro.

```bash
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Inserte la imagen en el registro de contenedor:

```bash
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="package-the-docker-image-with-yeoman"></a>Empaquetamiento de la imagen de Docker con Yeoman
El SDK de Service Fabric para Linux incluye un generador [Yeoman](http://yeoman.io/) que permite crear fácilmente la aplicación y agregar una imagen de contenedor. Vamos a usar Yeoman para crear una aplicación con un único contenedor de Docker denominado "*SimpleContainerApp*".

Para crear una aplicación contenedora de Service Fabric, abra una ventana de terminal y ejecute `yo azuresfcontainer`.  

Dé nombre a la aplicación (por ejemplo, "mycontainer"). 

Proporcione la dirección URL de la imagen de contenedor de un registro de contenedor (por ejemplo, ""). 

Esta imagen tiene un punto de entrada de carga de trabajo definido, por lo que deberá especificar explícitamente comandos de entrada (que se ejecutarán dentro del contenedor, lo que mantendrá el contenedor en ejecución después del inicio). 

Especifique un número de instancias de "1".

![Generador Yeoman de Service Fabric para contenedores][sf-yeoman]

## <a name="configure-port-mapping-and-container-repository-authentication"></a>Configuración de la asignación de puertos y la autenticación de repositorios de contenedor
El servicio en contenedor necesita un punto de conexión para la comunicación.  Agregue el protocolo, el puerto y el tipo a un `Endpoint` en el archivo ServiceManifest.xml. Para este artículo, el servicio en contenedor realiza escuchas en el puerto 4000: 

```xml
<Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
```
Si se especifica `UriScheme`, se registra automáticamente el punto de conexión del contenedor con el servicio de nombres de Service Fabric para facilitar la detección. Al final de este artículo se proporciona un archivo ServiceManifest.xml de ejemplo. 

Configure la asignación de puertos de puerto a host del contenedor, para lo que debe especificar una directiva de `PortBinding` en `ContainerHostPolicies` del archivo ApplicationManifest.xml.  Para este artículo, `ContainerPort` es 80 (el contenedor expone el puerto 80, como se especifica en el Dockerfile) y `EndpointRef` es "myserviceTypeEndpoint" (el punto de conexión definido en el manifiesto de servicio).  Las solicitudes entrantes para el servicio en el puerto 4000 se asignan al puerto 80 del contenedor.  Si el contenedor necesita autenticarse con un repositorio privado, agregue `RepositoryCredentials`.  Para este artículo, agregue el nombre de cuenta y la contraseña del registro de contenedor myregistry.azurecr.io. 

```xml
<Policies>
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
    </ContainerHostPolicies>
</Policies>
```

## <a name="build-and-package-the-service-fabric-application"></a>Creación y empaquetamiento de la aplicación de Service Fabric
Las plantillas de Yeoman de Service Fabric incluyen un script de compilación para [Gradle](https://gradle.org/), que se puede usar para compilar la aplicación desde el terminal. Para compilar y empaquetar la aplicación, ejecute el siguiente código:

```bash
cd mycontainer
gradle
```

## <a name="deploy-the-application"></a>Implementación de la aplicación
Una vez compilada la aplicación, puede implementarla en el clúster local mediante la CLI de Azure.

Conéctese al clúster de Service Fabric local.

```bash
azure servicefabric cluster connect
```

Use el script de instalación proporcionado en la plantilla para copiar el paquete de aplicación en el almacén de imágenes del clúster, registrar el tipo de aplicación y crear una instancia de la aplicación.

```bash
./install.sh
```

Abra un explorador y vaya a Service Fabric Explorer en http://localhost:19080/Explorer (reemplace localhost por la dirección IP privada de la VM si usa Vagrant en Mac OS X). Expanda el nodo Applications y observe que ahora hay una entrada para su tipo de aplicación y otra para la primera instancia de ese tipo.

Conéctese al contenedor en ejecución.  Abra un explorador web que apunte a la dirección IP que se devuelve en el puerto 4000; por ejemplo, "http://localhost:4000". Debería ver que el título "¡Hola mundo!" se muestra en el explorador.

!["¡Hola mundo!"][hello-world]

## <a name="clean-up"></a>Limpieza
Para eliminar la instancia de aplicación del clúster de desarrollo local y anular el registro del tipo de aplicación, utilice el script de desinstalación proporcionado en la plantilla.

```bash
./uninstall.sh
```

Después de insertar la imagen en el registro de contenedor puede eliminar la imagen local del equipo de desarrollo:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Manifiestos de servicio y de aplicación de Service Fabric de ejemplo completos
Estos son los manifiestos de servicio y de aplicación completos que se usan en este artículo.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="myservicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="myserviceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers 
      to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    
    <EnvironmentVariables>
      <!--
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
      -->
    </EnvironmentVariables>
    
  </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="mycontainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="myservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="myservice">
      <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
      cluster, set InstanceCount to -1 for the container service to run on every node in 
      the cluster.
      -->
      <StatelessService ServiceTypeName="myserviceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="adding-more-services-to-an-existing-application"></a>Incorporación de más servicios a una aplicación existente

Para agregar otro servicio de contenedor a una aplicación ya creada mediante yeoman, realice los pasos siguientes:

1. Cambie el directorio al directorio raíz de la aplicación existente.  Por ejemplo, `cd ~/YeomanSamples/MyApplication`, si `MyApplication` es la aplicación creada por Yeoman.
2. Ejecute `yo azuresfcontainer:AddService`

<a id="manually"></a>


## <a name="configure-time-interval-before-container-is-force-terminated"></a>Configuración del intervalo de tiempo antes de hacer que se termine el contenedor

Puede configurar un intervalo de tiempo para que el entorno de ejecución espere antes de que el contenedor se quite una vez que la eliminación del servicio (o el movimiento a otro nodo) se haya iniciado. Si se configura el intervalo de tiempo, se envía el comando `docker stop <time in seconds>` al contenedor.   Para más detalles, consulte [docker stop](https://docs.docker.com/engine/reference/commandline/stop/). El intervalo de tiempo de espera se especifica en la sección `Hosting`. El siguiente fragmento de manifiesto de clúster muestra cómo establecer el intervalo de espera:

```xml
{
        "name": "Hosting",
        "parameters": [
          {
            "ContainerDeactivationTimeout": "10",
          ...
          }
        ]
}
```
El intervalo de tiempo predeterminado se establece en diez segundos. Puesto que esta configuración es dinámica, una actualización de solo configuración en el clúster actualiza el tiempo de espera. 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Configuración del entorno de ejecución para quitar imágenes de contenedor sin usar

Puede configurar el clúster de Service Fabric para quitar del nodo las imágenes de contenedor sin usar. Esta configuración permite recuperar el espacio en disco si hay demasiadas imágenes de contenedor en el nodo.  Para habilitar esta característica, actualice la sección `Hosting` en el manifiesto de clúster, tal como se muestra en el fragmento siguiente: 


```xml
{
        "name": "Hosting",
        "parameters": [
          {
            "PruneContainerImages": “True”,
            "ContainerImagesToSkip": "microsoft/windowsservercore|microsoft/nanoserver|…",
          ...
          }
        ]
} 
```

Puede especificar las imágenes que no se deben eliminar en el parámetro `ContainerImagesToSkip`. 


## <a name="next-steps"></a>Pasos siguientes
* Más información acerca de cómo ejecutar [contenedores en Service Fabric](service-fabric-containers-overview.md).
* Consulte el tutorial [Implementación de una aplicación .NET en un contenedor](service-fabric-host-app-in-a-container.md).
* Más información acerca del [ciclo de vida de aplicaciones](service-fabric-application-lifecycle.md) de Service Fabric.
* Consulte [los ejemplos de código de contenedor de Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-containers) en GitHub.

[hello-world]: ./media/service-fabric-get-started-containers-linux/HelloWorld.png
[sf-yeoman]: ./media/service-fabric-get-started-containers-linux/YoSF.png

