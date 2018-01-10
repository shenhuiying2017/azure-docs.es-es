---
title: "Escalado automático de conjuntos de escalado de máquinas virtuales con Azure PowerShell | Microsoft Docs"
description: "Creación de reglas de escalado automático de conjuntos de escalado de máquinas virtuales con Azure PowerShell"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 8928e56f353858234db314714d411a9c2990eb4e
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Creación de reglas de escalado automático de conjuntos de escalado de máquinas virtuales con Azure PowerShell
Al crear un conjunto de escalado, puede definir el número de instancias de máquina virtual que quiere ejecutar. A medida que cambia la demanda de las aplicaciones, puede aumentar o reducir automáticamente el número de estas instancias. La posibilidad de realizar el escalado automático le permite satisfacer la demanda del cliente o responder a los cambios de rendimiento de la aplicación a lo largo del ciclo de vida de esta.

Este artículo muestra cómo crear reglas de escalado automático con Azure PowerShell que supervisan el rendimiento de las instancias de máquina virtual del conjunto de escalado. Estas reglas de escalado automático permiten aumentar o reducir el número de instancias de máquina virtual en respuesta a estas métricas de rendimiento. Estos pasos también se pueden llevar a cabo con la [CLI de Azure 2.0](virtual-machine-scale-sets-autoscale-cli.md) o [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md).


## <a name="prerequisites"></a>requisitos previos
Para crear reglas de escalado automático, necesita un conjunto de escalado de máquinas virtuales ya existente. Puede crear un conjunto de escalado mediante [Azure Portal](virtual-machine-scale-sets-create-portal.md), [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) o la [CLI de Azure 2.0](virtual-machine-scale-sets-create-cli.md).

Para facilitar la creación de las reglas de escalado automático, defina algunas de las variables para el conjunto de escalado. En el siguiente ejemplo se definen variables para el conjunto de escalado denominado *myScaleSet* del grupo de recursos *myResourceGroup* en la región *Este de EE. UU.* El identificador de la suscripción se obtiene con [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription). Si tiene varias suscripciones asociadas a su cuenta, solo se devolverá la primera suscripción. Ajuste los nombres y los identificadores de la suscripción como se indica a continuación:

```powershell
$mySubscriptionId = (Get-AzureRmSubscription).Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```


## <a name="create-a-rule-to-automatically-scale-out"></a>Creación de una regla para realizar un escalado horizontal de forma automática
Si aumenta la demanda de la aplicación, la carga de las instancias de máquina virtual del conjunto de escalado aumenta. Si este aumento de la carga es continuado, en lugar de ser algo puntual, puede configurar reglas de escalado automático para aumentar el número de instancias de máquina virtual en el conjunto de escalado. Cuando se crean estas instancias de máquina virtual y se implementan las aplicaciones, el conjunto de escalado empieza a distribuir el tráfico entre ellas mediante el equilibrador de carga. Puede controlar qué métricas se deben supervisar como, por ejemplo, la CPU o el disco, cuánto tiempo debe cumplir la carga de la aplicación un límite determinado y cuántas instancias de máquina virtual se deben agregar al conjunto de escalado.

Vamos a crear una regla con [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) que aumenta el número de instancias de máquina virtual de un conjunto de escalado cuando la carga promedio de la CPU es superior al 70 % durante un período de más de 5 minutos. Cuando se desencadena la regla, aumenta el número de instancias de máquina virtual en un 20 %. En conjuntos de escalado con un pequeño número de instancias de máquina virtual, podría omitir `-ScaleActionScaleType` y especificar solo `-ScaleActionValue` para aumentar en *1* o *2* instancias. En conjuntos de escalado con un gran número de instancias de máquina virtual, un aumento del 10 % o 20 % de instancias puede resultar más adecuado.

Los siguientes parámetros se utilizan para esta regla:

| .               | Explicación                                                                                                         | Valor          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | La métrica de rendimiento del conjunto de escalado sobre el que realizar las acciones de supervisión y aplicación.                                                   | Porcentaje de CPU |
| *-TimeGrain*            | Frecuencia de recopilación de las métricas para el análisis.                                                                   | 1 minuto       |
| *-MetricStatistic*      | Define cómo se deben agregar las métricas recopiladas para el análisis.                                                | Media        |
| *-TimeWindow*           | El periodo durante el que se realiza la supervisión antes de que se comparen los valores de métricas y umbrales.                                   | 10 minutos      |
| *-Operator*             | El operador que se utiliza para comparar los datos de las métricas con los umbrales.                                                     | Mayor que   |
| *-Threshold*            | El valor que hace que la regla de escalado automático desencadene una acción.                                                      | 70%            |
| *-ScaleActionDirection* | Define si el conjunto de escalado debe escalarse o reducirse verticalmente.                                             | Aumento       |
| *–ScaleActionScaleType* | Indica que el número de instancias de máquina virtual debe cambiarse por un porcentaje.                                 | Cambio de porcentaje |
| *-ScaleActionValue*     | El porcentaje de instancias de máquina virtual se debe cambiar al desencadenarse la regla.                                            | 20              |
| *-ScaleActionCooldown*  | El periodo que hay que esperar hasta que la regla se vuelva a aplicar, para que las acciones de escalado automático tengan tiempo de surtir efecto. | 5 minutos      |

En el ejemplo siguiente se crea un objeto denominado *myRuleScaleOut* que contiene esta regla de escalado vertical. *- MetricResourceId* usa las variables definidas anteriormente para el identificador de la suscripción, el nombre del grupo de recursos y el nombre del conjunto de escalado:

```powershell
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic Average `
  -TimeWindow 00:10:00 `
  -Operator GreaterThan `
  -Threshold 70 `
  -ScaleActionDirection Increase `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-automatically-scale-in"></a>Creación de una regla para realizar una reducción horizontal de forma automática
La demanda de la aplicación puede reducirse por las tardes o durante los fines de semana. Si esta reducción es constante a lo largo de un período, puede configurar reglas de escalado automático para reducir el número de instancias de máquina virtual del conjunto de escalado. Esta acción de reducción horizontal permite rebajar el costo de ejecutar el conjunto de escalado ya que solo se ejecuta el número de instancias necesario para satisfacer la demanda actual.

Cree una regla con [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) que reduce el número de instancias de máquina virtual de un conjunto de escalado cuando la carga promedio de la CPU es inferior al 30 % durante un período de más de 10 minutos. Cuando la regla se desencadena, se reduce el número de instancias de máquina virtual en un 20 %. En el ejemplo siguiente se crea un objeto denominado *myRuleScaleDown* que contiene esta regla de escalado vertical. *- MetricResourceId* usa las variables definidas anteriormente para el identificador de la suscripción, el nombre del grupo de recursos y el nombre del conjunto de escalado:

```powershell
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:10:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20
```


## <a name="define-an-autoscale-profile"></a>Definición de un perfil de escalado automático
Para asociar las reglas de escalado automático a un conjunto de escalado, debe crear un perfil. El perfil de escalado automático define la capacidad predeterminada, mínima y máxima del conjunto de escalado y asocia las reglas de escalado automático. Cree un perfil de escalado automático con [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile). En el ejemplo siguiente se establece la capacidad predeterminada y la mínima para *2* instancias de máquina virtual y un máximo de *10*. Las reglas de escalado y reducción horizontal creadas en los pasos anteriores se asocian ahora:

```powershell
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Aplicación de reglas de escalado automático a un conjunto de escalado
El último paso consiste en aplicar el perfil de escalado automático al conjunto de escalado. Posteriormente, la escala se puede escalar o reducir horizontalmente en función de la demanda de la aplicación. Aplique el perfil de escalado automático con [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) como se indica a continuación:

```powershell
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Supervisión del número de instancias de un conjunto de escalado
Para ver el número y el estado de las instancias de máquina virtual, vea una lista de instancias del conjunto de escalado mediante [Get-AzureRmVmssVM](/powershell/module/AzureRM.Compute/Get-AzureRmVmssVM). El estado indica si la instancia de la máquina virtual se está aprovisionando a medida que el conjunto de escalado se escala horizontalmente de forma automática, o se está desaprovisionando a medida que la escala se reduce horizontalmente de forma automática. En el ejemplo siguiente se ve el estado de la instancia de la máquina virtual del conjunto de escalado denominado *myScaleSet* en el grupo de recursos denominado *myResourceGroup*:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="autoscale-based-on-a-schedule"></a>Escalado automático según una programación
En los ejemplos anteriores se escaló o redujo horizontalmente de forma automática un conjunto de escalado con métricas de host básicas como el uso de la CPU. También se pueden crear reglas de escalado automático basadas en programación. Estas reglas basadas en programaciones le permiten escalar horizontalmente de forma automática el número de instancias de máquina virtual anticipándose a un aumento de la demanda de la aplicación como, por ejemplo, durante las horas punta del trabajo para, a continuación, reducir horizontalmente de forma automática el número de instancias a la vez si anticipa una menor demanda como durante los fines de semana.

Para crear reglas de escalado automático basadas en una programación en lugar de en métricas del host, use Azure Portal. Actualmente, no se pueden crear reglas basadas en programaciones con Azure PowerShell.


## <a name="next-steps"></a>pasos siguientes
En este artículo, ha aprendido a utilizar reglas de escalado automático para escalar horizontalmente y aumentar o reducir el *número* de instancias de máquina virtual del conjunto de escalado. También puede escalar verticalmente para aumentar o reducir el *tamaño* de la instancia de máquina virtual. Para más información, consulte [Escalado automático vertical con conjuntos de escalado de máquinas virtuales](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Para más información acerca de cómo administrar las instancias de máquina virtual, consulte [Manage virtual machine scale sets with Azure PowerShell](virtual-machine-scale-sets-windows-manage.md) (Administración de conjuntos de escalado de máquinas virtuales con Azure PowerShell).

Para más información acerca de cómo generar alertas cuando la regla de escalado automático se desencadena, consulte [Uso de acciones de escalado automático para enviar notificaciones de alerta por correo electrónico y Webhook en Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). También puede consultar [Llamada a un webhook cuando se activan alertas del registro de actividades de Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
