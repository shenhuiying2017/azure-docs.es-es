---
title: Creación de identidad para aplicaciones de Azure con PowerShell | Microsoft Docs
description: Se describe cómo usar Azure PowerShell para crear una entidad de servicio y una aplicación de Azure Active Directory, además de cómo concederle acceso a recursos mediante el control de acceso basado en rol. Muestra cómo autenticar la aplicación con un certificado.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/10/2018
ms.author: tomfitz
ms.openlocfilehash: 3dc62ba21458cc91d1858e11e7abbb6e96b0d5dd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358955"
---
# <a name="use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Uso de Azure PowerShell para crear una entidad de servicio con un certificado

Cuando haya una aplicación o un script que necesite acceder a recursos, puede configurar una identidad para la aplicación y autenticarla con sus propias credenciales. Esta identidad se conoce como una entidad de servicio. Este enfoque le permite:

* Asignar permisos a la identidad de la aplicación que sean diferentes a los suyos propios. Normalmente, estos permisos están restringidos a exactamente aquello que la aplicación debe hacer.
* Usar un certificado para la autenticación al ejecutar un script desatendido.

> [!IMPORTANT]
> En lugar de crear una entidad de servicio, considere el uso de la identidad de servicio administrada de Azure AD para la identidad de la aplicación. La identidad de servicio administrada de Azure AD es una característica pública en versión preliminar de Azure Active Directory que simplifica la creación de una identidad para el código. Si el código se ejecuta en un servicio que admite la a identidad de servicio administrada de Azure AD y tiene acceso a recursos que admiten la autenticación de Azure Active Directory, la identidad de servicio administrada de Azure AD es una opción mejor para usted. Para obtener más información sobre la identidad de servicio administrada de Azure AD, incluidos los servicios que actualmente lo admiten, consulte [Managed Service Identity for Azure resources](../active-directory/managed-service-identity/overview.md) (Identidad de servicio administrada para recursos de Azure).

En este artículo se muestra cómo crear una entidad de servicio que se autentica con un certificado. Para configurar una entidad de servicio con contraseña, consulte [Creación de una entidad de servicio de Azure con Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Debe tener la [versión más reciente](/powershell/azure/get-started-azureps) de PowerShell para este artículo.

## <a name="required-permissions"></a>Permisos necesarios

Para completar este artículo, debe tener permisos suficientes tanto en su suscripción de Azure como en Azure Active Directory. En concreto, debe poder crear una aplicación en Azure Active Directory y asignar la entidad de servicio a un rol.

El portal representa la forma más sencilla de comprobar si su cuenta tiene los permisos adecuados. Consulte el [artículo que explica cómo comprobar el permiso requerido](resource-group-create-service-principal-portal.md#required-permissions).

## <a name="create-service-principal-with-self-signed-certificate"></a>Creación de una entidad de servicio con un certificado autofirmado

En el ejemplo siguiente se trata un escenario sencillo. En él se usa [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) para crear una entidad de servicio con un certificado autofirmado, y se utiliza[New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) para asignar el rol [Colaborador](../role-based-access-control/built-in-roles.md#contributor) a la entidad de servicio. La asignación de roles se limita a su suscripción de Azure seleccionada actualmente. Para seleccionar una suscripción diferente, use [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext).

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

El ejemplo entra en suspensión durante 20 segundos para dar tiempo a que la nueva entidad de servicio se propague por Azure Active Directory. Si el script no espera el tiempo suficiente, verá un error que dice: "La entidad de seguridad {ID} no existe en el directorio {DIR-ID}". Para resolver este error, espere unos instantes y, a continuación, ejecute el comando **New-AzureRmRoleAssignment** de nuevo.

Puede restringir el ámbito de la asignación de roles a un grupo de recursos específico mediante el parámetro **ResourceGroupName**. Puede restringir el ámbito a un recurso concreto si usa los parámetros **ResourceType** y **ResourceName**. 

Si **no tiene Windows 10 o Windows Server 2016**, descargue el [generador de certificados autofirmados](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) del sitio Microsoft Script Center. Extraiga el contenido e importe el cmdlet que necesita.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

En el script, sustituya las dos líneas siguientes para generar el certificado.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Proporcionar un certificado a través de un script automatizado de PowerShell

Cada vez que inicie sesión como entidad de servicio, debe proporcionar el id. del inquilino del directorio para la aplicación de AD. Un inquilino es una instancia de Azure Active Directory.

```powershell
$TenantId = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzureRmADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Creación de una entidad de servicio con un certificado de una entidad de certificación

En el ejemplo siguiente se utiliza un certificado emitido por una entidad de certificación para crear una entidad de servicio. La asignación se limita a la suscripción de Azure especificada. Se agrega la entidad de servicio al rol [Colaborador](../role-based-access-control/built-in-roles.md#contributor). Si se produce un error durante la asignación de roles, vuelva a intentar la asignación.

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Connect-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzureRmADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Proporcionar un certificado a través de un script automatizado de PowerShell
Cada vez que inicie sesión como entidad de servicio, debe proporcionar el id. del inquilino del directorio para la aplicación de AD. Un inquilino es una instancia de Azure Active Directory.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Connect-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

Ni el identificador de la aplicación ni el identificador del inquilino son confidenciales, por lo que se pueden integrar directamente en el script. Si necesita recuperar el identificador del inquilino, use:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Si necesita recuperar el identificador de la aplicación, use:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Cambio de credenciales

Para cambiar las credenciales de una aplicación de AD, ya sea debido a un riesgo de seguridad o a la expiración de una credencial, use los cmdlets [Remove-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) y [New-AzureRmADAppCredential](/powershell/module/azurerm.resources/new-azurermadappcredential).

Para quitar todas las credenciales de una aplicación, use:

```powershell
Get-AzureRmADApplication -DisplayName exampleapp | Remove-AzureRmADAppCredential
```

Para agregar un valor de certificado, cree un certificado autofirmado, como se muestra en este artículo. A continuación, use:

```powershell
Get-AzureRmADApplication -DisplayName exampleapp | New-AzureRmADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Depurar

Durante la creación de una entidad de servicio pueden producirse los siguientes errores:

* **"Authentication_Unauthorized"** o **"No subscription found in the context"** (No se encuentra una suscripción en el contexto). - Este error se ve cuando la cuenta no tiene los [permisos necesarios](#required-permissions) en Azure Active Directory para registrar una aplicación. Por lo general, este error aparece cuando los usuarios administradores de Azure Active Directory son los únicos que pueden registrar las aplicaciones y la cuenta no es de un administrador. Pida al administrador que le asigne un rol de administrador o que permita a los usuarios registrar aplicaciones.

* Su cuenta **"no tiene autorización para realizar la acción 'Microsoft.Authorization/roleAssignments/write' en el ámbito '/ subscriptions / {guid}'."**  - Este error aparece cuando la cuenta no tiene permisos suficientes para asignar un rol a una identidad. Pida al administrador de suscripciones que le agregue al rol de administrador de acceso de usuario.

## <a name="next-steps"></a>Pasos siguientes
* Para configurar una entidad de servicio con contraseña, consulte [Creación de una entidad de servicio de Azure con Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* Si desea conocer los pasos detallados de la integración de una aplicación en Azure para administrar recursos, consulte [Guía del desarrollador para la autorización con la API de Azure Resource Manager](resource-manager-api-authentication.md).
* Para obtener una explicación más detallada de las aplicaciones y entidades de servicio, consulte [Objetos de aplicación y de entidad de servicio](../active-directory/active-directory-application-objects.md). 
* Para más información sobre la autenticación de Azure Active Directory, consulte [Escenarios de autenticación para Azure AD](../active-directory/active-directory-authentication-scenarios.md).
