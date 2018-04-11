---
title: Creación de un conjunto de escalado de Azure que usa Zonas de disponibilidad | Microsoft Docs
description: Obtenga información sobre cómo crear conjuntos de escalado de máquinas virtuales de Azure que usan Zonas de disponibilidad para aumentar la redundancia contra las interrupciones
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: iainfou
ms.openlocfilehash: dee06eee045bc24c2864333a66a6d145a771b3ad
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Creación de un conjunto de escalado de máquinas virtuales que usa Zonas de disponibilidad
Para proteger los conjuntos de escalado de máquinas virtuales de los errores en el nivel de centro de datos, puede crear un conjunto de escalado en todas las zonas de disponibilidad. Las regiones de Azure que admiten las Zonas de disponibilidad tienen un mínimo de tres zonas independientes, cada una de las cuales con su propia fuente de alimentación, red y sistema de refrigeración. Para obtener más información, consulte [Introducción a las zonas de disponibilidad](../availability-zones/az-overview.md).


## <a name="single-zone-and-zone-redundant-scale-sets"></a>Conjuntos de escalado de una sola zona y con redundancia de zona
Cuando implementa un conjunto de escalado de máquinas virtuales, puede elegir usar una sola zona de disponibilidad en una región o varias zonas.

Cuando crea un conjunto de escalado en una sola zona, controla en qué zona se ejecutan todas esas instancias de VM y el conjunto de escalado se administra y se escala automáticamente solo dentro de esa zona. Un conjunto de escalado con redundancia de zona permite crear un conjunto de escalado único que abarque varias zonas. De manera predeterminada, cuando se crean las instancias de VM, se equilibran de manera uniforme entre las distintas zonas. En caso de que se produzca una interrupción en una de las zonas, un conjunto de escalado no escala automáticamente de manera horizontal para aumentar la capacidad. Un procedimiento recomendado sería configurar reglas de escalado automático según el uso de la memoria o CPU. Las reglas de escalado automático permitirían que el conjunto de escalado responda ante una pérdida de las instancias de VM en esa zona mediante el escalado horizontal de instancias nuevas en las zonas operativas.

Para usar las Zonas de disponibilidad, el conjunto de escalado se debe crear en una [región de Azure compatible](../availability-zones/az-overview.md#regions-that-support-availability-zones). Puede crear un conjunto de escalado que use las Zonas de disponibilidad con uno de los métodos siguientes:

- [Azure Portal](#use-the-azure-portal)
- [CLI de Azure 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Plantillas del Administrador de recursos de Azure](#use-azure-resource-manager-templates)

## <a name="availability-considerations"></a>Consideraciones sobre disponibilidad
A partir de la versión 2017-12-01 de la API, al implementar un conjunto de escalado en una o más zonas, tiene la opción de hacerlo con propagación máxima o estática de 5 dominios de error. Con la propagación máxima, el conjunto de escalado propaga las máquinas virtuales en tantos dominios de error como sea posible dentro de cada zona. Esta propagación podría incluir más o menos de cinco dominios de error por zona. Por otra parte, con la propagación estática en 5 dominios de error, el conjunto de escalado propaga las máquinas virtuales en exactamente 5 dominios de error por zona. Si el conjunto de escalado no encuentra 5 dominios de error distintos por zona para satisfacer la solicitud de asignación, se produce un error en la solicitud.

**Se recomienda implementar con propagación máxima la mayoría de las cargas de trabajo**, ya que esta ofrece la mejor propagación en la mayoría de los casos. Si necesita que las réplicas se incluyan en unidades de aislamiento de hardware distintas, se recomienda la propagación en distintas zonas de disponibilidad y el empleo de la propagación máxima en cada una de ellas. Tenga en cuenta que, con la propagación máxima, solo verá un dominio de error en la vista de la instancia de la máquina virtual del conjunto de escalado y en los metadatos de la instancia, independientemente de la cantidad en la que se hayan propagado las máquinas virtuales en realidad; la propagación dentro de cada zona está implícita.

Para la propagación máxima, establezca "platformFaultDomainCount" en 1. Para la propagación en 5 dominios de error, establezca "platformFaultDomainCount" en 5. En versión 2017-12-01 de la API, el valor predeterminado de "platformFaultDomainCount" es 1 para los conjuntos de escalado de una zona y de varias. Actualmente, solo se admite la propagación en 5 dominios de error para los conjuntos de escalado regionales.

Además, al implementar un conjunto de escalado, tiene la opción de hacerlo con un solo [grupo de selección de ubicación](./virtual-machine-scale-sets-placement-groups.md) por zona de disponibilidad o con varios por cada zona (para los conjuntos de escalado regionales, la opción es tener un grupo único de selección de ubicación en la región o varios). Para la mayoría de las cargas de trabajo, se recomiendan varios grupos de selección de ubicación, lo que permite mayor escala. En la versión 2017-12-01 de la API, los conjuntos de escalado de una o varias zonas tienen varios grupos de selección de ubicación de manera predeterminada, pero se cambia de manera predeterminada a un único grupo para los conjuntos de escalado regionales.

>[!NOTE]
> Si usa la propagación máxima, debe usar varios grupos de selección de ubicación.

Por último, para conjuntos de escalado implementados en varias zonas, también tiene la opción de elegir la mejor opción de equilibrio de zonas o equilibrio de zonas estricto. Un conjunto de escalado se considera equilibrado si el número de máquinas virtuales de cada zona está comprendido en el número de máquinas virtuales de las demás. Por ejemplo, se considera equilibrado un conjunto de escalado con dos máquinas virtuales en la zona 1, tres en la zona 2 y tres en la zona 3. Sin embargo, un conjunto de escalado con una máquina virtual en la zona 1, tres en la zona 2 y tres en la zona 3 se considera desequilibrado. Es posible que las máquinas virtuales del conjunto de escalado se creen correctamente, aunque su extensión produzca un error. Estas máquinas virtuales con errores de extensión se siguen teniendo en cuenta para determinar si un conjunto de escalado está equilibrado. Por ejemplo, un conjunto de escalado con tres máquinas virtuales en la zona 1, tres en la zona 2 y tres en la zona 3 se considera equilibrado aunque todas las extensiones produzcan un error en la zona 1 y sean correctas en las zonas 2 y 3. Con la mejor opción de equilibrio de zonas, el conjunto de escalado intenta aumentarse y reducirse horizontalmente, y conserva el equilibrio. Sin embargo, si por alguna razón no es posible (por ejemplo, que una zona deje de funcionar, y, como consecuencia, el conjunto de escala no pueda crear una máquina virtual en esa zona), el conjunto de escalado permite el desequilibrio temporal para realizar correctamente el aumento o la reducción horizontal. Para los posteriores intentos de escalado horizontal, el conjunto de escalado e máquinas virtuales a las zonas donde se necesitan más para conseguir el equilibrio. Análogamente, para los posteriores intentos de reducción horizontal, el conjunto de escalado elimina máquinas virtuales de las zonas donde se necesitan menos para conseguir el equilibrio. Por otro lado, con el equilibrio de zonas estricto, el conjunto de escalado produce un error al intentar el aumento o la reducción horizontal si ello causara desequilibrio.

Para usar la mejor opción de equilibrio de zonas, establezca "zoneBalance" en false (valor predeterminado en la versión 2017-12-01 de la API). Para usar el equilibrio de zonas estricto, establezca "zoneBalance" en true.

## <a name="use-the-azure-portal"></a>Uso de Azure Portal
El proceso para crear un conjunto de escalado que usa una zona de disponibilidad es igual al que se detalla en el [artículo de introducción](quick-create-portal.md). Asegúrese de haberse [registrado para obtener la versión preliminar de las Zonas de disponibilidad](http://aka.ms/azenroll). Cuando selecciona una región de Azure compatible, puede crear un conjunto de escalado en una de las zonas disponibles, tal como se muestra en el ejemplo siguiente:

![Creación de un conjunto de escalado en una sola zona de disponibilidad](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

El conjunto de escalado y los recursos de apoyo, como la dirección IP pública y Azure Load Balancer, se crean en la zona única que se especifica.


## <a name="use-the-azure-cli-20"></a>Uso de la CLI de Azure 2.0
El proceso para crear un conjunto de escalado que usa una zona de disponibilidad es igual al que se detalla en el [artículo de introducción](quick-create-cli.md). Para usar las Zonas de disponibilidad, debe crear el conjunto de escalado en una región de Azure compatible y haberse [registrado para obtener la versión preliminar de las Zonas de disponibilidad](http://aka.ms/azenroll).

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
Para crear un conjunto de escalado con redundancia de zona, use una dirección IP pública de SKU *estándar* y un equilibrador de carga. Para obtener una mejor redundancia, la SKU *estándar* crea recursos de red con redundancia de zona. Para más información, consulte [Introducción a Azure Load Balancer estándar](../load-balancer/load-balancer-standard-overview.md). 

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
El proceso para crear un conjunto de escalado que usa una zona de disponibilidad es igual al que se detalla en el [artículo de introducción](quick-create-powershell.md). Para usar las Zonas de disponibilidad, debe crear el conjunto de escalado en una región de Azure compatible y haberse [registrado para obtener la versión preliminar de las Zonas de disponibilidad](http://aka.ms/azenroll). Agregue el parámetro `-Zone` al comando [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) y especifique qué zona se usará (como la zona *1*, *2* o *3*). 

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
Para crear un conjunto de escalado con redundancia de zona, use una dirección IP pública de SKU *estándar* y un equilibrador de carga. Para obtener una mejor redundancia, la SKU *estándar* crea recursos de red con redundancia de zona. Para más información, consulte [Introducción a Azure Load Balancer estándar](../load-balancer/load-balancer-standard-overview.md).

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
El proceso para crear un conjunto de escalado que usa una zona de disponibilidad es igual al que se detalla en el artículo de introducción para [Linux](quick-create-template-linux.md) o [Windows](quick-create-template-windows.md). Para usar las Zonas de disponibilidad, debe crear el conjunto de escalado en una región de Azure compatible y haberse [registrado para obtener la versión preliminar de las Zonas de disponibilidad](http://aka.ms/azenroll). Agregue la propiedad `zones` al tipo de recurso *Microsoft.Compute/virtualMachineScaleSets* en la plantilla y especifique qué zona se va a usar (como la zona *1*, *2* o *3*).

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

Para ver un ejemplo completo de los recursos de red y un conjunto de escalado de una sola zona, consulte [esta plantilla de Resource Manager de ejemplo](https://github.com/Azure/vm-scale-sets/blob/master/zones/singlezone.json)

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

Para ver un ejemplo completo de los recursos de red y un conjunto de escalado con redundancia de zona, consulte [esta plantilla de Resource Manager de ejemplo](https://github.com/Azure/vm-scale-sets/blob/master/zones/multizone.json)


## <a name="next-steps"></a>Pasos siguientes
Ahora que creó un conjunto de escalado en una zona de disponibilidad, puede obtener información sobre cómo [implementar aplicaciones en conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-deploy-app.md) o [usar el escalado automático con conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-autoscale-overview.md).
