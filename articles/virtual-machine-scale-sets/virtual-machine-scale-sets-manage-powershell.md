---
title: "Administrar conjuntos de escalado de máquinas virtuales con Azure PowerShell | Microsoft Docs"
description: "Cmdlets de Azure PowerShell comunes para administrar conjuntos de escalado de máquinas virtuales, por ejemplo, cómo iniciar y detener una instancia, o cambiar la capacidad del conjunto de escalado."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: 5b5f3eb05f0d6c10f7efe8af1b93b2cb4fc585c5
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Administrar un conjunto de escalado de máquinas virtuales con Azure PowerShell
Durante el ciclo de vida de la máquina virtual, es posible que deba ejecutar una o varias tareas de administración. Además, puede crear scripts para automatizar varias tareas de ciclo de vida. En este artículo se detallan algunos de los cmdlets comunes de Azure PowerShell que le permiten realizar estas tareas.

Para completar estas tareas de administración, se requiere el módulo de Azure PowerShell más reciente. Para obtener información acerca de cómo instalar y usar la versión más reciente, consulte [Introducción a Azure PowerShell](/powershell/azure/get-started-azureps). Si necesita crear un conjunto de escalado de máquinas virtuales, puede [crear un conjunto de escalado en Azure Portal](virtual-machine-scale-sets-create-portal.md).


## <a name="view-information-about-a-scale-set"></a>Visualización de información acerca de un conjunto de escalado
Para ver información general acerca de un conjunto de escalado, use [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss). En el ejemplo siguiente se obtiene información acerca del conjunto de escalado denominado *myScaleSet* del grupo de recursos *myResourceGroup*. Escriba sus propios nombres, como se indica a continuación:

```powershell
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Visualización de máquinas virtuales en un conjunto de escalado
Para ver una lista de las instancias de VM en un conjunto de escalado, use [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). En el ejemplo siguiente se enumeran todas las instancias de VM en el conjunto de escalado denominado *myScaleSet* y en el grupo de recursos *myResourceGroup*. Proporcione sus propios valores para estos nombres:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Para ver información adicional acerca de una instancia específica de la máquina virtual, agregue el parámetro `-InstanceId` a [AzureRmVmssVM Get](/powershell/module/azurerm.compute/get-azurermvmssvm) y especifique una instancia para verla. En el ejemplo siguiente se puede consultar información acerca de la instancia de VM *0* en el conjunto de escalado denominado *myScaleSet* y el grupo de recursos *myResourceGroup*. Escriba sus propios nombres, como se indica a continuación:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Cambio de la capacidad de un conjunto de escalado
Los comandos anteriores muestran información acerca del conjunto de escalado y las instancias de VM. Para aumentar o disminuir el número de instancias en el conjunto de escalado, puede cambiar la capacidad. El conjunto de escalado crea o elimina automáticamente el número necesario de máquinas virtuales, y configura las máquinas virtuales para recibir tráfico de la aplicación.

En primer lugar, cree un objeto de conjunto de escalado con [Get AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) y, después, especifique un nuevo valor para `sku.capacity`. Para aplicar el cambio de capacidad, use [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). En el ejemplo siguiente se actualiza *myScaleSet* en el grupo de recursos *myResourceGroup* a una capacidad de *5* instancias. Proporcione sus propios valores, como se indica a continuación:

```powershell
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

La capacidad del conjunto de escalado tarda unos minutos en actualizarse. Si reduce la capacidad de un conjunto de escalado, se quitan primero las máquinas virtuales con los identificadores de instancia más altos.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Detención e inicio de las máquinas virtuales de un conjunto de escalado
Para detener una o más máquinas virtuales en un conjunto de escalado, use [Stop-AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss). El parámetro `-InstanceId` le permite especificar una o más máquinas virtuales para que se detengan. Si no especifica un identificador de instancia, se detienen todas las máquinas virtuales del conjunto de escalado. Para detener varias máquinas virtuales, separe cada identificador de instancia con una coma.

En el ejemplo siguiente se detiene la instancia *0* del conjunto de escalado denominado *myScaleSet* y el grupo de recursos *myResourceGroup*. Proporcione sus valores, como se indica a continuación:

```powershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

De forma predeterminada, se cancela la asignación de las máquinas virtuales para no acumular cargos de proceso. Si quiere que la máquina virtual permanezca en un estado de aprovisionamiento cuando se detenga, agregue el parámetro `-StayProvisioned` al comando anterior. Las máquinas virtuales detenidas que se mantienen aprovisionadas acumulan cargos regulares de proceso.


### <a name="start-vms-in-a-scale-set"></a>Inicio de máquinas virtuales en un conjunto de escalado
Para iniciar una o más máquinas virtuales en un conjunto de escalado, use [Start-AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss). El parámetro `-InstanceId` le permite especificar una o más máquinas virtuales para que se inicien. Si no especifica un identificador de instancia, se inician todas las máquinas virtuales del conjunto de escalado. Para iniciar varias máquinas virtuales, separe cada identificador de instancia con una coma.

En el ejemplo siguiente se inicia la instancia *0* del conjunto de escalado denominado *myScaleSet* y el grupo de recursos *myResourceGroup*. Proporcione sus valores, como se indica a continuación:

```powershell
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Reinicio de máquinas virtuales en un conjunto de escalado
Para reiniciar una o más máquinas virtuales en un conjunto de escalado, use [Restart-AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss). El parámetro `-InstanceId` le permite especificar una o más máquinas virtuales para que se reinicien. Si no especifica un identificador de instancia, se reinician todas las máquinas virtuales del conjunto de escalado. Para reiniciar varias máquinas virtuales, separe cada identificador de instancia con una coma.

En el ejemplo siguiente se reinicia la instancia *0* del conjunto de escalado denominado *myScaleSet* y el grupo de recursos *myResourceGroup*. Proporcione sus valores, como se indica a continuación:

```powershell
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Eliminación de las máquinas virtuales de un conjunto de escalado
Para quitar una o más máquinas virtuales en un conjunto de escalado, use [Remove-AzureRmVmss](/powershell/module/azurerm.compute/remove-azurermvmss). El parámetro `-InstanceId` le permite especificar una o más máquinas virtuales para que se eliminen. Si no especifica un identificador de instancia, se eliminan todas las máquinas virtuales del conjunto de escalado. Para eliminar varias máquinas virtuales, separe cada identificador de instancia con una coma.

En el ejemplo siguiente se elimina la instancia *0* del conjunto de escalado denominado *myScaleSet* y el grupo de recursos *myResourceGroup*. Proporcione sus valores, como se indica a continuación:

```powershell
Remove-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>pasos siguientes
Entre otras tareas comunes para conjuntos de escalado se incluye cómo [implementar una aplicación](virtual-machine-scale-sets-deploy-app.md) y cómo [actualizar instancias de máquinas virtuales](virtual-machine-scale-sets-upgrade-scale-set.md). También puede usar Azure PowerShell para [configurar reglas de escalado automático](virtual-machine-scale-sets-autoscale-overview.md).
