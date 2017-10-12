---
title: "Creación de un clúster de Azure Service Fabric en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo implementar un clúster de Azure Service Fabric con Linux en una instancia existente de Azure Virtual Network mediante PowerShell."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2017
ms.author: ryanwi
ms.openlocfilehash: 7cee4f8d68062dcfd2b6f61d55319160a2a80a98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Implementación de un clúster de Windows de Service Fabric en una red virtual de Azure
Este tutorial es la primera parte de una serie. Obtendrá información sobre cómo implementar un clúster de Azure Service Fabric con Linux en una instancia existente de Azure Virtual Network (VNET) y en una subred mediante PowerShell. Cuando haya terminado, tendrá un clúster que se ejecuta en la nube en el que puede implementar aplicaciones.  Para crear un clúster de Linux con la CLI de Azure, consulte [Create a secure Linux cluster on Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) (Creación de un clúster de Linux seguro en Azure).

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una red virtual en Azure mediante PowerShell
> * Creación de un almacén de claves y carga de un certificado
> * Crear un clúster de Service Fabric seguro en Azure PowerShell
> * Protección del clúster con un certificado X.509
> * Conexión a un clúster con PowerShell
> * Eliminación de un clúster

En esta serie de tutoriales, se aprende a:
> [!div class="checklist"]
> * Crear un clúster seguro en Azure
> * [Implementación de API Management con Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial:
- Si no tiene ninguna suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Instale el [SDK de Service Fabric y el módulo de PowerShell](service-fabric-get-started.md).
- Instale la [versión 4.1 o superior del módulo de Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

Los siguientes procedimientos crean un clúster de Service Fabric de cinco nodos. Para calcular el costo producido por la ejecución de un clúster de Service Fabric en Azure, use la [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Inicio de sesión en Azure y selección de la suscripción
En esta guía se usa Azure PowerShell. Al iniciar una nueva sesión de PowerShell, inicie sesión en su cuenta de Azure y seleccione su suscripción antes de ejecutar comandos de Azure.
 
Ejecute el siguiente script para iniciar sesión en su cuenta de Azure y seleccione su suscripción:

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Cree un nuevo grupo de recursos para su implementación y asígnele un nombre y una ubicación.

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
New-AzureRmResourceGroup -Name $ResourceGroupName -Location centralus
```

## <a name="deploy-the-network-topology"></a>Implementación de la topología de red
A continuación, configure la topología de la red de implementación de API Management y el clúster de Service Fabric. La plantilla de Resource Manager [network.json][network-arm] está configurada para crear una red virtual (VNET) y también una subred y un grupo de seguridad de red (NSG) para Service Fabric, además de una subred y un grupo de seguridad de red para API Management. Obtenga más información sobre redes virtuales, subredes y grupos de seguridad de red [aquí](../virtual-network/virtual-networks-overview.md).

El archivo de parámetros [network.parameters.json][network-parameters-arm] contiene los nombres de las subredes y los grupos de seguridad de red donde se implementan Service Fabric y API Management.  API Management se implementa en el [siguiente tutorial](service-fabric-tutorial-deploy-api-management.md). En esta guía, los valores de los parámetros no deben cambiarse. Las plantillas de Resource Manager para Service Fabric usan estos valores.  Si los valores se modifican aquí, debe modificarlos en las otras plantillas de Resource Manager usadas en este tutorial y el [tutorial de implementación de API Management](service-fabric-tutorial-deploy-api-management.md). 

Descargue la plantilla de Resource Manager y el archivo de parámetros siguientes:
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

Use el siguiente comando de PowerShell para implementar la plantilla de Resource Manager y los archivos de parámetros para la configuración de la red:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="create-a-key-vault-and-upload-a-certificate"></a>Creación de un almacén de claves y carga de un certificado
La plantilla de Resource Manager para el clúster de Service Fabric del siguiente paso está configurada para crear un clúster con certificado seguro. El certificado se utiliza para proteger la comunicación de nodo a nodo del clúster y para administrar el acceso de los usuarios al clúster de Service Fabric. API Management también usa este certificado para tener acceso al servicio de nomenclatura de Service Fabric para detectar servicios. Este paso requiere un certificado en Key Vault para la seguridad del clúster.

El siguiente script crea un almacén de claves en Azure, crea un certificado autofirmado y carga el certificado en el almacén de claves.  Si desea usar un certificado existente, establezca **$CreateSelfSignedCertificate** en "$false" y especifique la ubicación en **$ExistingPfxFilePath**.

```powershell
$VaultResourceGroupName = 'ryanwikeyvaultgroup'
$VaultName= 'ryanwikeyvault'
$Location = "westus"
$CertificateName = "ryanwicertificate1"
$Password = 'mypa$$word!'
$DnsName = "www.mycluster.westus.mydomain.com" #The certificate's subject name must match the domain used to access the Service Fabric cluster.
$OutputPath = "C:\MyCertificates" # location where you want the .PFX to be stored
$CreateSelfSignedCertificate = $true
$ExistingPfxFilePath = 'C:\MyCertificates\ryanwicertificate1.pfx'

$ErrorActionPreference = 'Stop'

Write-Host "Switching context to SubscriptionId $SubscriptionId"
Set-AzureRmContext -SubscriptionId $SubscriptionId | Out-Null

# New-AzureRmResourceGroup is idempotent as long as the location matches
Write-Host "Ensuring ResourceGroup $VaultResourceGroupName in $Location"
New-AzureRmResourceGroup -Name $VaultResourceGroupName -Location $Location -Force | Out-Null
$resourceId = $null

try
{
    $existingKeyVault = Get-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultResourceGroupName
    $resourceId = $existingKeyVault.ResourceId

    Write-Host "Using existing vault $VaultName in $($existingKeyVault.Location)"
}
catch
{
}

if(!$existingKeyVault)
{
    Write-Host "Creating new vault $VaultName in $location"
    $newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultResourceGroupName -Location $Location -EnabledForDeployment
    $resourceId = $newKeyVault.ResourceId
}

if($CreateSelfSignedCertificate)
{
    $securePassword = ConvertTo-SecureString -String $password -AsPlainText -Force

    $NewPfxFilePath = Join-Path $OutputPath $($CertificateName+".pfx")

    Write-Host "Creating new self signed certificate at $NewPfxFilePath"
    
    ## Changes to PSPKI version 3.5.2 New-SelfSignedCertificate replaced by New-SelfSignedCertificateEx
    $PspkiVersion = (Get-Module PSPKI).Version
    if($PSPKIVersion.Major -ieq 3 -And $PspkiVersion.Minor -ieq 2 -And $PspkiVersion.Build -ieq 5) {
        New-SelfsignedCertificateEx -Subject "CN=$DnsName" -EKU "Server Authentication", "Client authentication" -KeyUsage "KeyEncipherment, DigitalSignature" -Path $NewPfxFilePath -Password $securePassword -Exportable
    }
    else {
        New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName $DnsName | Export-PfxCertificate -FilePath $NewPfxFilePath -Password $securePassword | Out-Null
    }

    $ExistingPfxFilePath = $NewPfxFilePath
}

Write-Host "Reading pfx file from $ExistingPfxFilePath"
$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2 $ExistingPfxFilePath, $Password

$bytes = [System.IO.File]::ReadAllBytes($ExistingPfxFilePath)
$base64 = [System.Convert]::ToBase64String($bytes)

$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $Password
   } | ConvertTo-Json

    $contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
    $content = [System.Convert]::ToBase64String($contentbytes)

    $secretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

Write-Host "Writing secret to $CertificateName in vault $VaultName"
$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $CertificateName -SecretValue $secretValue

$output = @{};
$output.SourceVault = $resourceId;
$output.CertificateURL = $secret.Id;
$output.CertificateThumbprint = $cert.Thumbprint;

Write-Host "Source vault: " $output.SourceVault
Write-Host "Certificate URL: " $output.CertificateURL
Write-Host "Certificate Thumbprint: " $output.CertificateThumbprint
```

## <a name="deploy-the-service-fabric-cluster"></a>Implementación del clúster de Service Fabric
Una vez implementados los recursos de red, el siguiente paso es implementar un clúster de Service Fabric a la red virtual, en la subred y el grupo de seguridad de red designados para el clúster de Service Fabric. La implementación de un clúster en una red virtual y subred (implementada anteriormente en este artículo) existentes requiere una plantilla de Resource Manager.  Para más información, vea [Creación de un clúster con Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Para esta serie de tutoriales, la plantilla está preconfigurada para usar los nombres de la red virtual, la subred y el grupo de seguridad de red que configuró en un paso anterior.  Descargue la plantilla de Resource Manager y el archivo de parámetros siguientes:
- [cluster.json][cluster-arm]
- [cluster.parameters.json][cluster-parameters-arm]

Complete los parámetros **clusterName**, **adminUserName**, **adminPassword**, **certificateThumbprint**, **certificateUrlValue** y **sourceVaultValue** vacíos en el archivo *cluster.parameters.json* para la implementación.  Si tiene un certificado existente cargado previamente en un almacén de claves, complete los valores **certificateThumbprint**, **certificateUrlValue** y **sourceVaultValue** para ese certificado.

Use el siguiente comando de PowerShell para implementar la plantilla de Resource Manager y los archivos de parámetros para crear el clúster de Service Fabric:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\cluster.json -TemplateParameterFile .\cluster.parameters.json -Verbose
```

## <a name="connect-to-the-secure-cluster"></a>Conexión al clúster seguro
Conéctese al clúster mediante el módulo PowerShell de Service Fabric instalado con el SDK de Service Fabric.  En primer lugar, instale el certificado en el almacén personal (Mi) del usuario actual en el equipo.  Ejecute el siguiente comando de PowerShell:

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Ahora está listo para conectarse a su clúster seguro.

El módulo PowerShell de **Service Fabric** proporciona muchos cmdlets para administrar clústeres, aplicaciones y servicios de Service Fabric.  Use el cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) para conectarse al clúster seguro. Los detalles de la huella digital de certificado y del punto de conexión se encuentran en la salida de un paso anterior.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Compruebe que está conectado y que el estado del clúster es correcto mediante el cmdlet [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Limpieza de recursos
En el resto de artículos de esta serie de tutoriales se usa el clúster que se acaba de crear. Si no va a pasar inmediatamente al siguiente artículo, puede eliminar el clúster para evitar incurrir en cargos. La manera más sencilla de eliminar el clúster y todos los recursos que consume es eliminar el grupo de recursos.

Inicie sesión en Azure y seleccione el identificador de suscripción con el que quiere quitar el clúster.  Para encontrar el identificador de suscripción, inicie sesión en [Azure Portal](http://portal.azure.com). Elimine el grupo de recursos y todos los recursos de clúster mediante el cmdlet [Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId "Subcription ID"

$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Crear una red virtual en Azure mediante PowerShell
> * Creación de un almacén de claves y carga de un certificado
> * Creación de un clúster de Service Fabric en Azure mediante PowerShell
> * Protección del clúster con un certificado X.509
> * Conexión a un clúster con PowerShell
> * Eliminación de un clúster

A continuación, avance hasta el siguiente tutorial para aprender a implementar API Management con Service Fabric.
> [!div class="nextstepaction"]
> [Implementación de API Management](service-fabric-tutorial-deploy-api-management.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json
