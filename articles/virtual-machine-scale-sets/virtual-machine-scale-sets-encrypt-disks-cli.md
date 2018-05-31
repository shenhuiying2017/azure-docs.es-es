---
title: Cifrado de discos para conjuntos de escalado de Azure con la CLI de Azure | Microsoft Docs
description: Obtenga información sobre cómo usar la CLI de Azure 2.0 para cifrar las instancias de máquina virtual y los discos conectados en un conjunto de escalado de máquinas virtuales Linux
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
ms.date: 04/30/2018
ms.author: iainfou
ms.openlocfilehash: 22d3c763317def137b4e0beb155f28585d7c6ae1
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
ms.locfileid: "32776421"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli-20-preview"></a>Cifrado de discos de datos conectados y de SO en un conjunto de escalado de máquinas virtuales con la CLI de Azure 2.0 (versión preliminar)

Para proteger los datos en reposo mediante la tecnología de cifrado estándar del sector, Virtual Machine Scale Sets admite Azure Disk Encryption (ADE). El cifrado se puede habilitar para conjuntos de escalado de máquinas virtuales Windows y Linux. Para obtener más información, vea [Azure Disk Encryption para máquinas virtuales IaaS Linux y Windows](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  El cifrado de discos para conjuntos de escalado de máquinas virtuales se encuentra actualmente en versión preliminar pública, disponible en todas las regiones públicas de Azure.

El cifrado de discos de Azure es compatible con:
- Conjuntos de escalado creados con discos administrados, y no es compatible con conjuntos de escalado de discos nativos (o no administrados).
- Volúmenes de datos y del sistema operativo en conjuntos de escalado de Windows. También se admite la deshabilitación del cifrado de volúmenes de datos y del sistema operativo en conjuntos de escalado de Windows.
- Volúmenes de datos en conjuntos de escalado de Linux. El cifrado de discos del sistema operativo no se admite en la versión preliminar actual para los conjuntos de escalado de Linux.

En la versión preliminar actual no se admiten operaciones de restablecimiento de la imagen inicial de máquinas virtuales del conjunto de escalado ni operaciones de actualización. La versión preliminar del cifrado de discos de Azure para conjuntos de escalado de máquinas virtuales se recomienda solo para entornos de prueba. En la versión preliminar, no habilite el cifrado de discos en entornos de producción en los pudiera necesitar actualizar una imagen del sistema operativo de un conjunto de escalado cifrado.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tutorial es preciso ejecutar la CLI de Azure de la versión 2.0.31 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="register-for-disk-encryption-preview"></a>Registro para la versión preliminar de cifrado de discos

El cifrado de discos de Azure para conjuntos de escalado de máquinas virtuales (versión preliminar) exige el autorregistro de la suscripción con [az feature register](/cli/azure/feature#az_feature_register). Solo tiene que realizar los siguientes pasos la primera vez que use la característica de cifrado de discos (versión preliminar):

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

La solicitud de registro puede tardar hasta diez minutos en propagarse. Puede comprobar el estado de registro con [az feature show](/cli/azure/feature#az_feature_show). Cuando `State` indique *Registrado*, vuelva a registrar el proveedor *Microsoft.Compute* con [az provider register](/cli/azure/provider#az_provider_register):

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="create-a-scale-set"></a>Creación de un conjunto de escalado

Antes de poder crear un conjunto de escalado, cree un grupo de recursos con [az group create](/cli/azure/group#az_group_create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Ahora, cree un conjunto de escalado de máquinas virtuales con [az vmss create](/cli/azure/vmss#az_vmss_create). En el ejemplo siguiente se crea un conjunto de escalado llamado *myScaleSet* que se establece para actualizarse automáticamente a medida que se aplican los cambios, y se generan claves SSH si no existen en *~/.ssh/id_rsa*. Se conecta un disco de datos de 32 Gb a cada instancia de máquina virtual y se usa la [extensión de script personalizado](../virtual-machines/linux/extensions-customscript.md) de Azure para preparar los discos de datos con [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set):

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Se tardan unos minutos en crear y configurar todos los recursos de conjunto de escalado y máquinas virtuales.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Creación de un almacén de claves de Azure habilitado para el cifrado de discos

Azure Key Vault puede almacenar claves, secretos o contraseñas que permiten su implementación segura en las aplicaciones y los servicios. Las claves criptográficas se almacenan en Azure Key Vault con protección de software, o puede importar o generar las claves en módulos de seguridad de hardware (HSM) certificados conforme a las normas FIPS 140-2 de nivel 2. Las claves criptográficas se usan para cifrar y descifrar los discos virtuales conectados a la máquina virtual. Estas claves criptográficas se pueden controlar y se puede auditar su uso.

Defina su propio elemento *keyvault_name* exclusivo. Luego cree una nueva instancia de Key Vault con [az keyvault create](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) en la misma suscripción y región que el conjunto de escalado y establezca la directiva de acceso *--enabled-for-disk-encryption*.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Uso de una instancia existente de Key Vault

Este paso solo es necesario si tiene una instancia de Key Vault que desea usar con el cifrado de discos. Omita este paso si creó una instancia de Key Vault en la sección anterior.

Defina su propio elemento *keyvault_name* exclusivo. Luego actualice la instancia de Key Vault con [az keyvault update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) y establezca la directiva de acceso *--enabled-for-disk-encryption*.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Habilitación del cifrado

Para cifrar instancias de máquina virtual de un conjunto de escalado, primero obtenga información sobre el identificador de recurso de Key Vault con [az keyvault show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Estas variables se usan luego para iniciar el proceso de cifrado con [az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable):

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

El proceso de cifrado puede tardar uno o dos minutos en iniciarse.

Como la directiva de actualización del conjunto de escalado creado en un paso anterior se establece en *automática*, las instancias de máquina virtual inician automáticamente el proceso de cifrado. En conjuntos de escalado donde la directiva de actualización es manual, inicie la directiva de cifrado en las instancias de máquina virtual con [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances).

## <a name="check-encryption-progress"></a>Comprobación del progreso del cifrado

Para comprobar el estado del cifrado de disco, use [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Cuando las instancias de máquina virtual están cifradas, el código de estado indica *EncryptionState/encrypted*, como se muestra en la salida de ejemplo siguiente:

```bash
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Deshabilitación del cifrado

Si ya no quiere usar los discos de instancias de máquina virtual cifrados, puede deshabilitar el cifrado con [az vmss encryption disable](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable), como se indica a continuación:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo, se ha usado la CLI de Azure 2.0 para cifrar un conjunto de escalado de máquinas virtuales. También puede usar [Azure PowerShell](virtual-machine-scale-sets-encrypt-disks-ps.md) o plantillas de [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) o [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).

[Aquí](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat) se puede encontrar un ejemplo de archivo por lotes de un extremo a otro para el cifrado de discos de datos de un conjunto de escalado de Linux. En este ejemplo se crea un grupo de recursos y un conjunto de escalado de Linux, se monta un disco de datos de 5 GB y se cifra el conjunto de escalado de máquinas virtuales.