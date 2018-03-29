---
title: Creación de un conjunto de escalado de máquinas virtuales de Azure | Microsoft Docs
description: Modificación de un conjunto de escalado de máquinas virtuales de Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: negat
ms.openlocfilehash: fcca912a8120a51d2f0a454ef0a6341cd5882015
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>Modificación de un conjunto de escalado de máquinas virtuales
En este artículo se describe cómo modificar un conjunto de escalado de máquinas virtuales existente. En las tareas se incluye cómo cambiar la configuración del conjunto de escalado, cómo cambiar la configuración de las aplicaciones que se ejecutan en el conjunto de escalado, cómo administrar la disponibilidad y mucho más.

## <a name="fundamental-concepts"></a>Conceptos básicos

### <a name="scale-set-model"></a>Modelo de conjunto de escalado

Un conjunto de escalado tiene un modelo que captura el estado *deseado* del conjunto de escalado completo. Para consultar el modelo de un conjunto de escalado, puede usar:

* API REST: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` 
   
  Para más información, consulte la [documentación de la API de REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/get).

* PowerShell:

  `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}`
   
  Para más información, consulte la [documentación de PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss).

* CLI de Azure: 

  `az vmss show -g {resourceGroupName} -n {vmSaleSetName}` 
   
  Para más información, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show).

También puede usar [Azure Resource Explorer (versión preliminar)](https://resources.azure.com) o los [SDK de Azure](https://azure.microsoft.com/downloads/) para consultar el modelo de un conjunto de escalado.

La presentación exacta de la salida depende de las opciones proporcionadas para el comando. Aquí se muestra un ejemplo de la salida de la CLI de Azure:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
  .
  .
  .
}
```

Como puede ver, estas propiedades se aplican al conjunto de escalado como un todo.



### <a name="scale-set-instance-view"></a>Vista de la instancia del conjunto de escalado

Un conjunto de escalado tiene también una vista de instancia que captura el estado de *tiempo de ejecución* actual del conjunto de escalado completo. Para consultar la vista de instancia de un conjunto de escalado, puede usar:

* API REST: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version={apiVersion}` 
   
  Para más información, consulte la [documentación de la API de REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview).

* PowerShell: 

  `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceView` 
  
  Para más información, consulte la [documentación de PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss).

* CLI de Azure: 

  `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName}` 
   
  Para más información, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view).

También puede usar [Azure Resource Explorer (versión preliminar)](https://resources.azure.com) o los [SDK de Azure](https://azure.microsoft.com/downloads/) para consultar la instancia de un conjunto de escalado.

La presentación exacta de la salida depende de las opciones proporcionadas para el comando. Aquí se muestra un ejemplo de la salida de la CLI de Azure:

```
$ az vmss get-instance-view -g {resourceGroupName} -n {virtualMachineScaleSetName}
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
  .
  .
  .
}
```

Como se puede ver, estas propiedades ofrecen un resumen del estado de tiempo de ejecución actual de las máquinas virtuales en el conjunto de escalado. El resumen incluye el estado de las extensiones aplicadas al conjunto de escalado (que aquí se omite para mayor brevedad).



### <a name="scale-set-vm-model-view"></a>Vista del modelo de máquina virtual del conjunto de escalado

Lo mismo que un conjunto de escalado tiene una vista de modelo, cada máquina virtual del conjunto de escalado tiene su propia vista de modelo. Para consultar la vista de modelo de un conjunto de escalado, puede usar:

* API REST: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}?api-version={apiVersion}` 
  
  Para más información, consulte la [documentación de la API de REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/get).

* PowerShell: 

  `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` 
  
  Para más información, consulte la [documentación de PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm).

* CLI de Azure: 

  `az vmss show -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` 
  
  Para más información, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show).

También puede usar [Azure Resource Explorer (versión preliminar)](https://resources.azure.com) o los [SDK de Azure](https://azure.microsoft.com/downloads/) para consultar el modelo de una máquina virtual de un conjunto de escalado.

La presentación exacta de la salida depende de las opciones proporcionadas para el comando. Aquí se muestra un ejemplo de la salida de la CLI de Azure:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
  .
  .
  .
}
```

Como puede ver, estas propiedades describen la configuración de la máquina virtual propiamente dicha, no la configuración del conjunto de escalado como un todo. Por ejemplo, el modelo de conjunto de escalado tiene la propiedad `overprovision`, mientras que el modelo de una máquina virtual de un conjunto de escalado no. Esto se debe a que el aprovisionamiento en exceso es una propiedad del conjunto de escalado completo, pero no de las máquinas virtuales individuales del conjunto de escalado (para más información acerca del aprovisionamiento en exceso, consulte [Consideraciones de diseño para conjuntos de escalado](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning)).



### <a name="scale-set-vm-instance-view"></a>Vista de la instancia de máquina virtual del conjunto de escalado

Lo mismo que un conjunto de escalado tiene una vista de instancia, cada máquina virtual del conjunto de escalado tiene su propia vista de instancia. Para consultar la vista de instancia de un conjunto de escalado, puede usar:

* API REST: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/instanceView?api-version={apiVersion}` 
 
  Para más información, consulte la [documentación de la API de REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/getinstanceview).

* PowerShell: 

  `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -InstanceView` 
  
  Para más información, consulte la [documentación de PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm).

* CLI de Azure: 

  `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` 
  
  Para más información, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view).

También puede usar [Azure Resource Explorer (versión preliminar)](https://resources.azure.com) o los [SDK de Azure](https://azure.microsoft.com/downloads/) para consultar la vista de la instancia de una máquina virtual de un conjunto de escalado.

La presentación exacta de la salida depende de las opciones proporcionadas para el comando. Aquí se muestra un ejemplo de la salida de la CLI de Azure:

```
$ az vmss get-instance-view -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
  .
  .
  .
}
```

Como se puede ver, estas propiedades describen el estado de tiempo de ejecución actual de la propia máquina virtual. El estado incluye las extensiones aplicadas al conjunto de escalado (que aquí se omite para mayor brevedad).




## <a name="techniques-for-updating-global-scale-set-properties"></a>Técnicas para la actualización de las propiedades globales del conjunto de escalado

Para actualizar una propiedad global del conjunto de escalado, debe actualizar la propiedad en el modelo de conjunto de escalado. Puede usar para ello:

* API REST: 

  `PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` 
  
  Para más información, consulte la [documentación de la API de REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

  También puede implementar una plantilla de Azure Resource Manager mediante las propiedades de la API REST para actualizar las propiedades globales del conjunto de escalado.

* PowerShell: 

  `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -VirtualMachineScaleSet {scaleSetConfigPowershellObject}` 
  
  Para más información, consulte la [documentación de PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss).

* CLI de Azure:

  * Para modificar una propiedad: `az vmss update --set {propertyPath}={value}`. 
  
  * Para añadir un objeto a una propiedad de la lista de un conjunto de escalado: `az vmss update --add {propertyPath} {JSONObjectToAdd}`. 
  
  * Para quitar un objeto de una propiedad de la lista de un conjunto de escalado: `az vmss update --remove {propertyPath} {indexToRemove}`. 
  
  Para más información, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update). 
  
  O bien, si ha implementado previamente el conjunto de escalado mediante el comando `az vmss create`, puede ejecutar de nuevo el comando `az vmss create` para actualizar el conjunto de escalado. Para ello, asegúrese de que todas las propiedades del comando `az vmss create` son las mismas que antes, excepto las propiedades que desea modificar.



También puede usar [Azure Resource Explorer (versión preliminar)](https://resources.azure.com) o los [SDK de Azure](https://azure.microsoft.com/downloads/) para actualizar el modelo de un conjunto de escalado.

Una vez que se actualiza el modelo de conjunto de escalado, la nueva configuración se aplica a las nuevas máquinas virtuales creadas en el conjunto de escalado. Sin embargo, los modelos de las máquinas virtuales existentes del conjunto de escalado se deben seguir actualizando con el modelo de conjunto de escalado general más reciente. En el modelo de cada máquina virtual, una propiedad booleana llamada `latestModelApplied` indica si la máquina virtual está o no actualizada con el modelo de conjunto de escalado general más reciente (`true` significa que sí lo está).




## <a name="techniques-for-bringing-vms-up-to-date-with-the-latest-scale-set-model"></a>Técnicas de actualización de las máquinas virtuales con el modelo de conjunto de escalado más reciente

Los conjuntos de escalado tienen una *directiva de actualización* que determina cómo se actualizan las máquinas virtuales con el modelo de conjunto de escalado más reciente. Los tres modos de la directiva de actualización son:

- **Automático**: en este modo, el conjunto de escalado no ofrece ninguna garantía sobre el orden en el que se desactivan las máquinas virtuales. El conjunto de escalado puede desactivarlas todas al mismo tiempo. 
- **Gradual**: en este modo, el conjunto de escalado implementa la actualización por lotes con un tiempo de pausa opcional entre ellos.
- **Manual**: en este modo, cuando se actualiza el modelo de conjunto de escalado, no ocurre nada en las máquinas virtuales existentes. Para actualizar las máquinas virtuales existentes, debe hacerlo manualmente una a una. Puede realizar esta actualización manual mediante:

  - API REST: 
  
    `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/manualupgrade?api-version={apiVersion}` 
    
    Para más información, consulte la [documentación de la API de REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/updateinstances).

  - PowerShell: 
  
    `Update-AzureRmVmssInstance -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` 
    
    Para más información, consulte la [documentación de PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance).

  - CLI de Azure: 
  
    `az vmss update-instances -g {resourceGroupName} -n {vmScaleSetName} --instance-ids {instanceIds}` 
    
    Para más información, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update_instances).

  También puede usar los [SDK de Azure](https://azure.microsoft.com/downloads/) para actualizar manualmente una máquina virtual de un conjunto de escalado.

>[!NOTE]
> Los clústeres de Azure Service Fabric solo pueden usar el modo automático, pero la actualización se administra de forma diferente. Para más información sobre las actualizaciones de Service Fabric, consulte la [documentación de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade).

Un tipo de modificación en las propiedades globales del conjunto de escalado no sigue la directiva de actualización: cambios en el perfil del sistema operativo del conjunto de escalado (por ejemplo, el nombre de usuario y la contraseña de administrador). Estas propiedades solo se pueden modificar en la versión API del 1 de diciembre de 2017 o posterior. Estos cambios solo se aplican a las máquinas virtuales creadas después del cambio en el modelo de conjunto de escalado. Para actualizar las máquinas virtuales existentes, debe restablecer la imagen inicial de cada una de ellas. La imagen inicial de una máquina virtual se restablece mediante:

* API REST: 

  `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` 
  
  Para más información, consulte la [documentación de la API de REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage).

* PowerShell: 

  `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` 
  
  Para más información, consulte la [documentación de PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm).

* CLI de Azure: 

  `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` 
  
  Para más información, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage).

También puede usar los [Azure SDK](https://azure.microsoft.com/downloads/) para restablecer la imagen inicial de una máquina virtual de un conjunto de escalado.




## <a name="properties-with-restrictions-on-modification"></a>Propiedades con restricciones en la modificación

### <a name="create-time-properties"></a>Propiedades de tiempo de creación

Algunas propiedades solo pueden establecerse al crear inicialmente el conjunto de escalado. Estas propiedades incluyen:

- Zones
- Image reference publisher
- Image reference offer

### <a name="properties-that-can-be-changed-based-on-the-current-value-only"></a>Propiedades que solo se pueden cambiar en función del valor actual

Algunas propiedades pueden cambiarse, con excepciones, dependiendo del valor actual. Estas propiedades incluyen:

- `singlePlacementGroup`: si `singlePlacementGroup` es true, se puede cambiar a false. Sin embargo, si `singlePlacementGroup` es false, *no* se puede cambiar a true.
- `subnet`: la subred de un conjunto de escalado se puede modificar siempre y cuando la subred original y la nueva subred estén en la misma red virtual.

### <a name="properties-that-require-deallocation-to-change"></a>Propiedades que requieren desasignación para poder cambiar

Algunas propiedades solo pueden cambiar a ciertos valores si se desasignan las máquinas virtuales del conjunto de escalado. Estas propiedades incluyen:

- `sku name`: si el nombre de la nueva SKU de máquina virtual no se admite en el hardware en el que se encuentra actualmente el conjunto de escalado, debe desasignar las máquinas virtuales del conjunto de escalado antes de modificar `sku name`. Para más información sobre el cambio de tamaño de las máquinas virtuales, consulte [esta entrada de blog de Azure](https://azure.microsoft.com/blog/resize-virtual-machines/).


## <a name="vm-specific-updates"></a>Actualizaciones específicas de la máquina virtual

Algunas modificaciones pueden aplicarse a máquinas virtuales específicas, y no a las propiedades globales del conjunto de escalado. Actualmente, la única actualización específica de la máquina virtual que se admite es asociar/desasociar discos de datos en las máquinas virtuales del conjunto de escalado. Esta característica se encuentra en su versión preliminar. Para más información, consulte la [documentación de versión preliminar](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).

## <a name="scenarios"></a>Escenarios

### <a name="application-updates"></a>Actualizaciones de aplicaciones

Si una aplicación se implementa en un conjunto de escalado mediante extensiones, al actualizar la configuración de extensiones la aplicación se actualiza en consonancia con la directiva de actualización. Por ejemplo, si tiene una nueva versión de un script que se va a ejecutar en una extensión de script personalizada, podría actualizar la propiedad `fileUris` para que apunte al nuevo script. 

En algunos casos, es posible que desee forzar una actualización incluso si no se ha modificado la configuración de la extensión (por ejemplo, si ha actualizado el script sin cambiar el URI del script). En estos casos, puede modificar la propiedad `forceUpdateTag` para forzar una actualización. La plataforma Azure no interpreta esta propiedad, así que el hecho de cambiar su valor no afecta al modo en que se ejecuta la extensión. Solo fuerza a que la extensión se vuelva a ejecutar. 

Para más información sobre `forceUpdateTag`, consulte la [documentación de API REST de las extensiones](https://docs.microsoft.com/rest/api/compute/virtualmachineextensions/createorupdate).

También es habitual que las aplicaciones se implementen mediante una imagen personalizada. Este escenario se explica en la siguiente sección.

### <a name="os-updates"></a>Actualizaciones del sistema operativo

Si usa imágenes de plataforma, puede actualizar las imágenes mediante la modificación de `imageReference`. Para más información, consulte la [documentación de la API de REST](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate).

>[!NOTE]
> Con imágenes de plataforma, es habitual especificar la versión más reciente de la referencia de imagen. Esto significa que durante la creación, el escalado horizontal y el restablecimiento de la imagen inicial de un conjunto de escalado, las máquinas virtuales se crean con la versión más reciente disponible. Sin embargo, *no* significa que la imagen del sistema operativo se actualice automáticamente con el tiempo a medida que se lanzan nuevas versiones de imágenes. Se trata de una característica aparte, actualmente en versión preliminar. Para más información, consulte [Actualizaciones automáticas del sistema operativo](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

Si usa imágenes personalizadas, puede actualizar las imágenes mediante la actualización del identificador de `imageReference`. Para más información, consulte la [documentación de la API de REST](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="examples"></a>Ejemplos

### <a name="update-the-os-image-for-your-scale-set"></a>Actualización de la imagen del sistema operativo en el conjunto de escalado

Supongamos que tiene un conjunto de escalado que ejecuta una versión antigua de Ubuntu LTS 16.04. Desea actualizar a una versión más reciente de Ubuntu LTS 16.04 (por ejemplo, la versión 16.04.201801090). La propiedad de versión de referencia de imagen no es parte de una lista, por lo que puede modificar directamente estas propiedades con estos comandos:

* PowerShell: 

  `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -ImageReferenceVersion 16.04.201801090`

* CLI de Azure: 

  `az vmss update -g {resourceGroupName} -n {vmScaleSetName} --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090`


### <a name="update-the-load-balancer-for-your-scale-set"></a>Actualización del equilibrador de carga en el conjunto de escalado

Supongamos que tiene un conjunto de escalado con una instancia de Azure Load Balancer y quiere reemplazar el equilibrador de carga por una instancia de Azure Application Gateway. Las propiedades de la instancias de Azure Load Balancer y Azure Application Gateway de un conjunto de escalado son parte de una lista. Por lo tanto, puede usar los comandos para quitar y añadir elementos de lista en lugar de modificar las propiedades directamente.

PowerShell:
```
# Get the current model of the scale set and store it in a local PowerShell object named $vmss
> $vmss=Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName}

# Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
> $ipconf = New-AzureRmVmssIPConfig myNic -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name

# Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure load balancer) with the new IP configuration
> $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf

# Update the model of the scale set with the new configuration in the local PowerShell object
> Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName} -virtualMachineScaleSet $vmss

```

CLI de Azure:
```
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0 # Remove the load balancer back-end pool from the scale set model
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0 # Remove the load balancer back-end pool from the scale set model; only necessary if you have NAT pools configured on the scale set
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}' # Add the application gateway back-end pool to the scale set model
```

>[!NOTE]
> Estos comandos dan por hecho que solo hay una configuración IP y un equilibrador de carga en el conjunto de escalado. Si hay varios, es posible que deba usar un índice de lista distinto de 0.