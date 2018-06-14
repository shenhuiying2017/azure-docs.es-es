---
title: Sustitución del certificado en un clúster de Azure Service Fabric | Microsoft Docs
description: Obtenga información sobre cómo sustituir el certificado en un clúster de Service Fabric identificado por el nombre común del certificado.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi
ms.openlocfilehash: df919e23fd608cdf41e93844f13342ca00657adb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205151"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Sustitución manual del certificado en un clúster de Service Fabric
Cuando el certificado de un clúster de Service Fabric esté a punto de expirar, deberá actualizarlo.  La sustitución del certificado es sencilla si el clúster está [configurado para usar certificados en función del nombre común](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (en lugar de la huella digital).  Obtenga un nuevo certificado de una entidad de certificación con una nueva fecha de expiración.  Recuerde que no se admiten certificados autofirmados (incluyendo aquellos que se crean al implementar un clúster de Service Fabric en Azure Portal).  El nuevo certificado debe tener el mismo nombre común que el certificado anterior. 

El siguiente script carga un nuevo certificado en un almacén de claves y, a continuación, instala el certificado en el conjunto de escalado de máquinas virtuales.  El clúster de Service Fabric utilizará automáticamente el certificado con la última fecha de expiración.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzureRmVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzureRmVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

Para más información, lea lo siguiente:
* Obtenga más información sobre la [seguridad del clúster](service-fabric-cluster-security.md).
* [Actualizar y administrar certificados del clúster](service-fabric-cluster-security-update-certs-azure.md)

