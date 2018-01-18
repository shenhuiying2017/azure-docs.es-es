---
title: "Creación de una cuenta de ejecución de Azure Automation | Microsoft Docs"
description: "Este artículo describe cómo actualizar la cuenta de Automation y crear cuentas de ejecución con PowerShell o desde el portal."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.openlocfilehash: 74d363be48972b40ba6a50b845acea78e1b5cc20
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="update-your-automation-account-authentication-with-run-as-accounts"></a>Actualizar la autenticación de la cuenta de Automation con cuentas de ejecución 
Puede actualizar una cuenta de Automation existente desde Azure Portal o mediante PowerShell si:

* Crea una cuenta de Automation, pero se rechaza la creación de la cuenta de ejecución.
* Ya usa una cuenta de Automation para administrar recursos de Resource Manager y quiere actualizarla para incluir la cuenta de ejecución para la autenticación de runbooks.
* Ya usa una cuenta de Automation para administrar recursos del modelo clásico y quiere actualizarla para usar la cuenta de ejecución en lugar de crear una nueva cuenta y migrar los runbooks y recursos a ella.   

El proceso crea los siguientes elementos en su cuenta de Automation.

**Para cuentas de ejecución:**

* Crea una aplicación de Azure AD con un certificado autofirmado, crea una cuenta de entidad servicio para la aplicación en Azure AD y asigna el rol Colaborador para esta cuenta en la suscripción actual. Puede cambiar esta configuración a Propietario o cualquier otro rol. Para más información, consulte [Control de acceso basado en rol en Azure Automation](automation-role-based-access-control.md).
* Crea un recurso de certificado de Automation llamado *AzureRunAsCertificate* en la cuenta de Automation especificada. El recurso de certificado contiene la clave privada del certificado que usa la aplicación de Azure AD.
* Crea un recurso de conexión de Automation llamado *AzureRunAsConnection* en la cuenta de Automation especificada. El recurso de conexión contiene el id. de aplicación, el id. de inquilino, el id. de suscripción y la huella digital de certificado.

**Para cuentas de ejecución clásicas:**

* Crea un recurso de certificado de Automation llamado *AzureClassicRunAsCertificate* en la cuenta de Automation especificada. El recurso de certificado contiene la clave privada del certificado que usa el certificado de administración.
* Crea un recurso de conexión de Automation llamado *AzureClassicRunAsConnection* en la cuenta de Automation especificada. El recurso de conexión contiene el nombre de la suscripción, el id. de suscripción y el nombre del recurso de certificado.

## <a name="prerequisites"></a>Requisitos previos
Si elige [usar PowerShell para crear las cuentas de ejecución](#create-run-as-account-using-powershell), necesitará para este proceso:

* Windows 10 y Windows Server 2016 con módulos de Azure Resource Manager 3.4.1 y versiones posteriores. El script de PowerShell no admite versiones anteriores de Windows.
* Azure PowerShell 1.0 y versiones superiores. Para más información sobre la versión 1.0 de PowerShell, consulte [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Una cuenta de Automation, a la que se hace referencia como el valor de los parámetros *–AutomationAccountName* y *-ApplicationDisplayName*.

Para obtener los valores de *SubscriptionID*, *ResourceGroup* y *AutomationAccountName*, que son parámetros necesarios para el script, haga lo siguiente:

1. En Azure Portal, haga clic en **Más servicios**, en la esquina inferior izquierda. En la lista de recursos, escriba **Automation**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Cuentas de Automation**.
2. En la página Cuenta de Automation, seleccione su cuenta de Automation y, en **Configuración de la cuenta**, seleccione **Propiedades**.  
3. Observe los valores de la página **Propiedades**.<br><br> ![Hoja "Propiedades" de la cuenta de Automation](media/automation-create-runas-account/automation-account-properties.png)  

### <a name="required-permissions-to-update-your-automation-account"></a>Privilegios necesarios para actualizar la cuenta de Automation
Para actualizar una cuenta de Automation, debe tener los siguientes privilegios y permisos, necesarios para completar este tema.   
 
* Debe agregarse la cuenta de usuario de AD a un rol con los privilegios equivalentes a los del rol Colaborador para los recursos de Microsoft Automation, como se resalta en el artículo [Control de acceso basado en rol en Azure Automation](automation-role-based-access-control.md#contributor-role-permissions).  
* Los usuarios que no son administradores en el inquilino de Azure AD pueden [registrar aplicaciones de AD](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions) si la opción **Los usuarios pueden registrar aplicaciones** del inquilino de Azure AD en la página **Configuración de usuario** está establecida en **Sí**. Si se establecen en **No**, el usuario que realice esta acción debe ser administrador global de Azure AD.

Si no es miembro de la instancia de Active Directory de la suscripción antes de que le agreguen al rol de administrador global/coadministrador de esta última, se le agregará a Active Directory como invitado. En este caso, recibirá una advertencia tipo "No tiene permisos para crear..." en la hoja **Agregar cuenta de Automation**. Los usuarios que primero se agregaron al rol de administrador global/coadministrador se pueden quitar de la instancia de Active Directory de la suscripción y volverse a agregar para convertirlos en usuarios completos en Active Directory. Para comprobar esta situación, en el panel de **Azure Active Directory** de Azure Portal, seleccione **Usuarios y grupos**, **All Users** y, después de seleccionar el usuario específico, seleccione **Perfil**. El valor del atributo **Tipo de usuario** del perfil de los usuarios no debería ser **Invitado**.

## <a name="create-run-as-account-from-the-portal"></a>Creación de una cuenta de ejecución desde el portal
En esta sección, realizará los pasos que se describen a continuación para actualizar su cuenta de Azure Automation en Azure Portal.  Las cuentas de ejecución y de ejecución clásica se crean de forma individual. Si no es necesario administrar los recursos clásicos, basta con crear la cuenta de ejecución de Azure.  

1. Inicie sesión en Azure Portal con una cuenta que sea miembro del rol Administradores de suscripciones y coadministrador de la suscripción.
2. En Azure Portal, haga clic en **Más servicios**, en la esquina inferior izquierda. En la lista de recursos, escriba **Automation**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Cuentas de Automation**.
3. En la página **Cuentas de Automation**, seleccione su cuenta de Automation en la lista Cuentas de Automation.
4. En el panel izquierdo, seleccione **Cuentas de ejecución** en la sección **Configuración de la cuenta**.  
5. En función de la cuenta que necesite, seleccione **Cuenta de ejecución de Azure** o **Cuenta de ejecución de Azure clásica**.  Después de seleccionar **Agregar cuenta de ejecución de Azure** o **Agregar cuenta de ejecución de Azure clásico**, aparece el panel. Revise la información general y haga clic en **Crear** para continuar con la creación de la cuenta de ejecución.  
6. Mientras Azure crea la cuenta de ejecución, se puede seguir el progreso en **Notificaciones** en el menú.  También se muestra un banner que indica que se está creando la cuenta.  Este proceso puede tardar unos minutos en completarse.  

## <a name="create-run-as-account-using-powershell-script"></a>Creación de una cuenta de ejecución con un script de PowerShell
Este script de PowerShell incluye compatibilidad con las siguientes configuraciones:

* Creación de una cuenta de ejecución mediante un certificado autofirmado.
* Creación de una cuenta de ejecución y una cuenta de ejecución clásica mediante un certificado autofirmado.
* Cree una cuenta de ejecución y una cuenta de ejecución clásica mediante un certificado emitido por su entidad de certificación (CA) empresarial.
* Creación de una cuenta de ejecución y una cuenta de ejecución clásica mediante un certificado autofirmado en la nube de Azure Government.

>[!NOTE]
> Si selecciona cualquiera de las opciones para crear una cuenta de ejecución clásica, cargue el certificado público (extensión de nombre del archivo .cer) en el almacén de administración para la suscripción en la que se creó la cuenta de Automation.
> 

1. Guarde el script siguiente en el equipo. En este ejemplo, guárdelo con el nombre *New-RunAsAccount.ps1*.

         #Requires -RunAsAdministrator
         Param (
         [Parameter(Mandatory=$true)]
         [String] $ResourceGroup,

         [Parameter(Mandatory=$true)]
         [String] $AutomationAccountName,

         [Parameter(Mandatory=$true)]
         [String] $ApplicationDisplayName,

         [Parameter(Mandatory=$true)]
         [String] $SubscriptionId,

         [Parameter(Mandatory=$true)]
         [Boolean] $CreateClassicRunAsAccount,

         [Parameter(Mandatory=$true)]
         [String] $SelfSignedCertPlainPassword,

         [Parameter(Mandatory=$false)]
         [String] $EnterpriseCertPathForRunAsAccount,

         [Parameter(Mandatory=$false)]
         [String] $EnterpriseCertPlainPasswordForRunAsAccount,

         [Parameter(Mandatory=$false)]
         [String] $EnterpriseCertPathForClassicRunAsAccount,

         [Parameter(Mandatory=$false)]
         [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

         [Parameter(Mandatory=$false)]
         [ValidateSet("AzureCloud","AzureUSGovernment")]
         [string]$EnvironmentName="AzureCloud",

         [Parameter(Mandatory=$false)]
         [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
         )

         function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
                                        [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
         $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
             -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
             -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

         $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
         Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
         Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
         }

         function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
         $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
         $keyId = (New-Guid).Guid

         $startDate = Get-Date
         $endDate = (Get-Date $PfxCert.GetExpirationDateString()).AddDays(-1)
         
         #Create an Azure AD application, AD App Credential, AD ServicePrincipal
         $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId) 
         $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $startDate -EndDate $endDate 
         $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
         $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

         # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
         Sleep -s 15
         $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
         $Retries = 0;
         While ($NewRole -eq $null -and $Retries -le 6)
         {
             Sleep -s 10
             New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
             $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
             $Retries++;
         }
             return $Application.ApplicationId.ToString();
         }

         function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName,[string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
         $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
         Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
         New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
         }

         function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
         Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
         New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
         }

         Import-Module AzureRM.Profile
         Import-Module AzureRM.Resources

         $AzureRMProfileVersion= (Get-Module AzureRM.Profile).Version
         if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3)))
         {
             Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
             return
         }

         Login-AzureRmAccount -Environment $EnvironmentName 
         $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

         # Create a Run As account by using a service principal
         $CertifcateAssetName = "AzureRunAsCertificate"
         $ConnectionAssetName = "AzureRunAsConnection"
         $ConnectionTypeName = "AzureServicePrincipal"

         if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
         $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
         $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
         } else {
            $CertificateName = $AutomationAccountName+$CertifcateAssetName
            $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
            $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
            $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
            CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
         }

         # Create a service principal
         $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
         $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

         # Create the Automation certificate asset
         CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

         # Populate the ConnectionFieldValues
         $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
         $TenantID = $SubscriptionInfo | Select TenantId -First 1
         $Thumbprint = $PfxCert.Thumbprint
         $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

         # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
         CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

         if ($CreateClassicRunAsAccount) {
              # Create a Run As account by using a service principal
              $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
              $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
              $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
              $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
                      "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
                      "Then click Upload and upload the .cer format of #CERT#"

               if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
               $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
               $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
               $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
         } else {
               $ClassicRunAsAccountCertificateName = $AutomationAccountName+$ClassicRunAsAccountCertifcateAssetName
               $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
               $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
               $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
               $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
               CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
         }

         # Create the Automation certificate asset
         CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

         # Populate the ConnectionFieldValues
         $SubscriptionName = $subscription.Subscription.Name
         $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

         # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
         CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

         Write-Host -ForegroundColor red $UploadMessage
         }

2. En el equipo, inicie **Windows PowerShell** desde la pantalla **Inicio** con permisos de usuario elevados.
3. Desde el shell de línea de comandos con privilegios elevados, vaya a la carpeta que contiene el script que creó en el paso 1.  
4. Ejecute el script mediante los valores de parámetro de la configuración que necesita.

    **Creación de una cuenta de ejecución mediante un certificado autofirmado**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **Creación de una cuenta de ejecución y una cuenta de ejecución clásica mediante un certificado autofirmado**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Creación de una cuenta de ejecución y una cuenta de ejecución clásica mediante un certificado de empresa**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Creación de una cuenta de ejecución y una cuenta de ejecución clásica mediante un certificado autofirmado en la nube de Azure Government**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > Después de ejecutar el script, se le pedirá que se autentique en Azure. Inicie sesión con una cuenta que sea miembro del rol Administradores de suscripciones y coadministrador de la suscripción.
    >
    >

Una vez que el script se ejecuta correctamente, observe lo siguiente:
* Si creó una cuenta de ejecución clásica con un certificado público autofirmado (formato .cer), el script lo crea y lo guarda en la carpeta de archivos temporales del equipo con el perfil de usuario *%USERPROFILE%\AppData\Local\Temp*, que se usa para ejecutar la sesión de PowerShell.
* Si creó una cuenta de identificación clásica con un certificado público de empresa (archivo .cer) , use este certificado. Siga las instrucciones para [cargar un certificado de API de administración en Azure Portal](../azure-api-management-certs.md) y luego valide la configuración de credenciales con recursos de implementación clásicos mediante el [código de ejemplo para realizar la autenticación con recursos del modelo de implementación clásica de Azure](automation-verify-runas-authentication.md#classic-run-as-authentication). 
* Si *no* creó una cuenta de ejecución clásica, realice la autenticación con recursos de Resource Manager y valide la configuración de credenciales mediante el [código de ejemplo para la autenticación con recursos de Service Management](automation-verify-runas-authentication.md#automation-run-as-authentication).

## <a name="next-steps"></a>pasos siguientes
* Para más información acerca de las entidades de servicio, consulte [Objetos Application y objetos ServicePrincipal](../active-directory/active-directory-application-objects.md).
* Para más información acerca de los certificados y de los servicios de Azure, consulte [Introducción a los certificados para Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
