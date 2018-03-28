---
title: Cifrado de discos de Azure Virtual Machine Scale Sets | Microsoft Docs
description: Obtenga información sobre cómo usar Azure PowerShell para cifrar las instancias de máquina virtual y los discos conectados en Virtual Machine Scale Sets
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: iainfou
ms.openlocfilehash: 856d4bc7dd636b3a2f3d072a10989cafd7efd6a6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>Cifrado de discos de datos conectados y de SO en un conjunto de escalado de máquinas virtuales
Para proteger los datos en reposo mediante la tecnología de cifrado estándar del sector, Virtual Machine Scale Sets admite Azure Disk Encryption (ADE). El cifrado se puede habilitar para Virtual Machine Scale Sets de Windows y Linux. Para más información, consulte [Azure Disk Encryption para Windows y Linux](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  Azure Disk Encryption para Virtual Machine Scale Sets actualmente está en versión preliminar, disponible en todas las regiones públicas de Azure. 
>
> En la versión preliminar actual no se admiten operaciones de restablecimiento de la imagen inicial de máquinas virtuales del conjunto de escalado ni operaciones de actualización. En la versión preliminar, el cifrado de los conjuntos de escalado solo se recomienda en los entornos de prueba. En la versión preliminar, no habilite el cifrado de discos en entornos de producción en los que pudiera necesitar actualizar una imagen del sistema operativo.

El cifrado de discos de Azure es compatible con:
- Conjuntos de escalado creados con discos administrados, y no es compatible con conjuntos de escalado de discos nativos (o no administrados).
- Volúmenes de datos y del sistema operativo en conjuntos de escalado de Windows. También se admite la deshabilitación del cifrado de volúmenes de datos y del sistema operativo en conjuntos de escalado de Windows.
- Volúmenes de datos en conjuntos de escalado de Linux. El cifrado de discos del sistema operativo no se admite en la versión preliminar actual para los conjuntos de escalado de Linux.


## <a name="prerequisites"></a>requisitos previos
En este artículo se requiere la versión 5.3.0 del módulo de Azure PowerShell, u otra posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps).

Registre la suscripción de Azure para obtener la versión preliminar del cifrado de discos para Virtual Machine Scale Sets con [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature): 

```powershell
Login-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Espere unos 10 minutos hasta que [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature) devuelva el estado *Registrado* y luego vuelva a registrar el proveedor `Microsoft.Compute`: 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```


## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Creación de una instancia de Azure Key Vault habilitada para el cifrado de discos
Azure Key Vault puede almacenar claves, secretos o contraseñas que permiten su implementación segura en las aplicaciones y los servicios. Las claves criptográficas se almacenan en Azure Key Vault con protección de software, o puede importar o generar las claves en módulos de seguridad de hardware (HSM) certificados conforme a las normas FIPS 140-2 de nivel 2. Las claves criptográficas se usan para cifrar y descifrar los discos virtuales conectados a la máquina virtual. Estas claves criptográficas se pueden controlar y se puede auditar su uso.

Cree una instancia de Key Vault con [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). Para permitir que la instancia de Key Vault se use para el cifrado de discos, establezca el parámetro *EnabledForDiskEncryption*. En el ejemplo siguiente también se definen las variables para el nombre del grupo de recursos, el nombre de la instancia de Key Vault y la ubicación. Proporcione su propio nombre de Key Vault:

```powershell
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzureRmResourceGroup -Name $rgName -Location $location
New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```


### <a name="use-an-existing-key-vault"></a>Uso de una instancia existente de Key Vault
Este paso solo es necesario si tiene una instancia de Key Vault que desea usar con el cifrado de discos. Omita este paso si creó una instancia de Key Vault en la sección anterior.

Puede habilitar una instancia de Key Vault existente en la misma suscripción y región que el conjunto de escalado para el cifrado de discos con [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/AzureRM.KeyVault/Set-AzureRmKeyVaultAccessPolicy). Defina el nombre de la instancia existente de Key Vault en la variable *$vaultName* como se indica a continuación:

```powershell
$vaultName="myexistingkeyvault"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```


## <a name="create-a-scale-set"></a>Creación de un conjunto de escalado
En primer lugar, establezca un nombre de usuario de administrador y una contraseña para las instancias de máquina virtual con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Ahora, cree un conjunto de escalado de máquinas virtuales con [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Para distribuir el tráfico a las instancias individuales de VM, también se crea un equilibrador de carga. El equilibrador de carga incluye reglas para distribuir el tráfico en el puerto TCP 80, así como también permite el tráfico de Escritorio remoto en el puerto TCP 3389 y la conexión remota de PowerShell en el puerto TCP 5985:

```powershell
$vmssName="myScaleSet"

New-AzureRmVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```


## <a name="enable-encryption"></a>Habilitación del cifrado
Para cifrar las instancias de máquina virtual de un conjunto de escalado, primero obtenga información sobre el identificador de recurso y el URI de Key Vault con [Get-AzureRmKeyVault](/powershell/module/AzureRM.KeyVault/Get-AzureRmKeyVault). Estas variables se usan luego para iniciar el proceso de cifrado con [Set-AzureRmVmssDiskEncryptionExtension](/powershell/module/AzureRM.Compute/Set-AzureRmVmssDiskEncryptionExtension):

```powershell
$diskEncryptionKeyVaultUrl=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

Cuando se le solicite, escriba *y* para continuar el proceso de cifrado de disco en las instancias de VM del conjunto de escalado.


## <a name="check-encryption-progress"></a>Comprobación del progreso del cifrado
Para comprobar el estado del cifrado de disco, use [Get-AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Get-AzureRmVmssDiskEncryption):

```powershell
Get-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Cuando las instancias de VM están cifradas, el código *EncryptionSummary* informa *ProvisioningState/succeeded*, como se muestra en la salida de ejemplo a continuación:

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```


## <a name="disable-encryption"></a>Deshabilitación del cifrado
Si ya no desea usar los discos de instancias de VM cifrados, puede deshabilitar el cifrado con [Disable-AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Disable-AzureRmVmssDiskEncryption), como se indica a continuación:

```powershell
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```


## <a name="next-steps"></a>Pasos siguientes
En este artículo, usó Azure PowerShell para cifrar un conjunto de escalado de máquinas virtuales. También puede usar la [CLI de Azure 2.0](virtual-machine-scale-sets-encrypt-disks-cli.md) o plantillas para [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) o [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
