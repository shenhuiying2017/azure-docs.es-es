---
title: Conjuntos de escalado de máquinas virtuales de Azure conectadas a discos de datos | Microsoft Docs
description: Aprenda a usar discos de datos conectados a conjuntos de escalado de máquinas virtuales
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: negat
ms.openlocfilehash: 4dd13f1feedf53255daa351bd087845ec5cc845a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187901"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Conjuntos de escalado de máquinas virtuales de Azure y discos de datos conectados
Para expandir el almacenamiento disponible, los [conjuntos de escalado de máquinas virtuales](/azure/virtual-machine-scale-sets/) de Azure admiten instancias de máquina virtual con discos de datos asociados. Puede asociar discos de datos cuando se crea el conjunto de escalado, o a un conjunto de escalado existente.

> [!NOTE]
>  Cuando se crea un conjunto de escalado con discos de datos asociados, debe montar y dar formato a los discos desde una máquina virtual para usarlos (al igual que sucede con las máquinas virtuales de Azure independientes). Una manera práctica de llevar cabo este proceso es usar una extensión de script personalizado que llama a un script para crear una partición y dar formato a todos los discos de datos en una máquina virtual. Para ver ejemplos de ello, consulte [CLI de Azure 2.0](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Creación y administración de discos de un conjunto de escalado
Para más información sobre cómo crear un conjunto de escalado con discos de datos asociados, prepararlos y aplicarles formato, o agregarlos y quitarlos, consulte uno de los siguientes tutoriales:

- [CLI de Azure 2.0](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

En el resto de este artículo se describen casos de uso específicos, como clústeres de Service Fabric que requieren discos de datos, o la asociación de discos de datos existentes con contenido a un conjunto de escalado.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Creación de un clúster de Service Fabric con discos de datos conectados
Cada [tipo de nodo](../service-fabric/service-fabric-cluster-nodetypes.md) de un clúster de [Service Fabric](/azure/service-fabric) en ejecución en Azure se encuentra respaldado por un conjunto de escalado de máquinas virtuales.  Mediante una plantilla de Azure Resource Manager, puede asociar discos de datos a los conjuntos de escalado que componen el clúster de Service Fabric. Puede usar una [plantilla existente](https://github.com/Azure-Samples/service-fabric-cluster-templates) como punto de partida. En la plantilla, incluya una sección _dataDisks_ en el atributo _storageProfile_ de los recursos _Microsoft.Compute/virtualMachineScaleSets_ e implemente la plantilla. En el ejemplo siguiente se conecta un disco de datos de 128 GB:

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

Puede crear particiones, formatear y montar los discos de datos automáticamente al implementar el clúster.  Añada una extensión de script personalizada al atributo _extensionProfile_ de _virtualMachineProfile_ de los conjuntos de escalado.

Para preparar automáticamente los discos de datos en un clúster de Windows, añada lo siguiente:

```json
{
    "name": "customScript",    
    "properties": {    
        "publisher": "Microsoft.Compute",    
        "type": "CustomScriptExtension",    
        "typeHandlerVersion": "1.8",    
        "autoUpgradeMinorVersion": true,    
        "settings": {    
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Para preparar automáticamente los discos de datos en un clúster de Linux, añada lo siguiente:
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existing-scale-set"></a>Adición de discos de datos previamente rellenados a un conjunto de escalado existente
Los discos de datos especificados en el modelo de conjunto de escalado siempre están vacíos. Sin embargo, puede asociar un disco de datos existente a una máquina virtual determinada de un conjunto de escalado. Esta característica está en versión preliminar y puede encontrar ejemplos en [GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk). Si quiere propagar los datos entre todas las máquinas virtuales del conjunto de escalado, puede duplicar el disco de datos y asociarlo a cada una de las máquinas virtuales del conjunto de escalado; también puede crear una imagen personalizada que contenga los datos y aprovisionar el conjunto de escalado a partir de ella, o usar Azure Files o una oferta de almacenamiento de datos similar.


## <a name="additional-notes"></a>Notas adicionales
Hay compatibilidad disponible con Azure Managed Disks y con discos de datos asociados al conjunto de escalado en la versión de API [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) o posterior de Microsoft.Compute API.

La compatibilidad con Azure Portal para discos de datos conectados en los conjuntos de escalado está limitada inicialmente. Dependiendo de los requisitos, puede usar plantillas de Azure, CLI, PowerShell, SDK y API de REST para administrar los discos conectados.


