---
title: "Conjuntos de escalado de máquinas virtuales de Azure conectadas a discos de datos | Microsoft Docs"
description: "Aprenda a usar discos de datos conectados a conjuntos de escalado de máquinas virtuales"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: negat
ms.openlocfilehash: 355865b963c313097f7f5900007f341dba92bf67
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Conjuntos de escalado de máquinas virtuales de Azure y discos de datos conectados
Los [conjuntos de escalado de máquinas virtuales](/azure/virtual-machine-scale-sets/) de Azure ahora son compatibles con máquinas virtuales con discos de datos conectados. Los discos de datos se pueden definir en el perfil de almacenamiento para los conjuntos de escalado creados con Azure Managed Disks. Anteriormente, las únicas opciones de almacenamiento disponibles directamente conectadas a las máquinas virtuales en los conjuntos de escalado eran la unidad de sistema operativo y unidades de disco temporales.

> [!NOTE]
>  Cuando se crea un conjunto de escalado con discos de datos conectados definidos, deberá montar y dar formato a los discos desde una máquina virtual para usarlos (al igual que para las máquinas virtuales de Azure independientes). Una manera cómoda de llevara cabo este proceso es utilizar una extensión de script personalizada que llama a un script estándar para crear una partición y dar formato a todos los discos de datos en una máquina virtual.

## <a name="create-a-scale-set-with-attached-data-disks"></a>Creación de un conjunto de escalado con discos de datos conectados
Una manera sencilla de crear un conjunto de escalado con discos conectados es usar el comando [az vmss create](/cli/azure/vmss#create). En el ejemplo siguiente se crea un grupo de recursos de Azure y un conjunto de escalado de máquina virtual de 10 máquinas virtuales Ubuntu, cada una con dos discos de datos asociados, de 50 GB y 100 GB, respectivamente.

```bash
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```

Los valores predeterminados del comando [az vmss create](/cli/azure/vmss#create) incluyen determinados valores de configuración que se aplicarán si no los especifica. Para ver que las opciones disponibles que puede reemplazar, pruebe lo siguiente:

```bash
az vmss create --help
```

Otra manera de crear un conjunto de escalado con discos de datos conectados es definir un conjunto de escalado en una plantilla de Azure Resource Manager, incluir una sección _dataDisks_ en la clase _storageProfile_ e implementar la plantilla. El disco de 50 GB y el de 100 GB en el ejemplo anterior se definirán como se muestra en el siguiente ejemplo de plantilla:

```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    }
]
```

Aquí [https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data](https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data) puede ver un ejemplo completo y listo para su implementación de una plantilla de conjunto de escalado con un disco conectado definido.

## <a name="adding-a-data-disk-to-an-existing-scale-set"></a>Adición de un disco de datos a un conjunto de escalado existente
> [!NOTE]
>  Solo puede conectar discos de datos a un conjunto de escalado que se haya creado con [Azure Managed Disks](./virtual-machine-scale-sets-managed-disks.md).

Puede agregar un disco de datos a un conjunto de escalado de máquina virtual con el comando _az vmss disk attach_ de la CLI de Azure. Asegúrese de que especifica un valor de LUN que no se esté usando ya. En el siguiente ejemplo de la CLI, se agrega una unidad de 50 GB a LUN 3:

```bash
az vmss disk attach -g dsktest -n dskvmss --size-gb 50 --lun 3
```

En el siguiente ejemplo de PowerShell, se agrega una unidad de 50 GB a LUN 3:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myvmssrg -VMScaleSetName myvmss
$vmss = Add-AzureRmVmssDataDisk -VirtualMachineScaleSet $vmss -Lun 3 -Caching 'ReadWrite' -CreateOption Empty -DiskSizeGB 50 -StorageAccountType StandardLRS
Update-AzureRmVmss -ResourceGroupName myvmssrg -Name myvmss -VirtualMachineScaleSet $vmss
```

> [!NOTE]
> Los distintos tamaños de las máquinas virtuales tienen distintos límites en la cantidad de unidades conectadas que admiten. Compruebe las [características de tamaño de la máquina virtual](../virtual-machines/windows/sizes.md) antes de agregar un nuevo disco.

También puede agregar un disco mediante la adición de una nueva entrada a la propiedad _dataDisks_ en la clase _storageProfile_ de una definición del conjunto de escalado y aplicar el cambio. Para probarlo, busque la definición del conjunto de escalado existente en el [Explorador de recursos de Azure](https://resources.azure.com/). Seleccione _Editar_ y agregue un nuevo disco a la lista de discos de datos como se muestra en el ejemplo siguiente:

```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    },
    {
    "lun": 3,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 20
    }          
]
```

A continuación, seleccione _PUT_ para aplicar los cambios a su conjunto de escalado. Este ejemplo podría funcionar siempre y cuando se utilice un tamaño de máquina virtual que admite más de dos discos de datos conectados.

> [!NOTE]
> Cuando se realiza un cambio en una definición de conjunto de escalado, como agregar o quitar un disco de datos, se aplica a todas las máquinas virtuales recién creadas, pero solo se aplica a las máquinas virtuales existentes si la propiedad _upgradePolicy_ se establece en "Automático". Si se establece en "Manual", hay que aplicar manualmente el nuevo modelo a las máquinas virtuales existentes. Puede hacerlo en el portal, con el comando _Update-AzureRmVmssInstance_ de PowerShell, o mediante el comando _az vmss update-instances_ de la CLI.

## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>Adición de discos de datos previamente rellenados a un conjunto de escalado existente 
> Cuando se agregan discos a un modelo de conjunto de escalado existente, los discos siempre se crean vacíos. Esto es así por diseño. Este escenario también incluye nuevas instancias creadas por el conjunto de escalado. Este comportamiento se debe a que la definición de conjunto de escalado tiene un disco de datos vacío. Para crear unidades de datos rellenadas previamente para un modelo de conjunto de escalado existente, puede elegir entre las dos opciones siguientes:

* Copiar datos de la máquina virtual de instancia 0 en discos de datos de las otras máquinas virtuales mediante la ejecución de un script personalizado.
* Crear una imagen administrada con el disco de SO más un disco de datos (con los datos necesarios) y crear un nuevo conjunto de escalado con la imagen. De esta forma cada nueva máquina creada tiene un disco de datos que se proporciona en la definición del conjunto de escalado. Dado que esta definición hace referencia a una imagen con un disco de datos que tiene datos personalizados, cada máquina virtual del conjunto de escalado tiene estos cambios.

> La forma de crear una imagen personalizada se puede encontrar aquí: [Captura de una imagen administrada de una máquina virtual generalizada en Azure](/azure/virtual-machines/windows/capture-image-resource/). 

> El usuario tiene que capturar la máquina virtual de instancia 0 que tenga los datos necesarios y luego usar ese disco duro virtual para la definición de la imagen.

## <a name="removing-a-data-disk-from-a-scale-set"></a>Supresión de un disco de datos de un conjunto de escalado
Puede quitar un disco de datos de un conjunto de escalado de la máquina virtual mediante el comando _az vmss disk detach_ de la CLI de Azure. Por ejemplo, el comando siguiente quita el disco definido en LUN 2:
```bash
az vmss disk detach -g dsktest -n dskvmss --lun 2
```  
De igual forma también puede quitar un disco de un conjunto de escalado al suprimir una entrada de la propiedad _dataDisks_ de la clase _storageProfile_ y aplicar el cambio. 

## <a name="additional-notes"></a>Notas adicionales
Hay compatibilidad disponible con Azure Managed Disks y con discos de datos asociados al conjunto de escalado en la versión de API [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) o posterior de Microsoft.Compute API.

En la implementación inicial de la compatibilidad con los discos conectados para los conjuntos de escalado, no se puede conectar ni desconectar discos de datos de máquinas virtuales individuales, o hacia ellas, en un conjunto de escalado.

La compatibilidad con Azure Portal para discos de datos conectados en los conjuntos de escalado está limitada inicialmente. Dependiendo de los requisitos, puede usar plantillas de Azure, CLI, PowerShell, SDK y API de REST para administrar los discos conectados.


