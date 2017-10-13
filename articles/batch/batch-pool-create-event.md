---
title: "Evento de creación de grupo de Azure Batch | Microsoft Docs"
description: "Referencia del evento de creación de grupo de Batch."
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: tamram
ms.openlocfilehash: 67edaa55d7ccd00d4aebb309f11bcf95486e87fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="pool-create-event"></a>Evento de creación del grupo

 Este evento se genera cuando se ha creado un grupo. El contenido del registro va a exponer información general acerca del grupo. Tenga en cuenta que si el tamaño de destino del grupo es mayor que 0 nodos de proceso, un evento de inicio de cambio de tamaño del grupo seguirá inmediatamente después de este evento.

 En el siguiente ejemplo, se muestra el cuerpo de un evento de creación de grupo para un grupo creado mediante la propiedad CloudServiceConfiguration.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Elemento|Tipo|Notas|
|-------------|----------|-----------|
|id|String|El identificador del grupo.|
|DisplayName|String|El nombre para mostrar del grupo.|
|vmSize|String|El tamaño de las máquinas virtuales que se usan en el grupo. Todas las máquinas virtuales de un grupo son del mismo tamaño. <br/><br/> Para obtener información sobre los tamaños disponibles de máquinas virtuales para los grupos de Cloud Services (grupos creados con cloudServiceConfiguration), consulte [Tamaños para Cloud Services](http://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). Batch admite todas las VM de Cloud Services excepto `ExtraSmall`.<br/><br/> Para obtener información sobre los tamaños de VM disponibles para grupos con imágenes del Virtual Machines Marketplace (grupos creados con virtualMachineConfiguration), consulte [Tamaños de máquinas virtuales](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) o [Tamaños de máquinas virtuales](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). Lote admite todos los tamaños de máquina virtual de Azure excepto `STANDARD_A0` y aquellos con almacenamiento premium (series `STANDARD_GS`, `STANDARD_DS` y `STANDARD_DSV2`).|
|[cloudServiceConfiguration](#bk_csconf)|Tipo complejo|La configuración del servicio en la nube para el grupo.|
|[virtualMachineConfiguration](#bk_vmconf)|Tipo complejo|La configuración de la máquina virtual para el grupo.|
|[networkConfiguration](#bk_netconf)|Tipo complejo|La configuración de red para el grupo.|
|resizeTimeout|Hora|El tiempo de espera para la asignación de nodos de proceso para el grupo especificado para la última operación de cambio de tamaño en el grupo.  (El tamaño inicial cuando se crea el grupo cuenta como un cambio de tamaño).|
|targetDedicated|Int32|El número de nodos de proceso solicitados para el grupo.|
|enableAutoScale|Booleano|Especifica si el tamaño del grupo se ajusta automáticamente con el tiempo.|
|enableInterNodeCommunication|Booleano|Especifica si el grupo está configurado para la comunicación directa entre nodos.|
|isAutoPool|Booleano|Especifica si el grupo se creó a través del mecanismo AutoPool de un trabajo.|
|maxTasksPerNode|Int32|El número máximo de tareas que pueden ejecutarse simultáneamente en un solo nodo de ejecución en el grupo.|
|vmFillType|String|Define cómo el servicio de Batch distribuye las tareas entre los nodos de proceso del grupo. Los valores válidos son Spread o Pack.|

###  <a name="bk_csconf"></a> cloudServiceConfiguration

|Nombre del elemento|Tipo|Notas|
|------------------|----------|-----------|
|osFamily|String|La familia del SO invitado de Azure para instalar en las máquinas virtuales en el grupo.<br /><br /> Los valores posibles son:<br /><br /> **2**: familia 2 de SO, que equivale a Windows Server 2008 R2 SP1.<br /><br /> **3**: familia 3 de SO, que equivale a Windows Server 2012.<br /><br /> **4**: familia 4 de SO, que equivale a Windows Server 2012 R2.<br /><br /> Para obtener más información, consulte [Versiones del SO invitado de Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|targetOSVersion|String|La versión del SO invitado de Azure para instalar en las máquinas virtuales en el grupo.<br /><br /> El valor predeterminado es **\***, que especifica la versión del sistema operativo más reciente para la familia especificada.<br /><br /> Para otros valores permitidos, consulte [Versiones del SO invitado de Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="bk_vmconf"></a> virtualMachineConfiguration

|Nombre del elemento|Tipo|Notas|
|------------------|----------|-----------|
|[imageReference](#bk_imgref)|Tipo complejo|Especifica información sobre la plataforma o imagen de Marketplace que se usará.|
|nodeAgentSKUId|String|El SKU agente del nodo de Batch proporcionado en el nodo de ejecución.|
|[windowsConfiguration](#bk_winconf)|Tipo complejo|Especifica la configuración del sistema operativo de Windows en la máquina virtual. Esta propiedad no se debe especificar si el imageReference hace referencia a una imagen del sistema operativo de Linux.|

###  <a name="bk_imgref"></a> imageReference

|Nombre del elemento|Tipo|Notas|
|------------------|----------|-----------|
|publisher|String|El publicador de la imagen.|
|offer|String|La oferta publicador de la imagen.|
|sku|String|El SKU de la imagen.|
|versión|String|La versión de la imagen.|

###  <a name="bk_winconf"></a> windowsConfiguration

|Nombre del elemento|Tipo|Notas|
|------------------|----------|-----------|
|enableAutomaticUpdates|Booleano|Indica si la máquina virtual está habilitada para las actualizaciones automáticas. Si esta propiedad no se especifica, se usa el valor predeterminado.|

###  <a name="bk_netconf"></a> networkConfiguration

|Nombre del elemento|Tipo|Notas|
|------------------|--------------|----------|
|subnetId|String|Especifica el identificador de recursos de la subred en la que se crean los nodos de proceso del grupo.|
