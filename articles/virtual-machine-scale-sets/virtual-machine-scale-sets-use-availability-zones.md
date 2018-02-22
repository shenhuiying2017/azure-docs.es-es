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
ms.date: 01/11/2018
ms.author: iainfou
ms.openlocfilehash: 2de214f604469025a8a4accde44359fea0ded7e9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>Creación de un conjunto de escalado de máquinas virtuales que usa Zonas de disponibilidad (versión preliminar)
Para proteger los conjuntos de escalado de máquinas virtuales de los errores en el nivel de centro de datos, puede crear un conjunto de escalado en todas las zonas de disponibilidad. Las regiones de Azure que admiten las Zonas de disponibilidad tienen un mínimo de tres zonas independientes, cada una de las cuales con su propia fuente de alimentación, red y sistema de refrigeración. Para obtener más información, consulte [Introducción a las zonas de disponibilidad](../availability-zones/az-overview.md).

[!INCLUDE [availability-zones-preview-statement.md](../../includes/availability-zones-preview-statement.md)]


## <a name="single-zone-and-zone-redundant-scale-sets"></a>Conjuntos de escalado de una sola zona y con redundancia de zona
Cuando implementa un conjunto de escalado de máquinas virtuales, puede elegir usar una sola zona de disponibilidad en una región o varias zonas.

Cuando crea un conjunto de escalado en una sola zona, controla en qué zona se ejecutan todas esas instancias de VM y el conjunto de escalado se administra y se escala automáticamente solo dentro de esa zona. Un conjunto de escalado con redundancia de zona permite crear un conjunto de escalado único que abarque varias zonas. De manera predeterminada, cuando se crean las instancias de VM, se equilibran de manera uniforme entre las distintas zonas. En caso de que se produzca una interrupción en una de las zonas, un conjunto de escalado no escala automáticamente de manera horizontal para aumentar la capacidad. Un procedimiento recomendado sería configurar reglas de escalado automático según el uso de la memoria o CPU. Las reglas de escalado automático permitirían que el conjunto de escalado responda ante una pérdida de las instancias de VM en esa zona mediante el escalado horizontal de instancias nuevas en las zonas operativas.

Para usar las Zonas de disponibilidad, el conjunto de escalado se debe crear en una [región de Azure compatible](../availability-zones/az-overview.md#regions-that-support-availability-zones). También necesita [registrarse para obtener la versión preliminar de las Zonas de disponibilidad](http://aka.ms/azenroll). Puede crear un conjunto de escalado que use las Zonas de disponibilidad con uno de los métodos siguientes:

- [portal de Azure](#use-the-azure-portal)
- [CLI de Azure 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Plantillas del Administrador de recursos de Azure](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>Uso de Azure Portal
El proceso para crear un conjunto de escalado que usa una zona de disponibilidad es igual al que se detalla en el [artículo de introducción](virtual-machine-scale-sets-create-portal.md). Asegúrese de haberse [registrado para obtener la versión preliminar de las Zonas de disponibilidad](http://aka.ms/azenroll). Cuando selecciona una región de Azure compatible, puede crear un conjunto de escalado en una de las zonas disponibles, tal como se muestra en el ejemplo siguiente:

![Creación de un conjunto de escalado en una sola zona de disponibilidad](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

El conjunto de escalado y los recursos de apoyo, como la dirección IP pública y Azure Load Balancer, se crean en la zona única que se especifica.


## <a name="use-the-azure-cli-20"></a>Uso de la CLI de Azure 2.0
El proceso para crear un conjunto de escalado que usa una zona de disponibilidad es igual al que se detalla en el [artículo de introducción](virtual-machine-scale-sets-create-cli.md). Para usar las Zonas de disponibilidad, debe crear el conjunto de escalado en una región de Azure compatible y haberse [registrado para obtener la versión preliminar de las Zonas de disponibilidad](http://aka.ms/azenroll).

Agregue el parámetro `--zones` al comando [az vmss create](/cli/azure/vmss#az_vmss_create) y especifique qué zona se usará (como la zona *1*, *2* o *3*). En el ejemplo siguiente se crea un conjunto de escalado de una sola zona denominado *myScaleSet* en la zona *1*:

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
Para ver un ejemplo completo de los recursos de red y un conjunto de escalado de una sola zona, consulte [este script de CLI de ejemplo](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh.)

### <a name="zone-redundant-scale-set"></a>Conjunto de escalado con redundancia de zona
Para crear un conjunto de escalado con redundancia de zona, use una dirección IP pública de SKU *estándar* y un equilibrador de carga. Para obtener una mejor redundancia, la SKU *estándar* crea recursos de red con redundancia de zona. Para más información, consulte [Introducción a Azure Load Balancer estándar](../load-balancer/load-balancer-standard-overview.md). La primera vez que crea un equilibrador de carga o un conjunto de escalado con redundancia de zona, debe completar estos pasos para registrar la cuenta y obtener estas características de versión preliminar.

1. Registre su cuenta para obtener las características de red y el conjunto de escalado con redundancia de zona con [az feature register](/cli/azure/feature#az_feature_register), tal como se indica a continuación:

    ```azurecli
    az feature register --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```
    
2. El proceso de registro para obtener las características puede tardar unos minutos. Puede comprobar el estado de la operación con [az feature show](/cli/azure/feature#az_feature_show):

    ```azurecli
    az feature show --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature show --name AllowLBPreview --namespace Microsoft.Network
    ```

    En el ejemplo siguiente se muestra el estado deseado de la característica como *Registrado*:
    
    ```json
    "properties": {
          "state": "Registered"
       },
    ```

3. Una vez que tanto los recursos de red como el conjunto d escalado con redundancia de zona aparezcan con el estado *Registrado*, vuelva a registrar los proveedores de *proceso* y *red* con [az provider register](/cli/azure/provider#az_provider_register), tal como se indica a continuación:

    ```azurecli
    az provider register --namespace Microsoft.Compute
    az provider register --namespace Microsoft.Network
    ```

Para crear un conjunto de escalado con redundancia de zona, especifique varias zonas con el parámetro `--zones`. En el ejemplo siguiente se crea un conjunto de escalado con redundancia de zona denominado *myScaleSet* en las zonas *1,2,3*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones {1,2,3}
```

Se tardan unos minutos en crear y configurar todos los recursos de conjunto de escalado y máquinas virtuales en las zonas que especifica. Para ver un ejemplo completo de los recursos de red y un conjunto de escalado con redundancia de zona, consulte [este script de CLI de ejemplo](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)


## <a name="use-azure-powershell"></a>Uso de Azure PowerShell
El proceso para crear un conjunto de escalado que usa una zona de disponibilidad es igual al que se detalla en el [artículo de introducción](virtual-machine-scale-sets-create-powershell.md). Para usar las Zonas de disponibilidad, debe crear el conjunto de escalado en una región de Azure compatible y haberse [registrado para obtener la versión preliminar de las Zonas de disponibilidad](http://aka.ms/azenroll). Agregue el parámetro `-Zone` al comando [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) y especifique qué zona se usará (como la zona *1*, *2* o *3*). 

En el ejemplo siguiente se crea una configuración de conjunto de escalado de una sola zona denominada *vmssConfig* en *Este de EE. UU. 2* zona *1*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

Para ver un ejemplo completo de los recursos de red y un conjunto de escalado de una sola zona, consulte [este script de PowerShell de ejemplo](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1)

### <a name="zone-redundant-scale-set"></a>Conjunto de escalado con redundancia de zona
Para crear un conjunto de escalado con redundancia de zona, use una dirección IP pública de SKU *estándar* y un equilibrador de carga. Para obtener una mejor redundancia, la SKU *estándar* crea recursos de red con redundancia de zona. Para más información, consulte [Introducción a Azure Load Balancer estándar](../load-balancer/load-balancer-standard-overview.md). La primera vez que crea un equilibrador de carga o un conjunto de escalado con redundancia de zona, debe completar estos pasos para registrar la cuenta y obtener estas características de versión preliminar.

1. Registre su cuenta para obtener las características de red y el conjunto de escalado con redundancia de zona con [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature), tal como se indica a continuación:

    ```powershell
    Register-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```
    
2. El proceso de registro para obtener las características puede tardar unos minutos. Puede comprobar el estado de la operación con [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature):

    ```powershell
    Get-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute 
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

    En el ejemplo siguiente se muestra el estado deseado de la característica como *Registrado*:
    
    ```powershell
    RegistrationState
    -----------------
    Registered
    ```

3. Una vez que tanto los recursos de red como el conjunto d escalado con redundancia de zona aparezcan con el estado *Registrado*, vuelva a registrar los proveedores de *proceso* y *red* con [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider), tal como se indica a continuación:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```

Para crear un conjunto de escalado con redundancia de zona, especifique varias zonas con el parámetro `-Zone`. En el ejemplo siguiente se crea una configuración de conjunto de escalado con redundancia de zona denominado *myScaleSet* en *Este de EE. UU. 2* zonas *1, 2, 3*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1", "2", "3"
```

Si crea una dirección IP pública con [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) o un equilibrador de carga con [New-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/New-AzureRmLoadBalancer), especifique el valor *-SKU "estándar"* para crear los recursos de red con redundancia de zona. También debe crear un grupo de seguridad de red y reglas para permitir cualquier tráfico. Para más información, consulte [Introducción a Azure Load Balancer estándar](../load-balancer/load-balancer-standard-overview.md).

Para ver un ejemplo completo de los recursos de red y un conjunto de escalado con redundancia de zona, consulte [este script de PowerShell de ejemplo](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1)


## <a name="use-azure-resource-manager-templates"></a>Utilización de plantillas del Administrador de recursos de Azure
El proceso para crear un conjunto de escalado que usa una zona de disponibilidad es igual al que se detalla en el artículo de introducción para [Linux](virtual-machine-scale-sets-create-template-linux.md) o [Windows](virtual-machine-scale-sets-create-template-windows.md). Para usar las Zonas de disponibilidad, debe crear el conjunto de escalado en una región de Azure compatible y haberse [registrado para obtener la versión preliminar de las Zonas de disponibilidad](http://aka.ms/azenroll). Agregue la propiedad `zones` al tipo de recurso *Microsoft.Compute/virtualMachineScaleSets* en la plantilla y especifique qué zona se va a usar (como la zona *1*, *2* o *3*).

En el ejemplo siguiente se crea un conjunto de escalado Linux de una sola zona denominado *myScaleSet* en *Este de EE. UU. 2* zona *1*:

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

Para ver un ejemplo completo de los recursos de red y un conjunto de escalado de una sola zona, consulte [esta plantilla de Resource Manager de ejemplo](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Conjunto de escalado con redundancia de zona
Para crear un conjunto de escalado con redundancia de zona, especifique varios valores en la propiedad `zones` del tipo de recurso *Microsoft.Compute/virtualMachineScaleSets*. En el ejemplo siguiente se crea un conjunto de escalado con redundancia de zona denominado *myScaleSet* en *Este de EE. UU. 2* zonas *1,2,3*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

Si crea un equilibrador de carga o una dirección IP pública especifique la propiedad *"sku": { "nombre": "estándar" }"* para crear los recursos de red con redundancia de zona. También debe crear un grupo de seguridad de red y reglas para permitir cualquier tráfico. Para más información, consulte [Introducción a Azure Load Balancer estándar](../load-balancer/load-balancer-standard-overview.md).

Para ver un ejemplo completo de los recursos de red y un conjunto de escalado con redundancia de zona, consulte [esta plantilla de Resource Manager de ejemplo](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)


## <a name="next-steps"></a>pasos siguientes
Ahora que creó un conjunto de escalado en una zona de disponibilidad, puede obtener información sobre cómo [implementar aplicaciones en conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-deploy-app.md) o [usar el escalado automático con conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-autoscale-overview.md).
