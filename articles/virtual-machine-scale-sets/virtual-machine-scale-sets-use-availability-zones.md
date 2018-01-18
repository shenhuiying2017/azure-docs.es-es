---
title: "Creación de un conjunto de escalado de Azure que usa Zonas de disponibilidad (versión preliminar) | Microsoft Docs"
description: "Obtenga información sobre cómo crear conjuntos de escalado de máquinas virtuales de Azure que usan Zonas de disponibilidad para aumentar la redundancia contra las interrupciones"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: iainfou
ms.openlocfilehash: 310fdc68d3eb662906053d2bc0c45e6cfa18d4da
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>Creación de un conjunto de escalado de máquinas virtuales que usa Zonas de disponibilidad (versión preliminar)
Para proteger los conjuntos de escalado de máquinas virtuales de los errores en el nivel del centro de datos, puede crear un conjunto de escalado en una zona de disponibilidad. Las regiones de Azure que admiten las Zonas de disponibilidad tienen un mínimo de tres zonas independientes, cada una de las cuales con su propia fuente de alimentación, red y sistema de refrigeración. Para obtener más información, consulte [Introducción a las zonas de disponibilidad](../availability-zones/az-overview.md).

Para usar las Zonas de disponibilidad, el conjunto de escalado se debe crear en una [región de Azure compatible](../availability-zones/az-overview.md#regions-that-support-availability-zones). Puede crear un conjunto de escalado que use las Zonas de disponibilidad con uno de los métodos siguientes:

- [portal de Azure](#use-the-azure-portal)
- [CLI de Azure 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Plantillas del Administrador de recursos de Azure](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>Uso de Azure Portal
El proceso para crear un conjunto de escalado que usa una zona de disponibilidad es igual al que se detalla en el [artículo de introducción](virtual-machine-scale-sets-create-portal.md). Cuando selecciona una región de Azure compatible, puede crear un conjunto de escalado en una de las zonas disponibles, tal como se muestra en el ejemplo siguiente:

![Creación de un conjunto de escalado en una sola zona de disponibilidad](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

El conjunto de escalado y los recursos de apoyo, como la dirección IP pública y Azure Load Balancer, se crean en la zona única que se especifica.


## <a name="use-the-azure-cli-20"></a>Uso de la CLI de Azure 2.0
El proceso para crear un conjunto de escalado que usa una zona de disponibilidad es igual al que se detalla en el [artículo de introducción](virtual-machine-scale-sets-create-cli.md). Para usar las Zonas de disponibilidad, debe crear el conjunto de escalado en una región de Azure compatible. Agregue el parámetro `--zones` al comando [az vmss create](/cli/azure/vmss#az_vmss_create) y especifique qué zona se usará (como la zona *1*, *2* o *3*). En el ejemplo siguiente se crea un conjunto de escalado denominado *myScaleSet* en la zona *1*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

Se tardan unos minutos en crear y configurar todos los recursos de conjunto de escalado y máquinas virtuales en la zona que especifica.


## <a name="use-azure-powershell"></a>Uso de Azure PowerShell
El proceso para crear un conjunto de escalado que usa una zona de disponibilidad es igual al que se detalla en el [artículo de introducción](virtual-machine-scale-sets-create-powershell.md). Para usar las Zonas de disponibilidad, debe crear el conjunto de escalado en una región de Azure compatible. Agregue el parámetro `-Zone` al comando [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) y especifique qué zona se usará (como la zona *1*, *2* o *3*). En el ejemplo siguiente se crea una configuración de conjunto de escalado denominada *vmssConfig* en *Este de EE. UU. 2* zona *1*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

Para crear el conjunto de escalado real, siga los pasos adicionales que se detallan en el [artículo de introducción](virtual-machine-scale-sets-create-powershell.md).


## <a name="use-azure-resource-manager-templates"></a>Utilización de plantillas del Administrador de recursos de Azure
El proceso para crear un conjunto de escalado que usa una zona de disponibilidad es igual al que se detalla en el artículo de introducción para [Linux](virtual-machine-scale-sets-create-template-linux.md) o [Windows](virtual-machine-scale-sets-create-template-windows.md). Para usar las Zonas de disponibilidad, debe crear el conjunto de escalado en una región de Azure compatible. Agregue la propiedad `zones` al tipo de recurso *Microsoft.Compute/virtualMachineScaleSets* en la plantilla y especifique qué zona se va a usar (como la zona *1*, *2* o *3*). En el ejemplo siguiente se crea un conjunto de escalado Linux denominado *myScaleSet* en *Este de EE. UU. 2* zona *1*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

Para crear el conjunto de escalado real, siga los pasos adicionales que se detallan en el artículo de introducción para [Linux](virtual-machine-scale-sets-create-template-linux.md) o [Windows](virtual-machine-scale-sets-create-template-windows.md)


## <a name="next-steps"></a>pasos siguientes
Ahora que creó un conjunto de escalado en una zona de disponibilidad, puede obtener información sobre cómo [implementar aplicaciones en conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-deploy-app.md) o [usar el escalado automático con conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-autoscale-overview.md).
