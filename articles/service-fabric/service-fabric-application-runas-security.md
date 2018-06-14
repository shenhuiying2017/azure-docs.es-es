---
title: Ejecución de un servicio de Azure Service Fabric en cuentas de seguridad del sistema y locales | Microsoft Docs
description: Aprenda cómo ejecutar una aplicación de Service Fabric en cuentas de seguridad del sistema y locales.  Cree entidades de seguridad y aplique la directiva RunAs para ejecutar los servicios de forma segura.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: mfussell
ms.openlocfilehash: 33ca23834f35e631c6943ec22a88f4fe3dc853e1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212405"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Ejecución de un servicio como cuenta de usuario local o cuenta de sistema local
Azure Service Fabric le permite proteger aplicaciones que se ejecutan en distintas cuentas de usuario en el clúster. De forma predeterminada, las aplicaciones de Service Fabric se ejecutan en la misma cuenta en que se ejecuta el proceso Fabric.exe. Service Fabric también proporciona la funcionalidad de ejecutar aplicaciones con una cuenta de usuario o sistema local. Los tipos de cuenta de sistema local compatibles son **LocalUser**, **NetworkService**, **LocalService** y **LocalSystem**.  Si va a ejecutar Service Fabric en un clúster de Windows independiente, puede ejecutar un servicio en [cuentas de dominio de Active Directory](service-fabric-run-service-as-ad-user-or-group.md) o en [cuentas de servicio administradas de grupo](service-fabric-run-service-as-gmsa.md).

En el manifiesto de aplicación, se definen las cuentas de usuario necesarias para ejecutar los servicios o proteger los recursos en la sección **Principals**. También se pueden definir y crear grupos de usuarios, de modo que se puedan administrar uno o varios usuarios de forma conjunta. Esto es útil cuando hay varios usuarios para distintos puntos de entrada de servicio y necesitan privilegios comunes disponibles en el nivel de grupo.  Después, se hace referencia a los usuarios en una directiva RunAs, que se aplica a un servicio específico o a todos los servicios de la aplicación. 

De forma predeterminada, la directiva RunAs se aplica en el punto de entrada principal.  También puede aplicar una directiva RunAs al punto de entrada del programa de instalación, si necesita [ejecutar determinadas operaciones de instalación con privilegios elevados bajo una cuenta de sistema](service-fabric-run-script-at-service-startup.md), o tanto a puntos de entrada principales como de instalación.  

> [!NOTE] 
> Si aplica una directiva de RunAs a un servicio y el manifiesto de servicio declara los recursos de punto de conexión con el protocolo HTTP, debe especificar **SecurityAccessPolicy**.  Para obtener más información, consulte [Assign a security access policy for HTTP and HTTPS endpoints](service-fabric-assign-policy-to-endpoint.md) (Asignación de una directiva de acceso de seguridad a los puntos de conexión HTTP y HTTPS). 
>

## <a name="run-a-service-as-a-local-user"></a>Ejecución de un servicio como usuario local
Puede crear un usuario local para proteger un servicio dentro de la aplicación. Si se especifica un tipo de cuenta **LocalUser** en la sección de entidades de seguridad del manifiesto de aplicación, Service Fabric crea cuentas de usuario locales en las máquinas donde se implementa la aplicación. De forma predeterminada, los nombres de dichas cuentas no coinciden con los que se especifican en el manifiesto de aplicación (por ejemplo, *Customer3* en el ejemplo siguiente de manifiesto de aplicación). En su lugar, se generan dinámicamente y tienen contraseñas aleatorias.

En la sección **RunAsPolicy** de **ServiceManifestImport**, especifica la cuenta de usuario de la sección **Principals** para ejecutar el paquete de código de servicio.  En el ejemplo siguiente se muestra cómo crear un usuario local y aplicar una directiva RunAs al punto de entrada principal:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="Customer3" />
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="create-a-local-user-group"></a>Creación de un grupo de usuarios local
Puede crear grupos de usuarios y agregar uno o varios usuarios al grupo. Esto es útil si hay varios usuarios para distintos puntos de entrada de servicio y es preciso que tengan ciertos privilegios comunes disponibles en el nivel de grupo. En el ejemplo siguiente de manifiesto de aplicación se muestra un grupo local denominado *LocalAdminGroup* que tiene privilegios de administrador. Dos usuarios, *Customer1* y *Customer2*, se convierten en miembros de este grupo local. En la sección **ServiceManifestImport**, se aplica una directiva RunAs para ejecutar el paquete de código *Stateful1Pkg* como *Customer2*.  Se aplica otra directiva RunAs para ejecutar el paquete de código *Web1Pkg* como *Customer1*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer2" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="Stateful1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Groups>
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <User Name="Customer1">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <User Name="Customer2">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Aplicación de una directiva predeterminada a todos los paquetes de código de servicio
La sección **DefaultRunAsPolicy** se usa para especificar una cuenta de usuario predeterminada para todos los paquetes de código que no tienen definido ningún parámetro **RunAsPolicy** específico. Si la mayoría de los paquetes de código especificados en el manifiesto de servicio que usa una aplicación deben ejecutarse en el mismo usuario, la aplicación solo puede definir una directiva RunAs predeterminada con dicha cuenta de usuario. En el ejemplo siguiente, se especifica que si un paquete de código no tiene una directiva **RunAsPolicy** especificada, dicho paquete tendrá que ejecutarse en el usuario **MyDefaultAccount** especificado en la sección de entidades de seguridad.  Los tipos de cuenta admitidos son LocalUser, NetworkService, LocalSystem y LocalService.  Si utiliza un servicio o usuario local, especifique también el nombre de cuenta y la contraseña.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <Policies>
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
  </Policies>
</ApplicationManifest>
```

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Depuración de un paquete de código localmente mediante el redireccionamiento de consola
En ocasiones, resulta útil ver la salida de la consola tras ejecutar un servicio con fines de depuración. Puede establecer una directiva de redireccionamiento de consola en el punto de entrada del manifiesto de servicio, que escribe la salida en un archivo. La salida del archivo se escribe en la carpeta de aplicación denominada **log** en el nodo del clúster donde se ha implementado y ejecutado la aplicación. 

> [!WARNING]
> No use nunca la directiva de redirección de la consola en una aplicación implementada en producción, ya que esto puede afectar a la capacidad de conmutación por error de la aplicación. *Solo* debe usarla con fines de depuración y desarrollo local.  
> 
> 

En el ejemplo de manifiesto de servicio siguiente se muestra cómo habilitar el redireccionamiento de consola con un valor de FileRetentionCount:

```xml
<CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
        <ConsoleRedirection FileRetentionCount="10"/>
      </ExeHost>
    </EntryPoint>
</CodePackage>

```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
* [Entender el modelo de aplicación](service-fabric-application-model.md)
* [Especificación de los recursos en un manifiesto de servicio](service-fabric-service-manifest-resources.md)
* [Implementar una aplicación](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
