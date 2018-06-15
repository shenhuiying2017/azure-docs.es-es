---
title: Creación de un conjunto de escalado de Azure que usa máquinas virtuales de prioridad baja (versión preliminar) | Microsoft Docs
description: Aprenda a crear conjuntos de escalado de máquinas virtuales de Azure que usan máquinas virtuales de prioridad baja para ahorrar en los costos.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mmccrory
manager: rajraj
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: memccror
ms.openlocfilehash: 5c0726ea0da288d5306e28b101e4d3b59605b443
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894914"
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>Máquinas virtuales de prioridad baja en conjuntos de escalado (versión preliminar)

El uso de máquinas virtuales de prioridad baja en conjuntos de escalado le permite aprovechar las ventajas de nuestra capacidad no utilizada con un importante ahorro en los costos. En cualquier momento en que Azure necesite recuperar la capacidad, la infraestructura de Azure expulsará máquinas virtuales de prioridad baja. Por lo tanto, las máquinas virtuales de prioridad baja son excelentes para cargas de trabajo que puedan administrar interrupciones como trabajos de procesamiento por lotes, entornos de desarrollo y pruebas, cargas de trabajo de proceso grandes y mucho más.

La cantidad de capacidad no utilizada disponible puede variar en función del tamaño, la región, la hora del día, etc. Al implementar máquinas virtuales de prioridad baja en conjuntos de escalado, Azure asigna máquinas virtuales si hay capacidad disponible, pero no hay un Acuerdo de Nivel de Servicio para estas máquinas virtuales. Un conjunto de escalado de prioridad baja se implementa en un único dominio de error y no ofrece ninguna garantía de alta disponibilidad.

## <a name="eviction-policy"></a>Directiva de expulsión

Al crear conjuntos de escalado de prioridad baja, puede establecer la directiva de expulsión en *Desasignar* (valor predeterminado) o *Eliminar*. 

La directiva *Desasignar* mueve las VM expulsadas al estado stopped-deallocated, lo que le permite volver a implementar instancias expulsadas. Sin embargo, no hay ninguna garantía de que la asignación se realizará correctamente. Las máquinas virtuales desasignadas se siguen teniendo en cuenta en la cuota de instancias del conjunto de escalado y se le cobra por los discos subyacentes. 

Si quiere que las máquinas virtuales del conjunto de escalado de prioridad baja se eliminen al expulsarse, puede establecer la directiva de expulsión en *Eliminar*. Con la directiva de expulsión establecida para eliminar, puede crear nuevas máquinas virtuales mediante el aumento de la propiedad de recuento de instancias del conjunto de escalado. Las máquinas virtuales expulsadas se eliminan junto con sus discos subyacentes y, por tanto, no se le cobra el almacenamiento. También puede usar la característica de escalado automático de los conjuntos de escalado para probar y compensar automáticamente las máquinas virtuales expulsadas; sin embargo, no hay ninguna garantía de que la asignación se realice correctamente. Se recomienda usar únicamente la característica de escalado automático en conjuntos de escalado de prioridad baja cuando establezca la directiva de expulsión para eliminar, con el fin de evitar el costo de los discos y alcanzar los límites de cuota. 

> [!NOTE]
> Durante la versión preliminar, podrá establecer la directiva de expulsión mediante [Azure Portal](#use-the-azure-portal) y [plantillas de Azure Resource Manager](#use-azure-resource-manager-templates). 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>Implementación de máquinas virtuales de prioridad baja en conjuntos de escalado

Para implementar máquinas virtuales de prioridad baja en conjuntos de escalado, puede establecer la nueva marca *Prioridad* en *Baja*. Todas las máquinas virtuales del conjunto de escalado se establecerán en prioridad baja. Para crear un conjunto de escalado con máquinas virtuales de prioridad baja, use uno de los métodos siguientes:
- [Azure Portal](#use-the-azure-portal)
- [CLI de Azure 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Plantillas del Administrador de recursos de Azure](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Uso de Azure Portal

El proceso para crear un conjunto de escalado que use VM de prioridad baja es igual al que se detalla en el [artículo de introducción](quick-create-portal.md). Cuando va a implementar un conjunto de escalado, se puede establecer la marca de prioridad baja y la directiva de expulsión: ![Crear un conjunto de escalado con máquinas virtuales de prioridad baja](media/virtual-machine-scale-sets-use-low-priority/vmss-low-priority-portal.png)

## <a name="use-the-azure-cli-20"></a>Uso de la CLI de Azure 2.0

El proceso para crear un conjunto de escalado con máquinas virtuales de prioridad baja es igual al que se detalla en el [artículo de introducción](quick-create-cli.md). Basta con que agregue el parámetro "--Priority" a la llamada a la CLI y lo establezca en *Baja*, como se muestra en el ejemplo siguiente:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Low
```

## <a name="use-azure-powershell"></a>Uso de Azure PowerShell

El proceso para crear un conjunto de escalado con máquinas virtuales de prioridad baja es igual al que se detalla en el [artículo de introducción](quick-create-powershell.md).
Basta con que agregue el parámetro "--Priority" a [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) y lo establezca en *Baja*, como se muestra en el ejemplo siguiente:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>Utilización de plantillas de Azure Resource Manager

El proceso para crear un conjunto de escalado que usa máquinas virtuales de prioridad baja es igual al que se detalla en el artículo de introducción para [Linux](quick-create-template-linux.md) o [Windows](quick-create-template-windows.md). Agregue la propiedad "priority" al tipo de recurso *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile* en la plantilla y especifique el valor *Baja*. Asegúrese de usar la versión de API *2018-03-01* o superior. 

Para establecer la directiva de expulsión en eliminación, agregue el parámetro "evictionPolicy" y establézcalo en *Eliminar*.

En el ejemplo siguiente se crea un conjunto de escalado de Linux de prioridad baja llamado *myScaleSet* en *Centro de EE. UU.*, que *elimina* las máquinas virtuales del conjunto de escalado tras su expulsión:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2018-03-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Low",
       "evictionPolicy": "delete",
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
## <a name="faq"></a>Preguntas más frecuentes

### <a name="can-i-convert-existing-scale-sets-to-low-priority-scale-sets"></a>¿Puedo convertir los conjuntos de escalado existentes en conjuntos de escalado de prioridad baja?
No, establecer la marca de prioridad baja solo se admite durante la creación.

### <a name="can-i-create-a-scale-set-with-both-regular-vms-and-low-priority-vms"></a>¿Puedo crear un conjunto de escalado con VM normales y VM de prioridad baja?
No, un conjunto de escalado no admite más de un tipo de prioridad.

### <a name="how-is-quota-managed-for-low-priority-vms"></a>¿Cómo se administra la cuota para las VM de prioridad baja?
Las VM de prioridad baja y las VM normales comparten el mismo grupo de cuota. 

### <a name="can-i-use-autoscale-with-low-priority-scale-sets"></a>¿Puedo usar el escalado automático con conjuntos de escalado de prioridad baja?
Sí, puede establecer reglas de escalado automático en el conjunto de escalado de prioridad baja. Si se expulsan las VM, el escalado automático puede intentar crear nuevas VM de prioridad baja. Recuerde que, aun así, no se garantiza esta capacidad. 

### <a name="does-autoscale-work-with-both-eviction-policies-deallocate-and-delete"></a>¿Funciona el escalado automático con las dos directivas de expulsión (desasignar y eliminar)?
Se recomienda establecer la directiva de expulsión en Eliminar cuando se usa el escalado automático. Esto es porque las instancias desasignadas se cuentan para el número de capacidad en el conjunto de escalado. Cuando se usa el escalado automático, es probable que alcance el número de instancias de destino rápidamente debido a las instancias expulsadas desasignadas. 

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha creado un conjunto de escalado con máquinas virtuales de prioridad baja, intente implementar nuestra [plantilla de escalado automático mediante prioridad baja](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Consulte la [página de precios del conjunto de escalado de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) para ver información de precios.