---
title: Service Fabric e implementación de contenedores | Microsoft Docs
description: Service Fabric y el uso de contenedores para implementar aplicaciones de microservicios. En este artículo se describen las funcionalidades que ofrece Service Fabric para los contenedores y cómo implementar una imagen de contenedor en un clúster
services: service-fabric
documentationcenter: .net
author: msfussell
manager: ''
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2016
ms.author: msfussell

---
# <a name="preview:-deploy-a-container-to-service-fabric"></a>Vista previa: Implementación de un contenedor en Service Fabric
> [!NOTE]
> Esta característica está en fase de vista previa para Linux y no está disponible actualmente en Windows Server. Estará también disponible como vista previa para Windows Server en la próxima versión de Service Fabric después de la disponibilidad general de Windows Server 2016 y se admitirá en versiones posteriores después de ella.
> 
> 

Service Fabric tiene varias funcionalidades de contenedor que le ayudarán a crear aplicaciones que se componen de microservicios en contenedores. Son los llamados servicios en contenedores. Entre estas funcionalidades, cabe destacar:

* Activación e implementación de la imagen de contenedor
* Regulador de recursos
* Autenticación de repositorio
* Asignación de puerto a host de contenedor
* Detección y comunicación entre contenedores
* Capacidad de configurar y establecer variables de entorno

Echemos un vistazo a cada una de las funcionalidades por separado al empaquetar un servicio en contenedor que desea incluir en la aplicación.

## <a name="packaging-a-container"></a>Empaquetado de un contenedor
Al empaquetar un contenedor, puede usar una plantilla de proyecto de Visual Studio o [crear el paquete de aplicación manualmente](#manually). Con Visual Studio, la estructura del paquete de aplicación y los archivos de manifiesto se crean mediante el Asistente para nuevos proyectos.

## <a name="using-visual-studio-to-package-an-existing-executable"></a>Uso de Visual Studio para empaquetar un ejecutable existente
> [!NOTE]
> En una versión futura del SDK de herramientas de Visual Studio, podrá agregar un contenedor a una aplicación de una manera parecida a la que se emplea hoy en día para agregar un archivo ejecutable de invitado. Consulte el tema [Implementación de un ejecutable invitado en Service Fabric](service-fabric-deploy-existing-app.md) . Actualmente se debe hacer el empaquetado de forma manual como se describe a continuación.
> 
> 

<a id="manually"></a>

## <a name="manually-packaging-and-deploying-container"></a>Empaquetado e implementación manual de contenedor
El proceso de empaquetado manual de un servicio en contenedor se basa en los siguientes pasos:

1. Publicar los contenedores en el repositorio.
2. Crear la estructura de directorios del paquete.
3. Editar el archivo de manifiesto de servicio.
4. Editar el archivo de manifiesto de aplicación.

## <a name="container-image-deployment-and-activation."></a>Activación e implementación de la imagen de contenedor.
En el [modelo de aplicación](service-fabric-application-model.md)de Service Fabric, un contenedor representa un host de la aplicación en el que se colocan varias réplicas de servicio. Para implementar y activar un contenedor, ponga el nombre de la imagen de contenedor en un elemento `ContainerHost` en el manifiesto de servicio.

En el manifiesto de servicio, agregue un `ContainerHost` para el punto de entrada y establezca el `ImageName` para que sea el nombre de la imagen y el repositorio del contenedor. El siguiente manifiesto parcial muestra un ejemplo de cómo implementar el contenedor llamado *myimage:v1* desde un repositorio denominado *myrepo*.

    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimagename:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>

Puede proporcionar los comandos de entrada a la imagen de contenedor especificando el elemento opcional `Commands` con un conjunto de comandos delimitados por comas que se ejecutarán dentro del contenedor. 

## <a name="resource-governance"></a>Regulador de recursos
La regulación de recursos es una funcionalidad del contenedor que permite restringir los recursos que puede usar el contenedor en el host. El `ResourceGovernancePolicy`, especificado en el manifiesto de la aplicación, proporciona la capacidad de declarar los límites de recursos para un paquete de código de servicio. Se pueden establecer límites de recursos para:

* Memoria
* MemorySwap
* CpuShares (peso relativo de CPU)
* MemoryReservationInMB  
* BlkioWeight (peso relativo de BlockIO). 

> [!NOTE]
> En una versión futura, se incluirá compatibilidad para especificar límites de E/S de bloques como IOP, BPS de lectura/escritura y mucho más.
> 
> 

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500" 
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>


## <a name="repository-authentication"></a>Autenticación de repositorio
Para descargar un contenedor puede que tenga que proporcionar credenciales de inicio de sesión para el repositorio de contenedor. Las credenciales de inicio de sesión incluidas en el manifiesto de la *aplicación* se usan para especificar la información de inicio de sesión, o clave SSH, para descargar la imagen del contenedor desde el repositorio de imágenes.  En el ejemplo siguiente se muestra una cuenta llamada *TestUser* junto con la contraseña no cifrada. Esto **no** es recomendable.

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

La contraseña puede y debe cifrarse mediante un certificado implementado en la máquina.

El ejemplo siguiente muestra una cuenta denominada *TestUser* con la contraseña cifrada mediante un certificado llamado *MyCert*. Puede usar el comando de Powershell `Invoke-ServiceFabricEncryptText` para crear el texto cifrado secreto de la contraseña. Consulte el artículo [Administración de secretos en aplicaciones de Service Fabric](service-fabric-application-secret-management.md) para más información acerca de cómo hacerlo. La clave privada del certificado para descifrar la contraseña se debe implementar en la máquina local con un método fuera de banda (en Azure esto se logra mediante Resource Manager). Posteriormente, cuando Service Fabric implementa el paquete de servicio en la máquina, es capaz de descifrar el secreto y, junto con el nombre de cuenta, autenticarse con el repositorio de contenedores mediante esas credenciales.

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
            <SecurityAccessPolicies>
                <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
            </SecurityAccessPolicies>
        </Policies>
    </ServiceManifestImport>

## <a name="container-port-to-host-port-mapping"></a>Asignación de puerto a host de contenedor
Puede configurar un puerto de host utilizado para comunicarse con el contenedor mediante la especificación de un `PortBinding` en el manifiesto de aplicación. El enlace de puerto asigna el puerto que el servicio está escuchando dentro del contenedor a un puerto en el host.

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>


## <a name="container-to-container-discovery-and-communication"></a>Detección y comunicación entre contenedores
Mediante la directiva `PortBinding` se puede asignar un puerto de contenedor a un `Endpoint` en el manifiesto de servicio como se muestra en el ejemplo siguiente. El punto de conexión `Endpoint1` puede especificar un puerto fijo, por ejemplo, el puerto 80 o no especificar ningún puerto, en cuyo caso se elegirá un puerto aleatorio del intervalo de puertos de la aplicación de los clústeres.

Para los contenedores de invitado, especificar un `Endpoint` como este en el manifiesto de servicio permite a Service Fabric publicar automáticamente este punto de conexión en el servicio de nombres para que los demás servicios que se ejecutan en el clúster puedan detectar este contenedor mediante las consultas REST del servicio de resolución. 

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

Al registrarse con el servicio de nombres, se puede establecer fácilmente la comunicación de contenedor a contenedor en el código dentro del contenedor mediante el [proxy inverso](service-fabric-reverseproxy.md). Solo tiene que proporcionar el puerto de escucha http de proxy inverso y el nombre de los servicios con los que desea comunicarse estableciendo estos como variables de entorno. Consulte la sección siguiente para averiguar cómo hacerlo.  

## <a name="configure-and-set-environment-variables"></a>Configuración y establecimiento de variables de entorno
Las variables de entorno se pueden especificar para cada paquete de código en el manifiesto de servicio para ambos servicios implementados en contenedores o como archivos ejecutables de procesos o de invitados. Estos valores de variables de entorno se pueden invalidar específicamente en el manifiesto de aplicación o se pueden especificar durante la implementación como parámetros de la aplicación.

El siguiente fragmento de código XML del manifiesto de servicio muestra un ejemplo de cómo especificar variables de entorno para un paquete de código. 

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

Estas variables de entorno se pueden invalidar en el nivel del manifiesto de aplicación:

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>

En el ejemplo anterior, se ha especificado un valor explícito para la variable de entorno `HttpGateway` (19000) mientras que el valor del parámetro `BackendServiceName` se ha establecido a través del parámetro de la aplicación `[BackendSvc]`. Esto le permite especificar el valor para `BackendServiceName`en el momento de implementar la aplicación sin necesidad de tener un valor fijo en el manifiesto. 

## <a name="complete-examples-for-application-and-service-manifest"></a>Ejemplos completos de manifiesto de servicio y de aplicación
El siguiente es un manifiesto de aplicación de ejemplo que muestra las características del contenedor.

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


El siguiente es un ejemplo de manifiesto de servicio (especificado en el manifiesto de aplicación anterior) que muestra las características del contenedor

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless frontend in a container</Description>
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
            <Eendpoints>
                <Endpoint Name="Endpoint1" Port="80"  UriScheme="http" />
            </Eendpoints>
        </Resources>
    </ServiceManifest>



<!--HONumber=Oct16_HO2-->


