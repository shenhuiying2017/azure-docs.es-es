---
title: Crear un clúster de Azure Service Fabric mediante un nombre común del certificado | Microsoft Docs
description: Obtenga información acerca de cómo crear un clúster de Service Fabric mediante un nombre común del certificado de una plantilla.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi
ms.openlocfilehash: 8725dd1931b120b0369d0810fa49108a00c71e8e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211072"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Implementar un clúster de Service Fabric que utiliza un nombre común del certificado en lugar de una huella digital
No hay dos certificados que puedan tener la misma huella digital, lo que dificulta la sustitución o administración del certificado de clúster. Sin embargo, varios certificados pueden tener el mismo nombre o asunto común.  Si un clúster usa nombres comunes del certificado, se simplificará considerablemente la administración de certificados. En este artículo se describe cómo implementar un clúster de Service Fabric para que use un nombre común del certificado en lugar de la huella digital del certificado.
 
## <a name="get-a-certificate"></a>Obtener un certificado
Primero, obtenga un certificado de una [entidad de certificación (CA)](https://wikipedia.org/wiki/Certificate_authority).  El nombre común del certificado debe ser el nombre de host del clúster.  Por ejemplo, "myclustername.southcentralus.cloudapp.azure.com".  

Si quiere realizar pruebas, puede obtener un certificado que haya firmado una CA de una entidad de certificación gratuita o pública.

> [!NOTE]
> Recuerde que no se admiten certificados autofirmados (incluyendo aquellos que se crean al implementar un clúster de Service Fabric en Azure Portal).

## <a name="upload-the-certificate-to-a-key-vault"></a>Cargar el certificado en un almacén de claves
En Azure, se implementa un clúster de Service Fabric en un conjunto de escalado de máquinas virtuales.  Cargue el certificado en un almacén de claves.  Cuando se implementa el clúster, el certificado se instala en el conjunto de escalado de máquinas virtuales que está ejecutando el clúster.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

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
```

## <a name="download-and-update-a-sample-template"></a>Descargar y actualizar una plantilla de ejemplo
En este artículo se usa la plantilla de ejemplo del [clúster seguro de 5 nodos](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) y los parámetros de plantilla. Descargue los archivos *azuredeploy.json* y *azuredeploy.parameters.json* en el ordenador.

### <a name="update-parameters-file"></a>Actualizar el archivo de parámetros
En primer lugar, abra el archivo *azuredeploy.parameters.json* en el editor de texto y agregue el siguiente valor de parámetro:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

A continuación, establezca los valores de los parámetros *certificateCommonName*, *sourceVaultValue* y *certificateUrlValue* en aquellos valores que haya devuelto el script anterior:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Actualice el archivo de plantilla
A continuación, abra el archivo *azuredeploy.json* en el editor de texto y realice tres actualizaciones para admitir el nombre común del certificado.

1. En la sección **Parámetros**, agregue un parámetro *certificateCommonName* parámetro:
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    ```

    Asimismo, quite el valor *certificateThumbprint*, puesto que ya no será necesario.

2. Establezca el valor de la variable *sfrpApiVersion* en "2018-02-01":
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. En el recurso **Microsoft.Compute/virtualMachineScaleSets**, actualice la extensión de máquina virtual para usar el nombre común en la configuración del certificado en lugar de la huella digital.  En **virtualMachineProfile**->**extenstionProfile**->**extensiones**->**propiedades**->**configuración**->**certificado**, agregue `"commonNames": ["[parameters('certificateCommonName')]"],` y quite `"thumbprint": "[parameters('certificateThumbprint')]",`.
    ```json
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              "type": "ServiceFabricNode",
              "autoUpgradeMinorVersion": true,
              "protectedSettings": {
                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
              },
              "publisher": "Microsoft.Azure.ServiceFabric",
              "settings": {
                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                "nodeTypeRef": "[variables('vmNodeType0Name')]",
                "dataPath": "D:\\SvcFab",
                "durabilityLevel": "Bronze",
                "enableParallelJobs": true,
                "nicPrefixOverride": "[variables('subnet0Prefix')]",
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('certificateStoreValue')]"
                }
              },
              "typeHandlerVersion": "1.0"
            }
          },
    ```

4.  En el recurso **Microsoft.ServiceFabric/clusters**, actualice la versión de API a "2018-02-01".  A continuación, agregue un valor de configuración **certificateCommonNames** con la propiedad **commonNames** y quite la configuración del **certificado** (con la propiedad de la huella digital), tal como se indica en el siguiente ejemplo:
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
        "addonFeatures": [
            "DnsService",
            "RepairManager"
        ],        
        "certificateCommonNames": {
            "commonNames": [
            {
                "certificateCommonName": "[parameters('certificateCommonName')]",
                "certificateIssuerThumbprint": ""
            }
            ],
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
        ...
    ```

## <a name="deploy-the-updated-template"></a>Implementar la plantilla actualizada
Vuelva a implementar la plantilla actualizada después de realizar los cambios.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información sobre la [seguridad del clúster](service-fabric-cluster-security.md).
* Obtenga información acerca de cómo [sustituir un certificado de clúster](service-fabric-cluster-rollover-cert-cn.md)
* [Actualizar y administrar certificados del clúster](service-fabric-cluster-security-update-certs-azure.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png