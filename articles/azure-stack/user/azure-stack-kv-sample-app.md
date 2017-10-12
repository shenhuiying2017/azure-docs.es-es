---
title: "Permitir a aplicaciones recuperar secretos del almacén de claves de Azure Stack | Microsoft Docs"
description: "Usar una aplicación de ejemplo para trabajar con el almacén de claves de Azure Stack"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2017
ms.author: sngun
ms.openlocfilehash: 7cfb78cc5219d4adab5ceddc9d7eb8d1fc71b678
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Aplicación de ejemplo que usa claves y secretos almacenados en un almacén de claves

En este artículo, le mostraremos cómo ejecutar una aplicación de ejemplo (HelloKeyVault) que recupera las claves y secretos de un almacén de claves en Azure Stack.

## <a name="prerequisites"></a>Requisitos previos 

Implemente los siguientes requisitos previos desde el [kit de desarrollo](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) o desde un cliente externo basado en Windows, si está [conectado a través de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Instale los [módulos de Azure PowerShell compatibles con Azure Stack](azure-stack-powershell-install.md).  
* Descargue las [herramientas necesarias para trabajar con Azure Stack](azure-stack-powershell-download.md). 

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Creación y obtención del almacén de claves y la configuración de la aplicación

En primer lugar, debe crear un almacén de claves en Azure Stack y registrar una aplicación en Active Directory (Azure AD). Puede crear y registrar los almacenes de claves mediante Azure Portal o PowerShell. En este artículo se muestra la forma de hacer las tareas de PowerShell. De forma predeterminada, este script de PowerShell crea una aplicación nueva en Active Directory. Sin embargo, también puede usar una de las aplicaciones existentes. Asegúrese de proporcionar un valor para las variables `aadTenantName` y `applicationPassword`. Si no especifica ningún valor para la variable `applicationPassword` este script generará una contraseña aleatoria. 

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local' 

# Password for the application. If not specified, this script will generate a random password during app creation.
$applicationPassword = '' 
                         
# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "PLEASE FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack operator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience "https://graph.windows.net/"

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Login-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `
  
$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey
    
# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "http://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault within that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location   

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host

``` 

La captura de pantalla siguiente muestra la salida del script anterior:

![Configuración de la aplicación](media/azure-stack-kv-sample-app/settingsoutput.png)

Tome nota de los valores de **VaultUrl**, **AuthClientId** y **AuthClientSecret** que devolvió el script anterior. Para ejecutar la aplicación HelloKeyVault, utilizará estos valores.

## <a name="download-and-run-the-sample-application"></a>Descarga y ejecución de la aplicación de ejemplo

Descargue el ejemplo de almacén de claves de la página de Azure [Key vault client samples](https://www.microsoft.com/en-us/download/details.aspx?id=45343) (ejemplos de cliente del almacén de claves). Extraiga el contenido del archivo .zip en la estación de trabajo de desarrollo. Hay dos ejemplos dentro de la carpeta de ejemplos. En este tema, usamos el ejemplo HellpKeyVault. Navegue hasta los ejemplos **Microsoft.Azure.KeyVault.Samples** > **, carpeta**  > **HelloKeyVault** y abra la aplicación HelloKeyVault en Visual Studio. 

Abra el archivo HelloKeyVault\App.config y reemplace los valores del elemento <appSettings> con los valores de **VaultUrl**, **AuthClientId** y **AuthClientSecret**devueltos por el script anterior. Tenga en cuenta que, de forma predeterminada, el archivo App.config contiene el marcador de posición para *AuthCertThumbprint*, pero usará *AuthClientSecret* en su lugar. Después de reemplazar la configuración, vuelva a compilar la solución e inicie la aplicación.

![Configuración de la aplicación](media/azure-stack-kv-sample-app/appconfig.png)
 
La aplicación inicia una sesión en Azure AD y, a continuación, utiliza ese token para autenticarse en Azure Stack. La aplicación realiza las operaciones, como crear, cifrar, ajustar y eliminar, en las claves y secretos del almacén de claves. También puede pasar parámetros específicos, como *encrypt* y *decrypt* a la aplicación, lo que asegura que la aplicación ejecuta solo esas operaciones en el almacén. 


## <a name="next-steps"></a>Pasos siguientes
[Implementación de una máquina virtual con una contraseña de Key Vault](azure-stack-kv-deploy-vm-with-secret.md)

[Implementación de una máquina virtual con un certificado de Key Vault](azure-stack-kv-push-secret-into-vm.md)



