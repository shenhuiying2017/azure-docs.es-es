---
title: Ejecución de un servicio de Azure Service Fabric como un grupo o usuario de AD | Microsoft Docs
description: Obtenga información acerca de cómo ejecutar un servicio como un grupo o un usuario de Active Directory en un clúster independiente de Windows de Service Fabric.
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
ms.openlocfilehash: 624cb54778950a8a973bcf6cefddf66cba2bd0fe
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206999"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Ejecución de un servicio como un grupo o usuario de Active Directory
En un clúster independiente de Windows Server, puede ejecutar un servicio como un usuario o grupo de Active Directory mediante una directiva de RunAs.  De forma predeterminada, las aplicaciones de Service Fabric se ejecutan en la misma cuenta en que se ejecuta el proceso Fabric.exe. La ejecución de aplicaciones en cuentas diferentes, incluso en un entorno hospedado compartido, aumenta la seguridad entre aplicaciones. Tenga en cuenta que esto usa Active Directory local dentro del dominio y no en Azure Active Directory (Azure AD).  También puede ejecutar un servicio como una [cuenta de servicio administrada de grupo (gMSA)](service-fabric-run-service-as-gmsa.md).

Mediante el uso de un grupo o usuario de dominio puede tener acceso a otros recursos del dominio (por ejemplo, recursos compartidos de archivos) para los que se han concedido permisos.

El ejemplo siguiente muestra un usuario de Active Directory denominado *TestUser* con la contraseña de dominio cifrada mediante un certificado llamado *MyCert*. Puede usar el comando de PowerShell `Invoke-ServiceFabricEncryptText` para crear el texto cifrado secreto. Vea [Administración de secretos en aplicaciones de Service Fabric](service-fabric-application-secret-management.md) | Microsoft Azure.

La clave privada del certificado para descifrar la contraseña se debe implementar en la máquina local con un método fuera de banda (en Azure esto se realiza mediante Azure Resource Manager). Posteriormente, cuando Service Fabric implemente el paquete de servicio en la máquina, podrá descifrar el secreto y, junto con el nombre de usuario, autenticarse mediante Active Directory para ejecutarse con esas credenciales.

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```

> [!NOTE] 
> Si aplica una directiva de RunAs a un servicio y el manifiesto de servicio declara los recursos de punto de conexión con el protocolo HTTP, también debe especificar **SecurityAccessPolicy**.  Para obtener más información, consulte [Assign a security access policy for HTTP and HTTPS endpoints](service-fabric-assign-policy-to-endpoint.md) (Asignación de una directiva de acceso de seguridad a los puntos de conexión HTTP y HTTPS). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
A continuación, lea los siguientes artículos:
* [Entender el modelo de aplicación](service-fabric-application-model.md)
* [Especificación de los recursos en un manifiesto de servicio](service-fabric-service-manifest-resources.md)
* [Implementar una aplicación](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
