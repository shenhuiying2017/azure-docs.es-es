---
title: "Modelo de aplicación de Azure Service Fabric | Microsoft Docs"
description: "Cómo modelar y describir las aplicaciones y servicios en Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: ryanwi
ms.openlocfilehash: e30482427b88eb3e58d39075c7f0734664b79aa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="model-an-application-in-service-fabric"></a>Modelar una aplicación en Service Fabric
En este artículo, se proporciona información general sobre el modelo de aplicación de Azure Service Fabric y se describe el modo de definir una aplicación y un servicio a través de archivos de manifiesto.

## <a name="understand-the-application-model"></a>Entender el modelo de aplicación
Una aplicación es una colección de servicios constituyentes que realizan una determinada función o funciones. Un servicio realiza una función completa e independiente, y puede iniciarse y ejecutarse independientemente de otros servicios.  Un servicio se compone de código, configuración y datos. Para cada servicio, el código consta de los archivos binarios ejecutables, la configuración consta de una configuración del servicio que se puede cargar en tiempo de ejecución y los datos constan de datos estáticos arbitrarios que el servicio va a consumir. Cada componente de este modelo de aplicación jerárquico puede tener varias versiones y actualizarse independientemente.

![Modelo de aplicación de Service Fabric][appmodel-diagram]

Un tipo de aplicación es una categorización de una aplicación, que consta de un conjunto de tipos de servicio. Un tipo de servicio es una categorización de un servicio. La categorización puede tener una configuración diferente, pero cuya funcionalidad básica sigue siendo la misma. Las instancias de un servicio son las distintas variaciones de la configuración del servicio del mismo tipo de servicio.  

Las clases (o "tipos") de aplicaciones y servicios se describen a través de archivos XML (manifiestos de aplicación y de servicio).  Los manifiestos son las plantillas sobre las que se pueden crear instancias de las aplicaciones desde el almacén de imágenes del clúster. La definición de esquema para los archivos ServiceManifest.xml y ApplicationManifest.xml se instala con el SDK y las herramientas de Service Fabric en *C:\Archivos de programa\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

El código de las diversas instancias de aplicación se ejecuta como procesos independientes, incluso cuando lo hospede el mismo nodo de Service Fabric. Además, el ciclo de vida de cada instancia de aplicación se puede administrar (por ejemplo, actualizar) de forma independiente. En el siguiente diagrama se muestra cómo los tipos de aplicación constan de tipos de servicio, que a su vez constan de código, configuración y paquetes de datos. Para simplificar el diagrama, solo se muestran los paquetes code/config/data para `ServiceType4`, aunque cada tipo de servicio podría incluir algunos o todos esos tipos de paquetes.

![Tipos de aplicaciones de Service Fabric y tipos de servicio][cluster-imagestore-apptypes]

Dos archivos de manifiesto se usan para describir aplicaciones y servicios: el manifiesto de servicio y el manifiesto de aplicación. En las siguientes secciones se detallan los manifiestos.

Puede haber una o más instancias de un tipo de servicio activas en el clúster. Por ejemplo, las instancias de servicio con estado o réplicas logran una alta confiabilidad mediante la replicación del estado entre réplicas ubicadas en distintos nodos del clúster. La replicación esencialmente proporciona redundancia para que el servicio esté disponible, incluso si se produce un error en un nodo de un clúster. Un [servicio con particiones](service-fabric-concepts-partitioning.md) divide aún más su estado (y patrones de acceso a ese estado) entre los nodos del clúster.

En el siguiente diagrama se muestra la relación entre aplicaciones e instancias de servicio, particiones y réplicas.

![Particiones y réplicas dentro de un servicio][cluster-application-instances]

> [!TIP]
> Puede ver el diseño de las aplicaciones en un clúster mediante la herramienta Service Fabric Explorer disponible en http://&lt;direcciónDelClúster&gt;:19080/Explorer. Para más información, vea [Visualización del clúster mediante Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
> 
> 

## <a name="describe-a-service"></a>Describir un servicio
El manifiesto de servicio define mediante declaración el tipo de servicio y la versión. Especifica los metadatos de servicio, como el tipo de servicio, las propiedades de estado, las métricas de equilibrio de carga, archivos binarios del servicio y archivos de configuración.  Dicho de otro modo, describe los paquetes de código, configuración y datos que componen un paquete de servicio para admitir uno o más tipos de servicio. Este es un ejemplo sencillo de manifiesto de servicio:

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

**Versión** son cadenas no estructuradas y no analizadas por el sistema. Los atributos de versión se usan para dotar a cada componente de varias versiones para las actualizaciones.

**ServiceTypes** declara qué tipos de servicio admite **CodePackages** en este manifiesto. Cuando se crea una instancia de un servicio en uno de estos tipos de servicio, todos los paquetes de código declarados en este manifiesto se activan mediante la ejecución de sus puntos de entrada. Se espera que los procesos resultantes registren los tipos de servicio admitidos en tiempo de ejecución. Los tipos de servicio se declaran en el nivel de manifiesto y no en el nivel de paquete de código. De modo que, cuando hay varios paquetes de código, se activan todos cada vez que el sistema busca cualquiera de los tipos de servicio declarados.

**SetupEntryPoint** es un punto de entrada con privilegios que se ejecuta con las mismas credenciales que Service Fabric (normalmente, la cuenta *LocalSystem* ) antes que cualquier otro punto de entrada. El archivo ejecutable especificado por **EntryPoint** suele ser el host de servicio de ejecución prolongada. La presencia de un punto de entrada de configuración independiente evita tener que ejecutar el host de servicio con privilegios elevados durante largos períodos de tiempo. El archivo ejecutable especificado por **EntryPoint** se ejecuta después de salir de **SetupEntryPoint** correctamente. Si el proceso finaliza o se bloquea alguna vez, el proceso resultante se supervisa y reinicia (comenzando de nuevo con **SetupEntryPoint**).  

Los escenarios de uso de **SetupEntryPoint** habituales se corresponden a cuando se ejecuta un archivo ejecutable antes de iniciar el servicio o cuando se realiza una operación con privilegios elevados. Por ejemplo:

* configuración e inicialización de las variables de entorno que el ejecutable del servicio necesita. Esto no se limita tan solo a los archivos ejecutables escritos a través de los modelos de programación de Service Fabric. Por ejemplo, npm.exe necesita algunas variables de entorno configuradas para implementar una aplicación node.js.
* Configuración del control de acceso mediante la instalación de certificados de seguridad.

Para obtener más información sobre cómo configurar **SetupEntryPoint**, vea [Configuración de la directiva para un punto de entrada del programa de instalación del servicio](service-fabric-application-runas-security.md).

**EnvironmentVariables** proporciona una lista de variables de entorno que se establecen para este paquete de código. Las variables de entorno se pueden invalidar en `ApplicationManifest.xml` para proporcionar valores diferentes para distintas instancias de servicio. 

**DataPackage** declara una carpeta denominada por el atributo **Nombre** que contiene datos estáticos arbitrarios que va a consumir el proceso en tiempo de ejecución.

**ConfigPackage** declara una carpeta denominada por el atributo **Nombre** que contiene un archivo *Settings.xml*. El archivo de configuración contiene secciones de configuración del par clave-valor definido por el usuario que el proceso vuelve a leer en tiempo de ejecución. Durante una actualización, si solo ha cambiado la **versión** de **ConfigPackage**, no se reiniciará el proceso en ejecución. En su lugar, una devolución de llamada notifica el proceso que los ajustes de configuración han cambiado, por lo que pueden volver a cargarse de forma dinámica. Este es un ejemplo de archivo *Settings.xml* :

```xml
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

> [!NOTE]
> Un manifiesto de servicio puede contener varios paquetes de código, configuración y datos, pudiendo tener cada uno de ellos varias versiones de forma independiente.
> 
> 

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application"></a>Describir una aplicación
Mediante declaración, el manifiesto de aplicación describe el tipo de aplicación y la versión. Especifica metadatos de composición de servicios como nombres estables, esquema de particiones, recuento de instancias/factor de replicación, directiva de seguridad y aislamiento, restricciones de ubicación, reemplazos de configuración y tipos de servicio constituyentes. También se describen los dominios de equilibrio de carga en los que se coloca la aplicación.

Por lo tanto, un manifiesto de aplicación describe elementos en el nivel de aplicación y hace referencia a uno o más manifiestos de servicio para componer un tipo de aplicación. Este es un ejemplo sencillo de manifiesto de aplicación:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
    <ConfigOverrides/>
    <EnvironmentOverrides CodePackageRef="MyCode"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
```

Al igual que los manifiestos de servicio, los atributos **Versión** son cadenas no estructuradas y no analizadas por el sistema. Los atributos de versión también se usan para dotar a cada componente de varias versiones para las actualizaciones.

**ServiceManifestImport** contiene referencias a manifiestos de servicio que componen este tipo de aplicación. Los manifiestos de servicio importados determinan qué tipos de servicio son válidos dentro de este tipo de aplicación. Dentro de ServiceManifestImport se invalidan los valores de configuración en Settings.xml y las variables de entorno en los archivos ServiceManifest.xml. 


**DefaultServices** declara instancias de servicio que se crean automáticamente cada vez que se crea una instancia de una aplicación en este tipo de aplicación. Los servicios predeterminados son simplemente una comodidad y se comportan como servicios normales en todos los aspectos después de su creación. Se actualizan junto con cualquier otro servicio de la instancia de aplicación y también se pueden quitar.

> [!NOTE]
> Un manifiesto de aplicación puede contener varias importaciones y servicios predeterminados del manifiesto de servicio. Cada importación del manifiesto de servicio puede tener varias versiones independientemente.
> 
> 

Para información sobre cómo mantener diferentes parámetros de aplicación y servicio para entornos individuales, vea [Administración de los parámetros de la aplicación en varios entornos](service-fabric-manage-multiple-environment-app-configuration.md).

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Pasos siguientes
[Empaquete una aplicación](service-fabric-package-apps.md) y prepárela para la implementación.

En [Implementación y eliminación de aplicaciones][10] se describe cómo usar PowerShell para administrar instancias de las aplicaciones.

[Administración de los parámetros de la aplicación en varios entornos][11] describe cómo configurar parámetros y variables de entorno para diferentes instancias de aplicación.

[Configuración de directivas de seguridad para la aplicación][12] describe cómo ejecutar los servicios a través de directivas de seguridad para restringir el acceso.

Los [Modelos de hospedaje de aplicaciones][13] describen la relación entre las réplicas o las instancias de un servicio implementado y el proceso de host de servicio.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
[13]: service-fabric-hosting-model.md
