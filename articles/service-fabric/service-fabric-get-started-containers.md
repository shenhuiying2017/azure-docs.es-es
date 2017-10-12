---
title: "Creación de una aplicación contenedora en Azure Service Fabric | Microsoft Docs"
description: "Cree la primera aplicación contenedora en Windows en Azure Service Fabric.  Cree una imagen de Docker con una aplicación en Python, inserte la imagen en un registro de contenedores y compile e implemente una aplicación contenedora en Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/18/2017
ms.author: ryanwi
ms.openlocfilehash: 025bde02b3f342ec3399d51819d1fa8a91f11374
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-service-fabric-container-application-on-windows"></a>Cree la primera aplicación contenedora en Service Fabric en Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Para ejecutar una aplicación que existe en un contenedor de Windows en un clúster de Service Fabric no hay que hacer ningún cambio en la aplicación. Este artículo le guiará por la creación de una imagen de Docker que contiene una aplicación web [Flask](http://flask.pocoo.org/) en Python y su implementación en un clúster de Service Fabric.  También compartirá la aplicación en el contenedor mediante [Azure Container Registry](/azure/container-registry/).  Este artículo supone que el usuario tiene un conocimiento básico de Docker. Para obtener información acerca de Docker, lea la [introducción a Docker](https://docs.docker.com/engine/understanding-docker/).

## <a name="prerequisites"></a>Requisitos previos
Un equipo de desarrollo en el que se ejecute:
* Visual Studio 2015 o Visual Studio 2017.
* [SDK y herramientas de Service Fabric](service-fabric-get-started.md).
*  Docker para Windows.  [Obtener Docker CE para Windows (estable)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Después de instalar e iniciar Docker, haga clic con el botón derecho en el icono de la bandeja y seleccione **Switch to Windows containers** (Conmutar a contenedores de Windows), algo que es necesario para ejecutar imágenes de Docker basadas en Windows.

Un clúster de Windows con tres, o más, nodos que se ejecute en Windows Server 2016 con contenedores: [Creación del primer clúster de Service Fabric en Azure](service-fabric-cluster-creation-via-portal.md) o [Prueba gratis de Service Fabric](https://aka.ms/tryservicefabric).

Un registro de Azure Container Registry: [cree un registro de contenedor](../container-registry/container-registry-get-started-portal.md) en la suscripción de Azure.

## <a name="define-the-docker-container"></a>Definición del contenedor de Docker
Compile una imagen basada en la [imagen de Python](https://hub.docker.com/_/python/) ubicada en Docker Hub.

Defina el contenedor de Docker en un archivo Dockerfile. El archivo Dockerfile contiene instrucciones para la configuración del entorno dentro del contenedor, la carga de la aplicación que desea ejecutar y la asignación de puertos. El Dockerfile es la entrada para el comando `docker build`, que crea la imagen.

Cree un directorio vacío y cree el archivo *Dockerfile* (sin extensión de archivo). Agregue lo siguiente al archivo *Dockerfile* y guarde los cambios:

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
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

<a id="Build-Containers"></a>
## <a name="build-the-image"></a>Compilación de la imagen
Ejecute el comando `docker build` para crear la imagen que ejecuta la aplicación web. Abra una ventana de PowerShell y navegue hasta el directorio que contiene el archivo Dockerfile. Ejecute el siguiente comando:

```
docker build -t helloworldapp .
```

Este comando crea la imagen nueva con las instrucciones del Dockerfile y asígnele el nombre (etiqueta -t) "helloworldapp" a la imagen. La creación de una imagen extrae la imagen base de Docker Hub y crea una nueva imagen que agrega la aplicación sobre la imagen base.  

Una vez que se complete el comando de compilación, ejecute el comando `docker images` para ver información sobre la nueva imagen:

```
$ docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>Ejecución de la aplicación de forma local
Compruebe la imagen localmente antes de insertarla en el registro de contenedor.  

Ejecute la aplicación:

```
docker run -d --name my-web-site helloworldapp
```

*name* asigna un nombre al contenedor en ejecución (en lugar del identificador del contenedor).

Una vez que el contenedor se inicia, busque su dirección IP para que pueda conectarse a su contenedor en ejecución desde un explorador:
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

Conéctese al contenedor en ejecución.  Abra un explorador web que apunte a la dirección IP devuelta; por ejemplo, "http://172.31.194.61". Debería ver que el título "¡Hola mundo!" se muestra en el explorador.

Para detener el contenedor, ejecute:

```
docker stop my-web-site
```

Elimine el contenedor del equipo de desarrollo:

```
docker rm my-web-site
```

<a id="Push-Containers"></a>
## <a name="push-the-image-to-the-container-registry"></a>Inserción de la imagen en el registro de contenedor
Después de comprobar que el contenedor se ejecuta en el equipo de desarrollo, inserte la imagen en el registro de Azure Container Registry.

Ejecute ``docker login`` para iniciar sesión en el registro de contenedor con sus [credenciales de registro](../container-registry/container-registry-authentication.md).

En el ejemplo siguiente se pasa el identificador y la contraseña de una [entidad de servicio](../active-directory/active-directory-application-objects.md) de Azure Active Directory. Por ejemplo, puede que haya asignado una entidad de servicio al registro para ver un escenario de automatización. O bien, puede iniciar sesión con su nombre de usuario y contraseña del registro.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

El siguiente comando crea una etiqueta, o alias, de la imagen, con una ruta de acceso completa al registro. Este ejemplo coloca la imagen en el espacio de nombres ```samples``` para evitar el desorden en la raíz del registro.

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Inserte la imagen en el registro de contenedor:

```
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="create-the-containerized-service-in-visual-studio"></a>Creación del servicio en contenedor en Visual Studio
Las herramientas y el SDK de Service Fabric proporcionan una plantilla de servicio que le ayuda a crear una aplicación en contenedor.

1. Inicie Visual Studio.  Seleccione **Archivo** > **Nuevo** > **Proyecto**.
2. Seleccione **Aplicación de Service Fabric**, asígnele el nombre "MyFirstContainer" y haga clic en **Aceptar**.
3. Seleccione **Contenedor de invitado**  en la lista de **plantillas del servicio**.
4. En **Nombre de la imagen** escriba "myregistry.azurecr.io/samples/helloworldapp", la imagen que insertó en el repositorio de contenedor.
5. Asigne un nombre a su servicio y haga clic en **Aceptar**.

## <a name="configure-communication"></a>Configuración de la comunicación
El servicio en contenedor necesita un punto de conexión para la comunicación. Agregar un elemento `Endpoint` con el protocolo, el puerto y el tipo en el archivo ServiceManifest.xml. Para este artículo, el servicio en contenedor realiza escuchas en el puerto 8081.  En este ejemplo, se usa un puerto fijo 8081.  Si no se especifica ningún puerto, se elige un puerto aleatorio dentro del intervalo de puertos de la aplicación.  

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
  </Endpoints>
</Resources>
```

Al definir un punto de conexión, Service Fabric publica el punto de conexión en el servicio de nomenclatura.  Otros servicios que se ejecuten en el clúster pueden resolver este contenedor.  También puede realizar la comunicación de contenedor a contenedor utilizando el [proxy inverso](service-fabric-reverseproxy.md).  La comunicación se establece al proporcionar el puerto de escucha HTTP del proxy inverso y el nombre de los servicios con los que desea comunicarse como variables de entorno.

## <a name="configure-and-set-environment-variables"></a>Configuración y establecimiento de variables de entorno
En el manifiesto de servicio, las variables de entorno se pueden especificar para cada paquete de código. Esta característica está disponible para todos los servicios, con independencia de si se implementan como contenedores, procesos o archivos ejecutables de invitado. Estos valores de variable de entorno se invalidan en el manifiesto de aplicación o se especifican durante la implementación como parámetros de la aplicación.

El siguiente fragmento de código XML del manifiesto de servicio muestra un ejemplo de cómo especificar variables de entorno para un paquete de código:
```xml
<CodePackage Name="Code" Version="1.0.0">
  ...
  <EnvironmentVariables>
    <EnvironmentVariable Name="HttpGatewayPort" Value=""/>    
  </EnvironmentVariables>
</CodePackage>
```

Estas variables de entorno se pueden invalidar en el manifiesto de aplicación:

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
  </EnvironmentOverrides>
  ...
</ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping-and-container-to-container-discovery"></a>Configurar la asignación de puerto de puerto a host del contenedor y la detección de contenedor a contenedor
Configurar un puerto de host que se utiliza para comunicarse con el contenedor. El enlace de puerto asigna el puerto en el que el servicio está escuchando dentro del contenedor a un puerto en el host. Agregar un elemento `PortBinding` en el elemento `ContainerHostPolicies` del archivo ApplicationManifest.xml.  Para este artículo, `ContainerPort` es 80 (el contenedor expone el puerto 80, como se especifica en el Dockerfile) y `EndpointRef` es "Guest1TypeEndpoint" (el punto de conexión definido previamente en el manifiesto de servicio).  Las solicitudes entrantes para el servicio en el puerto 8081 se asignan al puerto 80 del contenedor.

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="configure-container-registry-authentication"></a>Configurar la autenticación de Container Registry
Configure la autenticación de Container Registry mediante la adición de `RepositoryCredentials` a `ContainerHostPolicies` en el archivo ApplicationManifest.xml. Agregue la cuenta y contraseña para el registro de contenedores myregistry.azurecr.io, que permite al servicio descargar la imagen de contenedor del repositorio.

```xml
<Policies>
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
    </ContainerHostPolicies>
</Policies>
```

Se recomienda cifrar la contraseña del repositorio con un certificado de cifrado que se implementa en todos los nodos del clúster. Cuando Service Fabric implementa el paquete de servicio en el clúster, el certificado de cifrado se utiliza para descifrar el texto cifrado.  El cmdlet Invoke-ServiceFabricEncryptText se usa para crear el texto cifrado para la contraseña, que se agrega al archivo ApplicationManifest.xml.

El script siguiente crea un nuevo certificado autofirmado y lo exporta a un archivo PFX.  El certificado se importa en un almacén de claves existente y, a continuación, se implementa en el clúster de Service Fabric.

```powershell
# Variables.
$certpwd = ConvertTo-SecureString -String "Pa$$word321!" -Force -AsPlainText
$filepath = "C:\MyCertificates\dataenciphermentcert.pfx"
$subjectname = "dataencipherment"
$vaultname = "mykeyvault"
$certificateName = "dataenciphermentcert"
$groupname="myclustergroup"
$clustername = "mycluster"

$subscriptionId = "subscription ID"

Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId

# Create a self signed cert, export to PFX file.
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject $subjectname -Provider 'Microsoft Enhanced Cryptographic Provider v1.0' `
| Export-PfxCertificate -FilePath $filepath -Password $certpwd

# Import the certificate to an existing key vault.  The key vault must be enabled for deployment.
$cer = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certificateName -FilePath $filepath -Password $certpwd

Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $groupname -EnabledForDeployment

# Add the certificate to all the VMs in the cluster.
Add-AzureRmServiceFabricApplicationCertificate -ResourceGroupName $groupname -Name $clustername -SecretIdentifier $cer.SecretId
```
Cifre la contraseña mediante el cmdlet [Invoke-ServiceFabricEncryptText](/powershell/module/servicefabric/Invoke-ServiceFabricEncryptText?view=azureservicefabricps).

```powershell
$text = "=P==/==/=8=/=+u4lyOB=+=nWzEeRfF="
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint $cer.Thumbprint -Text $text -StoreLocation Local -StoreName My
```

Reemplace la contraseña con el texto cifrado devuelto por el cmdlet [Invoke-ServiceFabricEncryptText](/powershell/module/servicefabric/Invoke-ServiceFabricEncryptText?view=azureservicefabricps) y establezca `PasswordEncrypted` en "true".

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
    <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="configure-isolation-mode"></a>Configuración del modo de aislamiento
Windows admite dos modos de aislamiento para contenedores: de proceso y de Hyper-V. Con el modo de aislamiento de proceso, todos los contenedores que se ejecutan en la misma máquina host comparten el kernel con el host. Con el modo de aislamiento de Hyper-V, los kernels se aíslan entre los contenedores de Hyper-V y el host del contenedor. El modo de aislamiento se especifica en el elemento `ContainerHostPolicies` del archivo de manifiesto de aplicación. Los modos de aislamiento que se pueden especificar son `process`, `hyperv` y `default`. El modo de aislamiento predeterminado es `process` en hosts con Windows Server e `hyperv` en hosts con Windows 10. El siguiente fragmento de código muestra cómo el modo de aislamiento se especifica en el archivo de manifiesto de aplicación.

```xml
<ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
```

## <a name="configure-resource-governance"></a>Configuración de la regulación de recursos
La [regulación de recursos](service-fabric-resource-governance.md) restringe los recursos que el contenedor puede usar en el host. El elemento `ResourceGovernancePolicy`, especificado en el manifiesto de la aplicación, se utiliza para declarar los límites de recursos para un paquete de código de servicio. Se pueden establecer límites de recursos para los siguientes recursos: memoria, MemorySwap, CpuShares (peso relativo de CPU), MemoryReservationInMB, BlkioWeight (peso relativo de BlockIO).  En este ejemplo, el paquete de servicio Guest1Pkg obtiene un núcleo en los nodos del clúster en los que es situado.  Los límites de memoria son absolutos, por lo que el paquete de código está limitado a 1024 MB de memoria (con una reserva de garantía flexible de dicha capacidad). Los paquetes de código (contenedores o procesos) no pueden asignar más memoria de la que establece este límite; si se intenta, el resultado es una excepción de memoria insuficiente. Para que la aplicación del límite de recursos funcione, es necesario haber definido límites de memoria en todos los paquetes de código de un paquete de servicio.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```

## <a name="deploy-the-container-application"></a>Implementación de la aplicación contenedora
Guarde todos los cambios y compile la aplicación. Para publicar la aplicación, haga clic con el botón derecho en **MyFirstContainer** en el Explorador de soluciones y seleccione **Publicar**.

En **Punto de conexión de la conexión**, escriba el punto de conexión de administración del clúster.  Por ejemplo, "containercluster.westus2.cloudapp.azure.com:19000". El punto de conexión del cliente se puede encontrar en la hoja de información general del clúster en [Azure Portal](https://portal.azure.com).

Haga clic en **Publicar**.

[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) es una herramienta web para inspeccionar y administrar aplicaciones y nodos en un clúster de Service Fabric. Abra un explorador, navegue hasta http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/ y siga la implementación de la aplicación.  La aplicación se implementa, pero está en estado de error hasta que la imagen se descarga en los nodos del clúster (lo que pueden tardar un tiempo, según el tamaño de imagen): ![Error][1]

La aplicación está lista cuando está en el estado ```Ready```: ![Ready][2]

Abra un explorador y navegue hasta http://containercluster.westus2.cloudapp.azure.com:8081. Debería ver que el título "¡Hola mundo!" se muestra en el explorador.

## <a name="clean-up"></a>Limpieza
Mientras el clúster esté en ejecución seguirá generando cargos, así que debería considerar la posibilidad de [eliminar el clúster](service-fabric-get-started-azure-cluster.md#remove-the-cluster).  Los [clústeres de la entidad](http://tryazureservicefabric.westus.cloudapp.azure.com/) se eliminan automáticamente a las pocas horas.

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
    <EnvironmentVariables>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
    </EnvironmentVariables>

  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to
           listen. Please note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
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
    <EnvironmentOverrides CodePackageRef="FrontendService.Code">
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentOverrides>
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
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

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png
