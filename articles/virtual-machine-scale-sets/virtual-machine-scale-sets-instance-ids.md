---
title: "Identificadores de instancia de máquinas virtuales del conjunto de escalado de máquinas virtuales de Azure | Microsoft Docs"
description: "Identificadores de instancia de máquinas virtuales del conjunto de escalado de máquinas virtuales de Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: negat
ms.openlocfilehash: 3a43dc86f1fb53dfde4bce3938faaa30e18f5a6d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Identificadores de instancia de máquinas virtuales del conjunto de escalado de máquinas virtuales de Azure
En este artículo se describen los identificadores de instancia de los conjuntos de escalado y las distintas formas en que se muestran.

## <a name="scale-set-instance-ids"></a>Identificadores de instancia del conjunto de escalado

Cada máquina virtual de un conjunto de escala recibe un identificador de instancia que la identifica de forma única. Este identificador de instancia se usa en las API de conjunto de escalado para realizar operaciones en una máquina virtual determinada del conjunto de escalado. Por ejemplo, puede especificar un identificador de instancia específico para restablecer la imagen inicial cuando se usa la API de restablecimiento de imagen inicial:

API REST: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (para más información, consulte la [documentación de API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

Powershell: `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (para más información, consulte la [documentación de Powershell](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm))

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (para más información, consulte la [documentación de la CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage))

Para obtener la lista de identificadores de instancia, enumere todas las instancias de un conjunto de escalado:

API REST: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (para más información, consulte la [documentación de API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list))

Powershell: `Get-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (para más información, consulte la [documentación de Powershell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm))

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (para más información, consulte la [documentación de la CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_list_instances))

También puede usar [resources.azure.com](https://resources.azure.com) o los [Azure SDK](https://azure.microsoft.com/downloads/) para enumerar las máquinas virtuales de un conjunto de escalado.

La presentación exacta de la salida depende de las opciones proporcionadas para el comando. Aquí solo se muestra un ejemplo de la salida de la CLI:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
[
  {
    "instanceId": "85",
    "latestModelApplied": true,
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Como puede ver, la propiedad "instanceId" es simplemente un número decimal. Los identificadores de instancia se pueden volver a utilizar con nuevas instancias una vez que se eliminan las más antiguas.

>[!NOTE]
> No hay **ninguna garantía** en la forma en que se asignan los identificadores de instancia a las máquinas virtuales del conjunto de escalado. Puede parecer que aumentan de manera secuencial a veces, pero no siempre es el caso. No hay una forma específica en que los identificadores de instancia se asignan a las máquinas virtuales.

## <a name="scale-set-vm-names"></a>Nombres de máquinas virtuales del conjunto de escalado

En la salida del ejemplo anterior, hay también un nombre para la máquina virtual. Este nombre tiene el formato "{scale-set-name}_{instance-id}". Este nombre es el que se ve en Azure Portal al enumerar las instancias de un conjunto de escalado:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

La parte {instance-id} del nombre es el mismo número decimal que la propiedad "instanceId" descrita anteriormente.

## <a name="instance-metadata-vm-name"></a>Nombre de máquina virtual de metadatos de instancia

Si consulta los [metadatos de instancia](../virtual-machines/windows/instance-metadata-service.md) de la máquina virtual de un conjunto de escalado, verá un nombre en la salida:

```
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Este nombre es el mismo que el nombre descrito anteriormente.

## <a name="scale-set-vm-computer-name"></a>Nombre del equipo de máquina virtual del conjunto de escalado

A cada máquina virtual de un conjunto de escalado se le asigna también un nombre de equipo. Este nombre de equipo es el nombre de host de la máquina virtual en la [resolución de nombres DNS proporcionada por Azure dentro de la red virtual](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Este nombre de equipo tiene el formato "{computer-name-prefix}{base-36-instance-id}".

{base-36-instance-id} está en [base 36](https://en.wikipedia.org/wiki/Base36) y tiene siempre una longitud de seis dígitos. Si la representación de base 36 del número tiene menos de seis dígitos, {base-36-instance-id} se rellena con ceros hasta alcanzar la longitud de seis dígitos. Por ejemplo, una instancia con {computer-name-prefix} "nsgvmss" y un identificador de instancia 85 tendrán el nombre de equipo "nsgvmss00002D".

>[!NOTE]
> El prefijo del nombre de equipo es una propiedad del modelo de conjunto de escalado que se puede configurar, así que puede ser distinto del propio nombre del conjunto de escalado.