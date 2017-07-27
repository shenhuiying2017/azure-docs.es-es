---
title: "Service Fabric e implementación de contenedores en Linux | Microsoft Docs"
description: "Service Fabric y el uso de contenedores de Linux para implementar aplicaciones de microservicios. En este artículo se describen las funcionalidades que ofrece Service Fabric para los contenedores y cómo implementar una imagen de contenedor de Linux en un clúster"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4ba99103-6064-429d-ba17-82861b6ddb11
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/29/2017
ms.author: msfussell
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 9dcec753e5f999a1bac07276373c0c25f89ec58d
ms.contentlocale: es-es
ms.lasthandoff: 07/01/2017


---
# <a name="deploy-a-linux-container-to-service-fabric"></a>Implementar un contenedor de Linux en Service Fabric
> [!div class="op_single_selector"]
> * [Implementación de un contenedor de Windows](service-fabric-deploy-container.md)
> * [Implementar un contenedor de Linux](service-fabric-deploy-container-linux.md)
>
>

Este artículo lo guiará a través de la creación de servicios de contenedor en contenedores de Docker en Linux.

Service Fabric tiene varias funcionalidades de contenedor que le ayudarán a crear aplicaciones que se componen de microservicios en contenedores. Son los llamados servicios en contenedores.

Entre estas funcionalidades, cabe destacar:

* Activación e implementación de la imagen de contenedor
* Regulador de recursos
* Autenticación de repositorio
* Asignación de puerto a host de contenedor
* Detección y comunicación entre contenedores
* Capacidad de configurar y establecer variables de entorno

## <a name="packaging-a-docker-container-with-yeoman"></a>Empaquetado de un contenedor de Docker con Yeoman
Al empaquetar un contenedor en Linux, puede usar una plantilla de Yeoman o [crear el paquete de aplicación manualmente](#manually).

Una aplicación de Service Fabric puede contener uno o varios contenedores, cada uno de ellos con un rol específico en la prestación de la funcionalidad de la aplicación. El SDK de Service Fabric para Linux incluye un generador [Yeoman](http://yeoman.io/) que permite crear fácilmente la aplicación y agregar una imagen de contenedor. Vamos a usar Yeoman para crear una aplicación con un único contenedor de Docker denominado "*SimpleContainerApp*". Puede agregar más servicios más adelante editando los archivos de manifiesto generados.

## <a name="install-docker-on-your-development-box"></a>Instalación de un contenedor de Docker en el cuadro de desarrollo

Ejecute los comandos siguientes para instalar el contenedor de Docker en el cuadro de desarrollo de Linux (si utiliza la imagen de Vagrant en OSX, el contenedor de Docker ya estará instalado):

```bash
    sudo apt-get install wget
    wget -qO- https://get.docker.io/ | sh
```

## <a name="create-the-application"></a>Creación de la aplicación
1. En un terminal, escriba `yo azuresfcontainer`.
2. Dé nombre a la aplicación; por ejemplo, mycontainerap.
3. Proporcione la dirección URL de la imagen de contenedor desde un repositorio de DockerHub. El parámetro de imagen toma la forma [repositorio]/[nombre de la imagen]
4. Si la imagen no tiene ningún punto de entrada de carga de trabajo definido, debe especificar explícitamente comandos de entrada con un conjunto delimitado por comas de comandos que se ejecutarán dentro del contenedor, lo que mantendrá el contenedor en ejecución después del inicio.

![Generador Yeoman de Service Fabric para contenedores][sf-yeoman]

## <a name="deploy-the-application"></a>Implementación de la aplicación

### <a name="using-xplat-cli"></a>Uso de la CLI multiplataforma
Una vez compilada la aplicación, puede implementarla en el clúster local mediante la CLI de Azure.

1. Conéctese al clúster de Service Fabric local.

    ```bash
    azure servicefabric cluster connect
    ```

2. Use el script de instalación proporcionado en la plantilla para copiar el paquete de aplicación en el almacén de imágenes del clúster, registrar el tipo de aplicación y crear una instancia de la aplicación.

    ```bash
    ./install.sh
    ```

3. Abra un explorador y vaya a Service Fabric Explorer en http://localhost:19080/Explorer (reemplace localhost por la dirección IP privada de la VM si usa Vagrant en Mac OS X).
4. Expanda el nodo Applications y observe que ahora hay una entrada para su tipo de aplicación y otra para la primera instancia de ese tipo.
5. Para eliminar la instancia de aplicación y anular el registro del tipo de aplicación, utilice el script de desinstalación proporcionado en la plantilla.

    ```bash
    ./uninstall.sh
    ```

### <a name="using-azure-cli-20"></a>Uso de la CLI de Azure 2.0

Consulte la documentación de referencia sobre la administración de un [ciclo de vida de aplicación mediante la CLI de Azure 2.0](service-fabric-application-lifecycle-azure-cli-2-0.md).

Para ver una aplicación de ejemplo, [eche un vistazo a los ejemplos de código de contenedor de Service Fabric en GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-containers).

## <a name="adding-more-services-to-an-existing-application"></a>Incorporación de más servicios a una aplicación existente

Para agregar otro servicio de contenedor a una aplicación ya creada mediante `yo`, realice los pasos siguientes:

1. Cambie el directorio al directorio raíz de la aplicación existente.  Por ejemplo, `cd ~/YeomanSamples/MyApplication`, si `MyApplication` es la aplicación creada por Yeoman.
2. Ejecute `yo azuresfcontainer:AddService`

<a id="manually"></a>

## <a name="manually-package-and-deploy-a-container-image"></a>Empaquetado e implementación manual de una imagen de contenedor
El proceso de empaquetado manual de un servicio en contenedor se basa en los siguientes pasos:

1. Publicar los contenedores en el repositorio.
2. Crear la estructura de directorios del paquete.
3. Editar el archivo de manifiesto de servicio.
4. Editar el archivo de manifiesto de aplicación.

## <a name="deploy-and-activate-a-container-image"></a>Implementación y activación de una imagen de contenedor
En el [modelo de aplicación](service-fabric-application-model.md)de Service Fabric, un contenedor representa un host de la aplicación en el que se colocan varias réplicas de servicio. Para implementar y activar un contenedor, ponga el nombre de la imagen de contenedor en un elemento `ContainerHost` en el manifiesto de servicio.

En el manifiesto de servicio, añada un elemento `ContainerHost` para el punto de entrada. A continuación, establezca el elemento `ImageName` como nombre de la imagen y el repositorio del contenedor. El siguiente manifiesto parcial muestra un ejemplo de cómo implementar el contenedor llamado `myimage:v1` desde un repositorio denominado `myrepo`:

```xml
    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimage:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>
```

Puede proporcionar los comandos de entrada especificando el elemento opcional `Commands` con un conjunto de comandos delimitados por comas que se ejecutarán dentro del contenedor.

> [!NOTE]
> Si la imagen no tiene ningún punto de entrada de carga de trabajo definido, debe especificar explícitamente comandos de entrada dentro del elemento `Commands` con un conjunto delimitado por comas de comandos que se ejecutarán dentro del contenedor, lo que mantendrá el contenedor en ejecución después del inicio.

## <a name="understand-resource-governance"></a>Descripción de la regulación de recursos
La regulación de recursos es una funcionalidad del contenedor que permite restringir los recursos que puede usar el contenedor en el host. El elemento `ResourceGovernancePolicy`, especificado en el manifiesto de la aplicación, se utiliza para declarar los límites de recursos para un paquete de código de servicio. Es posible establecer límites para los siguientes recursos:

* Memoria
* MemorySwap
* CpuShares (peso relativo de CPU)
* MemoryReservationInMB  
* BlkioWeight (peso relativo de BlockIO).

> [!NOTE]
> En una versión futura, se incluirá compatibilidad para especificar límites de E/S de bloques como IOP, BPS de lectura/escritura y mucho más.
>
>

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500"
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="authenticate-a-repository"></a>Autenticación de un repositorio
Para descargar un contenedor, puede que tenga que proporcionar credenciales de inicio de sesión para el repositorio de contenedor. Las credenciales de inicio de sesión incluidas en el manifiesto de la aplicación se usan para especificar la información de inicio de sesión, o clave SSH, para descargar la imagen del contenedor desde el repositorio de imágenes. En el ejemplo siguiente se muestra una cuenta llamada *TestUser* junto con la contraseña no cifrada (*no* recomendado):

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

Se recomienda cifrar la contraseña mediante un certificado implementado en la máquina.

El ejemplo siguiente muestra una cuenta denominada *TestUser* con la contraseña cifrada mediante un certificado llamado *MyCert*. Puede usar el comando de PowerShell `Invoke-ServiceFabricEncryptText` para crear el texto cifrado secreto de la contraseña. Para más información, consulte el artículo [Administración de secretos en aplicaciones de Service Fabric](service-fabric-application-secret-management.md).

La clave privada del certificado que se usa para descifrar la contraseña se debe implementar en la máquina local con un método fuera de banda (en Azure esto se logra mediante Azure Resource Manager). Posteriormente, cuando Service Fabric implementa el paquete de servicio en la máquina, es capaz de descifrar el secreto. Al usar el secreto junto con el nombre de cuenta, puede autenticarse con el repositorio de contenedor.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping"></a>Configuración de la asignación de puerto a host de contenedor
Puede configurar un puerto de host utilizado para comunicarse con el contenedor mediante la especificación de un elemento `PortBinding` en el manifiesto de aplicación. El enlace de puerto asigna el puerto que el servicio está escuchando dentro del contenedor a un puerto en el host.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name="configure-container-to-container-discovery-and-communication"></a>Configuración de detección y comunicación entre contenedores
Mediante el uso de la directiva `PortBinding`, puede asignar un puerto de contenedor a un `Endpoint` en el manifiesto de servicio. El punto de conexión `Endpoint1` puede especificar un puerto fijo (por ejemplo, el puerto 80). También puede no especificar ningún puerto, en cuyo caso se elegirá un puerto aleatorio del intervalo de puertos de la aplicación del clúster.

Si especifica un punto de conexión, al usar la etiqueta `Endpoint` en el manifiesto de servicio de un contenedor invitado, Service Fabric puede publicar automáticamente este punto de conexión en el servicio de nombres. De este modo, otros servicios que se ejecutan en el clúster podrán detectar que este contenedor usa las consultas REST para la resolución.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

Al registrarse con el servicio de nombres, se puede establecer fácilmente la comunicación de contenedor a contenedor en el código dentro del contenedor mediante el [proxy inverso](service-fabric-reverseproxy.md). La comunicación se establece al proporcionar el puerto de escucha http de proxy inverso y el nombre de los servicios con los que desea comunicarse como variables de entorno. Para más información, consulte la siguiente sección.

## <a name="configure-and-set-environment-variables"></a>Configuración y establecimiento de variables de entorno
Las variables de entorno se pueden especificar para cada paquete de código en el manifiesto de servicio, tanto para los servicios implementados en contenedores como para los servicios implementados como archivos ejecutables de procesos o invitados. Estos valores de variables de entorno se pueden invalidar específicamente en el manifiesto de aplicación o se pueden especificar durante la implementación como parámetros de la aplicación.

El siguiente fragmento de código XML del manifiesto de servicio muestra un ejemplo de cómo especificar variables de entorno para un paquete de código:

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>a guest executable service in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
    </ServiceManifest>
```

Estas variables de entorno se pueden invalidar en el nivel del manifiesto de aplicación:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>
```

En el ejemplo anterior, se ha especificado un valor explícito para la variable de entorno `HttpGateway` (19000), mientras que el valor del parámetro `BackendServiceName` se ha establecido a través del parámetro de la aplicación `[BackendSvc]`. Esta configuración permite especificar el valor de `BackendServiceName` al implementar la aplicación sin necesidad de tener un valor fijo en el manifiesto.

## <a name="complete-examples-for-application-and-service-manifest"></a>Ejemplos completos de manifiesto de servicio y de aplicación

Este es un ejemplo de un manifiesto de aplicación:

```xml
    <ApplicationManifest ApplicationTypeName="SimpleContainerApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>A simple service container application</Description>
        <Parameters>
            <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
            <Parameter Name="BackEndSvcName" DefaultValue="bkend"></Parameter>
        </Parameters>
        <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
            <EnvironmentOverrides CodePackageRef="FrontendService.Code">
                <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvcName]"/>
                <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentOverrides>
            <Policies>
                <ResourceGovernancePolicy CodePackageRef="Code" CpuShares="500" MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
                <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                    <RepositoryCredentials AccountName="username" Password="****" PasswordEncrypted="true"/>
                    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
                </ContainerHostPolicies>
            </Policies>
        </ServiceManifestImport>
    </ApplicationManifest>
```

Este es un ejemplo de manifiesto de servicio (especificado en el manifiesto de aplicación anterior):

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless front end in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
        <ConfigPackage Name="FrontendService.Config" Version="1.0" />
        <DataPackage Name="FrontendService.Data" Version="1.0" />
        <Resources>
            <Endpoints>
                <Endpoint Name="Endpoint1" UriScheme="http" Port="80" Protocol="http"/>
            </Endpoints>
        </Resources>
    </ServiceManifest>
```

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha implementado un servicio en contenedor, consulte [Ciclo de vida de la aplicación de Service Fabric](service-fabric-application-lifecycle.md) para saber cómo administrar su ciclo de vida.

* [Información general: Service Fabric y contenedores](service-fabric-containers-overview.md)
* [Interactuación con los clústeres de Service Fabric mediante la CLI de Azure](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman1.png

## <a name="related-articles"></a>Artículos relacionados

* [Introducción a Service Fabric y la CLI de Azure 2.0](service-fabric-azure-cli-2-0.md)
* [Introducción a la CLI de XPlat de Service Fabric](service-fabric-azure-cli.md)

