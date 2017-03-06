---
title: "Configuración de Azure Key Vault para VM Linux | Microsoft Docs"
description: "Configuración de Key Vault para usarlo con una máquina virtual de Azure Resource Manager con la CLI 2.0."
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: 54c3bd47eaedee0d2269ee49143b18dc6b702e1b
ms.openlocfilehash: 16994b3dfe4945b1b023fa23aafd8541c74ae2e1
ms.lasthandoff: 02/27/2017


---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli-20"></a>Configuración de Key Vault para máquinas virtuales con la CLI de Azure 2.0

En la pila de Azure Resource Manager, los certificados o secretos se modelan como recursos que se proporcionan mediante Key Vault. Para más información sobre el Almacén de claves de Azure, consulte [¿Qué es el Almacén de claves de Azure?](../key-vault/key-vault-whatis.md) Para que Key Vault se utilice con VM de Azure Resource Manager, la propiedad *EnabledForDeployment* de Key Vault se debe establecer en true. En este artículo se muestra cómo configurar Key Vault para su uso con máquinas virtuales (VM) de Azure mediante la CLI de Azure 2.0. También puede llevar a cabo estos pasos con la [CLI de Azure 1.0](virtual-machines-linux-key-vault-setup-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para realizar estos pasos, necesita tener instalada la [CLI de Azure 2.0](/cli/azure/install-az-cli2) más reciente y haber iniciado sesión en una cuenta de Azure mediante [az login](/cli/azure/#login).

## <a name="create-a-key-vault"></a>Creación de un Almacén de claves
Cree un almacén de claves y asigne la directiva de implementación con [az keyvault create](/cli/azure/keyvault#create). En el ejemplo siguiente se crea un almacén de claves denominado `myKeyVault` en el grupo de recursos `myResourceGroup`:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Actualización de un almacén de claves para su uso con VM
Establezca la directiva de implementación en un almacén de claves existente con [az keyvault update](/cli/azure/keyvault#update). Lo siguiente actualiza el almacén de claves denominado `myKeyVault` en el grupo de recursos `myResourceGroup`:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Uso de plantillas para configurar el Almacén de claves
Al utilizar plantillas, debe configurar la propiedad `enabledForDeployment` como `true` para el recurso Key Vault tal y como se indica a continuación:

```json
{
    "type": "Microsoft.KeyVault/vaults",
    "name": "ContosoKeyVault",
    "apiVersion": "2015-06-01",
    "location": "<location-of-key-vault>",
    "properties": {
    "enabledForDeployment": "true",
    ....
    ....
    }
}
```

## <a name="next-steps"></a>Pasos siguientes
Para otras opciones que puede configurar al crear un almacén de claves mediante plantillas, consulte [Create a key vault](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)(Creación de un almacén de claves).

