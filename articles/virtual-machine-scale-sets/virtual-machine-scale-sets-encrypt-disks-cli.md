---
title: Cifrado de discos en Azure Virtual Machine Scale Sets | Microsoft Docs
description: "Aprenda a cifrar los discos conectados a conjuntos de escalado de máquinas virtuales."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: iainfou
ms.openlocfilehash: 3263ed4d6325207f38656d741fa4a1f091d4fdea
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>Cifrado de discos de datos conectados y de SO en un conjunto de escalado de máquinas virtuales
Azure [Virtual Machine Scale Sets](/azure/virtual-machine-scale-sets/) admite el cifrado de discos de Azure (ADE).  Se puede habilitar el cifrado de discos de Azure para conjuntos de escalado de máquinas virtuales Windows y Linux para proteger los datos en reposo de los conjuntos de escalado mediante la tecnología de cifrado estándar del sector. Para más información, lea la documentación sobre el cifrado de discos de Azure para máquinas virtuales Windows y Linux.

> [!NOTE]
>  El cifrado de discos para conjuntos de escalado de máquinas virtuales se encuentra actualmente en versión preliminar pública, disponible en todas las regiones públicas de Azure.

El cifrado de discos de Azure es compatible con:
- conjuntos de escalado creados con discos administrados, y no es compatible con conjuntos de escalado de discos nativos (o no administrados).
- volúmenes de datos y del sistema operativo en conjuntos de escalado de Windows. También se admite la deshabilitación del cifrado de volúmenes de datos y del sistema operativo en conjuntos de escalado de Windows.
- volúmenes de datos en conjuntos de escalado de Linux. El cifrado de discos del sistema operativo no se admite en la versión preliminar actual para los conjuntos de escalado de Linux.

En la versión preliminar actual no se admiten operaciones de restablecimiento de la imagen inicial de máquinas virtuales del conjunto de escalado ni operaciones de actualización. La versión preliminar del cifrado de discos de Azure para conjuntos de escalado de máquinas virtuales se recomienda solo para entornos de prueba. En la versión preliminar, no habilite el cifrado de discos en entornos de producción en los pudiera necesitar actualizar una imagen del sistema operativo de un conjunto de escalado cifrado.

[Aquí](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat) se puede encontrar un ejemplo de archivo por lotes de un extremo a otro para el cifrado de discos de datos de un conjunto de escalado de Linux.  En este ejemplo se crea un grupo de recursos y un conjunto de escalado de Linux, se monta un disco de datos de 5 GB y se cifra el conjunto de escalado de máquinas virtuales.

## <a name="prerequisites"></a>requisitos previos
Instale las últimas versiones de [Azure Powershell](https://github.com/Azure/azure-powershell/releases) o de la [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), que contienen los comandos de cifrado.

El cifrado de discos de Azure para conjuntos de escalado de máquinas virtuales le permite autorregistrar su suscripción mediante los siguientes comandos de PowerShell: 

```powershell
Login-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Espere unos 10 minutos hasta que el siguiente comando devuelva el estado "Registrado": 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Creación de un almacén de claves de Azure habilitado para el cifrado de discos
Cree un nuevo almacén de claves en la misma suscripción y región que el conjunto de escalado y establezca la directiva de acceso "EnabledForDiskEncryption".

```azurecli
rgname="linuxdatadiskencryptiontest"
VaultName="encryptionvault321"

az keyvault create --name $VaultName --resource-group $rgname --enabled-for-disk-encryption
```

O bien, habilite un almacén de claves existente en la misma suscripción y región que el conjunto de escalado para el cifrado de disco.

```azurecli
VaultName="encryptionvault321"
az keyvault update --name $VaultName --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Habilitar cifrado

Los siguientes comandos permiten cifrar un disco de datos en un conjunto de escalado en ejecución mediante un almacén de claves del mismo grupo de recursos. También puede usar plantillas para cifrar discos en un [conjunto de escalado de Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) o un [conjunto de escalado de Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox) en ejecución.

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"
EncryptionKeyVaultUrl="https://encryptionvaultlinuxsf.vault.azure.net"
VaultResourceId="/subscriptions/0754ecc2-d80d-426a-902c-b83f4cfbdc95/resourceGroups/linuxdatadiskencryptiontest/providers/Microsoft.KeyVault/vaults/encryptionvaultlinuxsf"

az vmss encryption enable -g $ResourceGroup -n $VmssName --disk-encryption-keyvault $VaultResourceId --volume-type DATA
az vmss update-instances -g $ResourceGroup -n $VmssName --instance-ids *
```

## <a name="check-encryption-progress"></a>Comprobación del progreso del cifrado

Use los siguientes comandos para mostrar el estado de cifrado del conjunto de escalado.

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"

az vmss encryption show -g $ResourceGroup -n $VmssName
```

## <a name="disable-encryption"></a>Deshabilitar cifrado
Deshabilite el cifrado de un conjunto de escalado de máquinas virtuales en ejecución mediante los siguientes comandos. También puede usar plantillas para deshabilitar el cifrado de un [conjunto de escalado de Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows) o un [conjunto de escalado de Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux) en ejecución.

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"

az vmss encryption disable -g $ResourceGroup -n $VmssName
```

