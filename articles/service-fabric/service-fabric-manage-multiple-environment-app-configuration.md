---
title: "Administración de varios entornos en Service Fabric | Microsoft Docs"
description: "Las aplicaciones de Service Fabric se pueden ejecutar en clústeres cuyo tamaño oscila entre una y miles de máquinas. En algunos casos, deseará configurar su aplicación de forma diferente para cada uno de los entornos. Este artículo explica cómo definir distintos parámetros de aplicación por entorno."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: mikkelhegn
ms.openlocfilehash: 671cc9b0f7b7b37fcf5b052f7e34bc98e66b2838
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-application-parameters-for-multiple-environments"></a>Administración de los parámetros de la aplicación en varios entornos
Puede crear clústeres de Service Fabric de Azure con cualquier número de máquinas, desde una sola máquina hasta miles de ellas. Aunque los archivos binarios de una aplicación pueden ejecutarse sin modificación alguna en este amplio espectro de entornos, con frecuencia se desea configurar la aplicación de manera diferente, en función del número de máquinas donde se vaya a implementar.

A modo de ejemplo simple, considere `InstanceCount` en un servicio sin estado. Cuando se ejecutan las aplicaciones de Azure, normalmente se prefiere establecer este parámetro en el valor especial "-1". Esta configuración garantiza que el servicio se ejecute en todos los nodos del clúster (o en todos los nodos de ese tipo, si ha establecido una restricción de colocación). Sin embargo, no es adecuada para un clúster de una sola máquina, ya que no puede haber varios procesos que escuchen el mismo punto de conexión en una máquina individual. En su lugar, normalmente se establece `InstanceCount` en "1".

## <a name="specifying-environment-specific-parameters"></a>Especificación de parámetros concretos del entorno
La solución a este problema de configuración es un conjunto de servicios predeterminados con parámetros y los archivos de parámetros de la aplicación que rellene los valores de dichos parámetros para un entorno determinado. Los parámetros predeterminados de los servicios y las aplicaciones se configuran en los manifiestos de servicio y aplicación. La definición de esquema para los archivos ApplicationManifest.xml y ServiceManifest.xml se instala con el SDK y las herramientas de Service Fabric en *C:\Archivos de programa\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

### <a name="default-services"></a>Servicios predeterminados
Las aplicaciones de Service Fabric constan de una colección de instancias de servicio. Aunque se puede crear una aplicación vacía y, a continuación, crear todas las instancias del servicio de forma dinámica, la mayoría de las aplicaciones tienen un conjunto de servicios principales que se deben crear siempre que se crea una instancia de la aplicación. Estos se conocen como "servicios predeterminados". Se especifican en el manifiesto de aplicación con marcadores de posición para la configuración de cada entorno entre corchetes:

```xml
  <DefaultServices>
      <Service Name="Stateful1">
          <StatefulService
              ServiceTypeName="Stateful1Type"
              TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]"
              MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

              <UniformInt64Partition
                  PartitionCount="[Stateful1_PartitionCount]"
                  LowKey="-9223372036854775808"
                  HighKey="9223372036854775807"
              />
        </StatefulService>
    </Service>
  </DefaultServices>
```

Todos los parámetros con nombre deben definirse dentro del elemento Parameters del manifiesto de aplicación:

```xml
    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>
```

El atributo DefaultValue especifica el valor que se usará en ausencia de un parámetro más específico para un entorno determinado.

> [!NOTE]
> No todos los parámetros de instancias de servicio son adecuados para la configuración de cada entorno. En el ejemplo anterior, los valores LowKey y HighKey del esquema de partición del servicio se definen explícitamente para todas las instancias del servicio, ya que el intervalo de partición es una función del dominio de datos, no del entorno.
> 
> 

### <a name="per-environment-service-configuration-settings"></a>Opciones de configuración del servicio de cada entorno
El [modelo de aplicación de Service Fabric](service-fabric-application-model.md) permite a los servicios incluir paquetes de configuración que contienen pares clave-valor personalizados que se pueden leer en tiempo de ejecución. Los valores de estas opciones también se pueden diferenciar por entorno, para lo que se debe especificar `ConfigOverride` en el manifiesto de aplicación.

Suponga que tiene la siguiente opción en Config\Settings.xml para el servicio `Stateful1`:

```xml
  <Section Name="MyConfigSection">
     <Parameter Name="MaxQueueSize" Value="25" />
  </Section>
```
Para reemplazar este valor para un par entorno/aplicación específico, cree `ConfigOverride` al importar el manifiesto de servicio en el manifiesto de aplicación.

```xml
  <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>
```
Este parámetro se puede configurar después por entorno, tal como se mostró anteriormente. Puede hacerlo declarándolo en la sección de parámetros del manifiesto de aplicación y especificar valores específicos del entorno en el archivo de parámetros de la aplicación.

> [!NOTE]
> En el caso de las opciones de configuración, hay tres lugares donde se puede establecer el valor de una clave: el paquete de configuración del servicio, el manifiesto de aplicación y el archivo de parámetros de la aplicación. Service Fabric siempre elegirá entre el archivo de parámetros de la aplicación en primer lugar (si se especifica), luego el manifiesto de aplicación y, finalmente, el paquete de configuración.
> 
> 

### <a name="setting-and-using-environment-variables"></a>Establecimiento y uso de variables de entorno 
Puede especificar y establecer variables de entorno en el archivo ServiceManifest.xml y, luego, invalidarlas por instancia en el archivo ApplicationManifest.xml.
En el ejemplo siguiente se muestran dos variables de entorno, una con un valor definido y el de la otra, invalidado. Puede usar parámetros de aplicación para establecer los valores de las variables de entorno del mismo modo que se usaron para las invalidaciones de configuración.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
    <EnvironmentVariables>
      <EnvironmentVariable Name="MyEnvVariable" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentVariables>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
```
Para invalidar las variables de entorno del archivo ApplicationManifest.xml, haga referencia al paquete de código en el archivo ServiceManifest con el elemento `EnvironmentOverrides`.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="MyCode">
      <EnvironmentVariable Name="MyEnvVariable" Value="mydata"/>
    </EnvironmentOverrides>
  </ServiceManifestImport>
 ``` 
 Una vez que se cree la instancia de servicio con nombre, puede tener acceso a las variables de entorno desde el código. Por ejemplo, en C# puede hacer lo siguiente

```csharp
    string EnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

### <a name="service-fabric-environment-variables"></a>Variables de entorno de Service Fabric
Service Fabric tiene variables de entorno integradas que se establecen para cada instancia del servicio. A continuación se muestra la lista completa de variables de entorno; las que están en negrita son las que se utilizarán en el servicio y las otras por el tiempo de ejecución de Service Fabric. 

* Fabric_ApplicationHostId
* Fabric_ApplicationHostType
* Fabric_ApplicationId
* **Fabric_ApplicationName**
* Fabric_CodePackageInstanceId
* **Fabric_CodePackageName**
* **Fabric_Endpoint_[SuNombreDeServicio]TypeEndpoint**
* **Fabric_Folder_App_Log**
* **Fabric_Folder_App_Temp**
* **Fabric_Folder_App_Work**
* **Fabric_Folder_Application**
* Fabric_NodeId
* **Fabric_NodeIPOrFQDN**
* **Fabric_NodeName**
* Fabric_RuntimeConnectionAddress
* Fabric_ServicePackageInstanceId
* Fabric_ServicePackageName
* Fabric_ServicePackageVersionInstance
* FabricPackageFileName

El código siguiente muestra cómo enumerar las variables de entorno de Service Fabric.
 ```csharp
    foreach (DictionaryEntry de in Environment.GetEnvironmentVariables())
    {
        if (de.Key.ToString().StartsWith("Fabric"))
        {
            Console.WriteLine(" Environment variable {0} = {1}", de.Key, de.Value);
        }
    }
```
Los siguientes son ejemplos de variable de entorno para un tipo de aplicación denominado `GuestExe.Application` con un tipo de servicio denominado `FrontEndService` cuando se ejecuta en el equipo de desarrollo local.

* **Fabric_ApplicationName = fabric:/GuestExe.Application**
* **Fabric_CodePackageName = Code**
* **Fabric_Endpoint_FrontEndServiceTypeEndpoint = 80**
* **Fabric_NodeIPOrFQDN = localhost**
* **Fabric_NodeName = _Node_2**

### <a name="application-parameter-files"></a>Archivos de parámetros de la aplicación
El proyecto de aplicación de Service Fabric puede incluir uno o más archivos de parámetro de la aplicación. Cada uno de ellos define valores concretos para los parámetros que se definen en el manifiesto de aplicación:

```xml
    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="3" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>
```
De forma predeterminada, una aplicación nueva incluye tres archivos de parámetros de aplicación, denominados Local.1Node.xml, Local.5Node.xml y Cloud.xml:

![Archivos de parámetros de la aplicación en el Explorador de soluciones][app-parameters-solution-explorer]

Para crear un archivo de parámetros, basta con copiar y pegar uno existente y asignarle un nombre nuevo.

## <a name="identifying-environment-specific-parameters-during-deployment"></a>Identificación de parámetros específicos del entorno durante la implementación
En el momento de la implementación, es preciso elegir el archivo de parámetros apropiado que se aplicará en la aplicación. Esto se puede realizar desde el cuadro de diálogo Publicar de Visual Studio o a través de PowerShell.

### <a name="deploy-from-visual-studio"></a>Implementación desde Visual Studio
Al publicar una aplicación en Visual Studio, puede elegir en la lista de archivos de parámetros disponibles.

![Elección de un archivo de parámetros en el cuadro de diálogo Publicar][publishdialog]

### <a name="deploy-from-powershell"></a>Implementación a partir de PowerShell
El script de PowerShell `Deploy-FabricApplication.ps1` incluido en la plantilla de proyecto de aplicación acepta un perfil de publicación como un parámetro, y el elemento PublishProfile contiene una referencia al archivo de parámetros de la aplicación.

  ```PowerShell
    ./Deploy-FabricApplication -ApplicationPackagePath <app_package_path> -PublishProfileFile <publishprofile_path>
  ```

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre algunos de los conceptos principales descritos en este tema, vea la [Información técnica de Service Fabric](service-fabric-technical-overview.md). Para obtener más información sobre otras funcionalidades de administración de aplicaciones disponibles en Visual Studio, vea [Administración de aplicaciones de Service Fabric en Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
