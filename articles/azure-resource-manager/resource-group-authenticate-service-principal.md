---
title: "Creación de identidad para aplicaciones de Azure con PowerShell | Microsoft Docs"
description: "Se describe cómo usar Azure PowerShell para crear una entidad de servicio y una aplicación de Azure Active Directory, además de cómo concederle acceso a recursos mediante el control de acceso basado en rol. Muestra cómo autenticar aplicaciones con una contraseña o un certificado."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/03/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: eb6bddbe4220418f7c525985ab6a15524589829e
ms.contentlocale: es-es
ms.lasthandoff: 04/27/2017


---
# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>Uso de Azure PowerShell para crear a una entidad de servicio para acceder a recursos
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [CLI de Azure](resource-group-authenticate-service-principal-cli.md)
> * [Portal](resource-group-create-service-principal-portal.md)
> 
> 

Cuando haya una aplicación o un script que necesite acceder a recursos, puede configurar una identidad para la aplicación y autenticarla con sus propias credenciales. Esta identidad se conoce como una entidad de servicio. Este enfoque le permite:

* Asignar permisos a la identidad de la aplicación que sean diferentes a los suyos propios. Normalmente, estos permisos están restringidos a exactamente aquello que la aplicación debe hacer.
* Usar un certificado para la autenticación al ejecutar un script desatendido.

En este tema se explica cómo usar [Azure PowerShell](/powershell/azure/overview) para configurar todo lo que necesita para que una aplicación se ejecute con sus propias credenciales e identidad.

## <a name="required-permissions"></a>Permisos necesarios
Para completar este tema, debe tener permisos suficientes tanto en su suscripción de Azure como en Azure Active Directory. En concreto, debe poder crear una aplicación en Azure Active Directory y asignar la entidad de servicio a un rol. 

El portal representa la forma más sencilla de comprobar si su cuenta tiene los permisos adecuados. Consulte el [artículo que explica cómo comprobar el permiso requerido](resource-group-create-service-principal-portal.md#required-permissions).

Ahora, continúe en la sección sobre autenticación mediante [contraseña](#create-service-principal-with-password) o [certificado](#create-service-principal-with-certificate).

## <a name="create-service-principal-with-password"></a>Creación de entidad de servicio con contraseña
El script siguiente crea una identidad para una aplicación y la asigna al rol Colaborador del ámbito especificado:

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $Password
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.SubscriptionId
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 # Create Azure Active Directory application with password
 $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ApplicationDisplayName) -IdentifierUris ("http://" + $ApplicationDisplayName) -Password $Password

 # Create Service Principal for the AD app
 $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
 }
```

Algunos elementos que hay que indicar sobre el script:

* Para conceder el acceso de identidad a la suscripción predeterminada, no es preciso proporcionar los parámetros ResourceGroup o SubscriptionId.
* Especifique el parámetro ResourceGroup solo cuando desee limitar el ámbito de la asignación de rol a un grupo de recursos.
* En el caso de las aplicaciones de un solo inquilino, no se validan la página de inicio ni los URI identificadores.
*  En este ejemplo, la entidad de servicio se agrega al rol de colaborador. Para obtener información sobre otros roles, consulte [RBAC: Roles integrados](../active-directory/role-based-access-built-in-roles.md).
* El script entra en suspensión durante 15 segundos para dar tiempo a que la nueva entidad de servicio se propague por Azure Active Directory. Si el script no espera el tiempo suficiente, verá un mensaje de error que dice: "PrincipalNotFound: Principal {id} does not exist in the directory" (PrincipalNotFound: la entidad de servicio {id} no existe en el directorio).
* Si necesita conceder a la entidad de servicio acceso a más suscripciones o grupos de recursos, vuelva a ejecutar el cmdlet `New-AzureRMRoleAssignment` con ámbitos diferentes.


### <a name="provide-credentials-through-powershell"></a>Proporcionar credenciales a través de PowerShell
Ahora, debe iniciar sesión como la aplicación para realizar operaciones. Para el nombre de usuario, use el valor de `ApplicationId` que creó para la aplicación. Para la contraseña, use la que especificó al crear la cuenta. 

```powershell   
$creds = Get-Credential
Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId {tenant-id}
```

El identificador del inquilino no es confidencial, por lo que se puede insertar directamente en el script. Si necesita recuperar el identificador del inquilino, use:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

## <a name="create-service-principal-with-self-signed-certificate"></a>Creación de una entidad de servicio con un certificado autofirmado
Para generar un certificado autofirmado y una entidad de servicio con Azure PowerShell 2.0 en Windows 10 o Windows Server 2016 Technical Preview:

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.SubscriptionId
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
 $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

 # Use Key credentials
 $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ApplicationDisplayName) -IdentifierUris ("http://" + $ApplicationDisplayName) -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore

 $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
 }
```

Algunos elementos que hay que indicar sobre el script:

* Para conceder el acceso de identidad a la suscripción predeterminada, no es preciso proporcionar los parámetros ResourceGroup o SubscriptionId.
* Especifique el parámetro ResourceGroup solo cuando desee limitar el ámbito de la asignación de rol a un grupo de recursos.
* En el caso de las aplicaciones de un solo inquilino, no se validan la página de inicio ni los URI identificadores.
*  En este ejemplo, la entidad de servicio se agrega al rol de colaborador. Para obtener información sobre otros roles, consulte [RBAC: Roles integrados](../active-directory/role-based-access-built-in-roles.md).
* El script entra en suspensión durante 15 segundos para dar tiempo a que la nueva entidad de servicio se propague por Azure Active Directory. Si el script no espera el tiempo suficiente, verá un mensaje de error que dice: "PrincipalNotFound: Principal {id} does not exist in the directory" (PrincipalNotFound: la entidad de servicio {id} no existe en el directorio).
* Si necesita conceder a la entidad de servicio acceso a más suscripciones o grupos de recursos, vuelva a ejecutar el cmdlet `New-AzureRMRoleAssignment` con ámbitos diferentes.

Si **no tiene Windows 10 o Windows Server 2016 Technical Preview**, descargue el [generador de certificados autofirmados](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) del sitio Microsoft Script Center. Extraiga el contenido e importe el cmdlet que necesita.

```powershell  
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```
  
En el script, sustituya las dos líneas siguientes para generar el certificado.
  
```powershell
New-SelfSignedCertificateEx  -StoreLocation CurrentUser -StoreName My -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Proporcionar un certificado a través de un script automatizado de PowerShell
Cada vez que inicie sesión como entidad de servicio, debe proporcionar el id. del inquilino del directorio para la aplicación de AD. Un inquilino es una instancia de Azure Active Directory. Si solo tiene una suscripción, puede utilizar:

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertSubject,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match $CertSubject }).Thumbprint
 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

Ni el identificador de la aplicación ni el identificador del inquilino son confidenciales, por lo que se pueden integrar directamente en el script. Si necesita recuperar el identificador del inquilino, use:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Si necesita recuperar el identificador de la aplicación, use:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Creación de una entidad de servicio con un certificado de una entidad de certificación
Para usar un certificado emitido por una entidad de certificación para crear una entidad de servicio, use el siguiente script:

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

 Login-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -SubscriptionId $SubscriptionId

 $KeyId = (New-Guid).Guid
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
 $KeyCredential.StartDate = $PFXCert.NotBefore
 $KeyCredential.EndDate= $PFXCert.NotAfter
 $KeyCredential.KeyId = $KeyId
 $KeyCredential.CertValue = $KeyValue

 # Use Key credentials
 $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ApplicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential

 $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

Algunos elementos que hay que indicar sobre el script:

* El acceso se limita a la suscripción.
* En el caso de las aplicaciones de un solo inquilino, no se validan la página de inicio ni los URI identificadores.
*  En este ejemplo, la entidad de servicio se agrega al rol de colaborador. Para obtener información sobre otros roles, consulte [RBAC: Roles integrados](../active-directory/role-based-access-built-in-roles.md).
* El script entra en suspensión durante 15 segundos para dar tiempo a que la nueva entidad de servicio se propague por Azure Active Directory. Si el script no espera el tiempo suficiente, verá un mensaje de error que dice: "PrincipalNotFound: Principal {id} does not exist in the directory" (PrincipalNotFound: la entidad de servicio {id} no existe en el directorio).
* Si necesita conceder a la entidad de servicio acceso a más suscripciones o grupos de recursos, vuelva a ejecutar el cmdlet `New-AzureRMRoleAssignment` con ámbitos diferentes.

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
 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
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
Remove-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -All
```

Para agregar una contraseña, use:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -Password p@ssword!
```

Para agregar un valor de certificado, cree un certificado autofirmado, como se muestra en este tema. A continuación, use:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
```

## <a name="save-access-token-to-simplify-log-in"></a>Guardado del token de acceso para simplificar el inicio de sesión
Para evitar tener que especificar las credenciales de la entidad de servicio cada vez que necesite iniciar sesión, puede guardar el token de acceso.

Para usar el token de acceso actual en una sesión posterior, guarde el perfil.
   
```powershell
Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```
   
Abra el perfil y examine su contenido. Observe que contiene un token de acceso. En lugar de tener que volver a iniciar sesión manualmente, solo tiene que cargar el perfil.
   
```powershell
Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```

> [!NOTE]
> El token de acceso expirará y, por lo tanto, el uso de un perfil guardado solo funcionará mientras el token sea válido.
>  

Como alternativa, puede invocar operaciones de REST desde PowerShell para iniciar sesión. De la respuesta de autenticación, puede recuperar el token de acceso para su uso con otras operaciones. Para ver un ejemplo de recuperación del token de acceso mediante la invocación de operaciones REST, consulte [Generación de un token de acceso](resource-manager-rest-api.md#generating-an-access-token).

## <a name="debug"></a>Depurar

Durante la creación de una entidad de servicio pueden producirse los siguientes errores:

* **"Authentication_Unauthorized"** o **"No subscription found in the context"** (No se encuentra una suscripción en el contexto). - Este error se ve cuando la cuenta no tiene los [permisos necesarios](#required-permissions) en Azure Active Directory para registrar una aplicación. Por lo general, este error aparece cuando los usuarios administradores de Azure Active Directory son los únicos que pueden registrar las aplicaciones y la cuenta no es de un administrador. Pida al administrador que le asigne un rol de administrador o que permita a los usuarios registrar aplicaciones.

* Su cuenta **"no tiene autorización para realizar la acción 'Microsoft.Authorization/roleAssignments/write' en el ámbito '/ subscriptions / {guid}'."**  - Este error aparece cuando la cuenta no tiene permisos suficientes para asignar un rol a una identidad. Pida al administrador de suscripciones que le agregue al rol de administrador de acceso de usuario.

## <a name="sample-applications"></a>Aplicaciones de ejemplo
Las aplicaciones de ejemplo siguientes muestran cómo iniciar sesión como entidad de servicio.

**.NET**

* [Implementación de una máquina virtual habilitada para SSH con una plantilla con .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
* [Administración de recursos y grupos de recursos de Azure con .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

* [Introducción a recursos - Implementación mediante la plantilla de Azure Resource Manager - en Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
* [Introducción a recursos - Administración de grupo de recursos - en Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

* [Implementación de una máquina virtual habilitada para SSH con una plantilla en Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
* [Administración de recursos y grupos de recursos de Azure con Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

* [Implementación de una máquina virtual habilitada para SSH con una plantilla en Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
* [Administración de recursos y grupos de recursos de Azure con Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

* [Implementación de una máquina virtual habilitada para SSH con una plantilla en Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
* [Administración de recursos y grupos de recursos de Azure con Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Pasos siguientes
* Si desea conocer los pasos detallados de la integración de una aplicación en Azure para administrar recursos, consulte [Guía del desarrollador para la autorización con la API de Azure Resource Manager](resource-manager-api-authentication.md).
* Para obtener una explicación más detallada de las aplicaciones y entidades de servicio, consulte [Objetos de aplicación y de entidad de servicio](../active-directory/active-directory-application-objects.md). 
* Para más información sobre la autenticación de Azure Active Directory, consulte [Escenarios de autenticación para Azure AD](../active-directory/active-directory-authentication-scenarios.md).


