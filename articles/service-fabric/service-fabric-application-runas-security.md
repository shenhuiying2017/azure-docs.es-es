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
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 3df5374911ee6381f25d08d23d565cdf8a7cd12f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2018
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Ejecución de un servicio como cuenta de usuario local o cuenta de sistema local
Azure Service Fabric le permite proteger aplicaciones que se ejecutan en distintas cuentas de usuario en el clúster. De forma predeterminada, las aplicaciones de Service Fabric se ejecutan en la misma cuenta en que se ejecuta el proceso Fabric.exe. Service Fabric también permite ejecutar aplicaciones en una cuenta de usuario local o una cuenta de sistema local, lo que se logra al especificar la directiva RunAs en el manifiesto de la aplicación. Los tipos de cuenta de sistema local compatibles son **LocalUser**, **NetworkService**, **LocalService** y **LocalSystem**.  Si va a ejecutar Service Fabric en un clúster de Windows independiente, puede ejecutar un servicio en [cuentas de dominio de Active Directory](service-fabric-run-service-as-ad-user-or-group.md) o en [cuentas de servicio administradas de grupo](service-fabric-run-service-as-gmsa.md).

También se pueden definir y crear grupos de usuarios, de modo que se puedan agregar uno o varios usuarios a cada grupo para poder administrarlos de forma conjunta. Esto es útil cuando hay varios usuarios para distintos puntos de entrada de servicio y es preciso que tengan ciertos privilegios comunes disponibles en el nivel de grupo.

> [!NOTE] 
> Si aplica una directiva de RunAs a un servicio y el manifiesto de servicio declara los recursos de punto de conexión con el protocolo HTTP, debe especificar **SecurityAccessPolicy**.  Para obtener más información, consulte [Assign a security access policy for HTTP and HTTPS endpoints](service-fabric-assign-policy-to-endpoint.md) (Asignación de una directiva de acceso de seguridad a los puntos de conexión HTTP y HTTPS). 
>

## <a name="create-local-user-groups"></a>Creación de grupos de usuarios locales
Se pueden definir y crear grupos de usuarios que permitan agregar uno o varios usuarios a un grupo. Esto es útil si hay varios usuarios para distintos puntos de entrada de servicio y es preciso que tengan ciertos privilegios comunes disponibles en el nivel de grupo. En el ejemplo siguiente se muestra un grupo local denominado **LocalAdminGroup** con privilegios de administrador. Dos usuarios, Customer1 y Customer2, se convierten en miembros de este grupo local en este ejemplo de manifiesto de aplicación:

```xml
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
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
```

## <a name="create-local-users"></a>Creación de usuarios locales
Puede crear un usuario local para proteger un servicio dentro de la aplicación. Si se especifica un tipo de cuenta **LocalUser** en la sección de entidades de seguridad del manifiesto de aplicación, Service Fabric crea cuentas de usuario locales en las máquinas donde se implementa la aplicación. De forma predeterminada, los nombres de dichas cuentas no coinciden con los que se especifican en el manifiesto de aplicación (por ejemplo, Customer3 en el ejemplo siguiente de manifiesto de aplicación). En su lugar, se generan dinámicamente y tienen contraseñas aleatorias.

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

Si una aplicación requiere que la cuenta de usuario y la contraseña coincidan en todas las máquinas (por ejemplo, para habilitar la autenticación NTLM), el manifiesto de clúster debe establecer **NTLMAuthenticationEnabled** en true. El manifiesto de clúster también debe especificar un **NTLMAuthenticationPasswordSecret** que se use para generar la misma contraseña en todos los equipos.

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

## <a name="assign-policies-to-the-service-code-packages"></a>Asignación de directivas a los paquetes de código de servicio
En la sección **RunAsPolicy** de **ServiceManifestImport**, se especifica la cuenta de la sección de entidades de seguridad que debe usarse para ejecutar un paquete de código. También se asocian los paquetes de código del manifiesto de servicio con las cuentas de usuario de la sección de entidades de seguridad. Puede especificarlo para los puntos de entrada de configuración o principales, o bien puede especificar `All` para que se aplique a ambos. En el ejemplo siguiente se muestra la aplicación de diferentes directivas:

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

Si no se especifica **EntryPointType**, el valor predeterminado se establece en `EntryPointType=”Main”`. El uso de **SetupEntryPoint** es especialmente útil si lo que se desea es ejecutar determinadas operaciones de instalación con privilegios elevados en una cuenta de sistema. Para obtener más información, consulte [Ejecución de un script de inicio de servicio como cuenta de usuario local o del sistema](service-fabric-run-script-at-service-startup.md). El código de servicio real puede ejecutarse en una cuenta con menos privilegios.

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Aplicación de una directiva predeterminada a todos los paquetes de código de servicio
La sección **DefaultRunAsPolicy** se usa para especificar una cuenta de usuario predeterminada para todos los paquetes de código que no tienen definido ningún parámetro **RunAsPolicy** específico. Si la mayoría de los paquetes de código especificados en el manifiesto de servicio que usa una aplicación deben ejecutarse en el mismo usuario, la aplicación solo puede definir una directiva RunAs predeterminada con dicha cuenta de usuario. En el ejemplo siguiente, se especifica que si un paquete de código no tiene una directiva **RunAsPolicy** especificada, tendrá que ejecutarse en la directiva **MyDefaultAccount** especificada en la sección de entidades de seguridad.

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
* [Entender el modelo de aplicación](service-fabric-application-model.md)
* [Especificación de los recursos en un manifiesto de servicio](service-fabric-service-manifest-resources.md)
* [Implementar una aplicación](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
