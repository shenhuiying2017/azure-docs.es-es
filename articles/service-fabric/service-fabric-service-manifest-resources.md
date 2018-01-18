---
title: "Especificación de los puntos de conexión de servicio de Service Fabric | Microsoft Docs"
description: "Cómo describir los recursos de punto de conexión en un manifiesto de servicio, incluida la configuración de puntos de conexión HTTPS"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: da36cbdb-6531-4dae-88e8-a311ab71520d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2018
ms.author: subramar
ms.openlocfilehash: ccfbd03ed6d2cd84f8c2cf789e4fc1e99b1e5bbf
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="specify-resources-in-a-service-manifest"></a>Especificación de los recursos en un manifiesto de servicio
## <a name="overview"></a>Información general
El manifiesto de servicio permite que los recursos que utilizará el servicio sean declarados o modificados sin cambiar el código compilado. Service Fabric de Azure admite la configuración de recursos de puntos de conexión para el servicio. El acceso a los recursos especificados en el manifiesto de servicio puede controlarse a través de SecurityGroup en el manifiesto de aplicación. La declaración de recursos permite cambiar estos recursos durante la implementación, lo que significa que no es necesario que el servicio introduzca un nuevo mecanismo de configuración. La definición de esquema para el archivo ServiceManifest.xml se instala con el SDK y las herramientas de Service Fabric en *C:\Archivos de programa\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Puntos de conexión
Cuando se define un recurso de punto de conexión en el manifiesto de servicio, Service Fabric asigna puertos desde el intervalo de puertos reservados de aplicación cuando un puerto no se especifica expresamente. Por ejemplo, analice el punto de conexión *ServiceEndpoint1* especificado en el fragmento de manifiesto que encontrará después de este párrafo. Además, los servicios también pueden solicitar un puerto específico en un recurso. Es posible asignar números de puerto diferentes a réplicas de servicio que se ejecutan en nodos de clúster, mientras que las réplicas del mismo servicio que se ejecuta en el mismo nodo comparten el mismo puerto. Las réplicas de servicio pueden usar estos puertos según sea necesario para la replicación y procesar solicitudes de cliente.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Si hay varios paquetes de código en un solo paquete de servicio, también necesita hacer referencia al paquete de código en la sección **Puntos de conexión**.  Por ejemplo, si **ServiceEndpoint2a** y **ServiceEndpoint2b** son los puntos de conexión del mismo paquete de servicio y hacen referencia a paquetes de código diferentes, el paquete de código correspondiente a cada punto de conexión se indica de la siguiente manera:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Vea [Configuración de Reliable Services con estado](service-fabric-reliable-services-configuration.md) para obtener más información sobre cómo hacer referencia a los puntos de conexión del archivo de configuración del paquete de configuración (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Ejemplo: Especificación de un punto de conexión HTTP para el servicio
El siguiente manifiesto de servicio define un recurso de punto de conexión TCP y dos recursos de punto de conexión HTTP en el elemento &lt;Recursos&gt;.

Service Fabric hace ACL automáticamente en los puntos de conexión HTTP.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Ejemplo: Especificación de un punto de conexión HTTPS para el servicio
El protocolo HTTPS ofrece autenticación de servidor y también se usa para cifrar la comunicación entre cliente y servidor. Para habilitar HTTPS en el servicio Service Fabric, especifique el protocolo en la sección *Recursos -> Puntos de conexión -> Punto de conexión* del manifiesto de servicio, como se mostró anteriormente para el punto de conexión *ServiceEndpoint3*.

> [!NOTE]
> No se puede cambiar el protocolo de un servicio durante la actualización de la aplicación. Si se modifica en el transcurso, se tratará de un cambio brusco.
> 
> 

Este es un ejemplo ApplicationManifest que debe establecer para HTTPS. Necesitará proporcionar la huella digital para el certificado. EndpointRef es una referencia a EndpointResource en ServiceManifest, para la que establece el protocolo HTTPS. Puede agregar más de un certificado EndpointCertificate.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

En los clústeres de Linux, el valor predeterminado del almacén **MY** es la carpeta **/var/lib/sfcerts**.


## <a name="overriding-endpoints-in-servicemanifestxml"></a>Invalidación de Endpoints en ServiceManifest.xml

En ApplicationManifest, agregue una sección denominada ResourceOverrides que será un elemento del mismo nivel que la sección ConfigOverrides. En esta sección puede especificar la invalidación de la sección Endpoints en la sección de recursos especificada en el manifiesto del servicio.

Para invalidar Endpoint en ServiceManifest mediante ApplicationParameters cambie ApplicationManifest como se indica a continuación:

En la sección ServiceManifestImport, agregue una nueva sección "ResourceOverrides"

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

En Parameters, agregue a continuación:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Ahora, al implementar la aplicación puede pasar estos valores como ApplicationParameters, por ejemplo:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Nota: Si los valores proporcionados para ApplicationParameters están vacíos se vuelve al valor predeterminado proporcionado en ServiceManifest para el EndPointName correspondiente.

Por ejemplo: 

Si en ServiceManifest ha especificado

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

Y el valor de Port1 y Protocol1 en ApplicationParameters es nulo o está vacío. El puerto lo decide aún Service Fabric. Y el protocolo sera TCP.

Imagine que especifica un valor incorrecto. Supongamos que para el puerto ha especificado un valor de cadena "Foo" en lugar de un valor entero.  El comando New-ServiceFabricApplication producirá un error: el parámetro de invalidación con nombre 'ServiceEndpoint1', atributo 'Port1' en la sección 'ResourceOverrides' no es válido. El valor especificado es 'Foo' y se requiere 'int'.
