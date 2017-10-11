---
title: "Evento completo de cambio de tamaño de grupo de Azure Batch | Microsoft Docs"
description: "Referencia del evento completo de cambio de tamaño de grupo de Batch."
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
ms.openlocfilehash: 7072293d98526812cb42ce9c2f8e33bfcafaa149
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="pool-resize-complete-event"></a>Evento de finalización de cambio de tamaño del grupo

 Este evento se genera cuando finaliza o no se puede realizar el cambio de tamaño de un grupo.

 En el siguiente ejemplo, se muestra el cuerpo de un evento de cambio de tamaño del grupo para un grupo que aumentó su tamaño y se finalizó correctamente.

```
{
    "id": "p_1_0_01503750-252d-4e57-bd96-d6aa05601ad8",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 4,
    "targetDedicated": 4,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "result": "Success",
    "resizeError": "The operation succeeded"
}
```

|Elemento|Tipo|Notas|
|-------------|----------|-----------|
|id|String|El identificador del grupo.|
|nodeDeallocationOption|String|Especifica cuándo se pueden quitar los nodos del grupo, si disminuye el tamaño del grupo.<br /><br /> Los valores posibles son:<br /><br /> **requeue**: finalizar las tareas en ejecución y volver a ponerlas en cola. Las tareas volverán a ejecutarse cuando se habilite el trabajo. Elimine los nodos en cuanto finalicen las tareas.<br /><br /> **terminate**: finalizar las tareas en ejecución. Las tareas no se ejecutarán de nuevo. Elimine los nodos en cuanto finalicen las tareas.<br /><br /> **taskcompletion**: permita que finalicen las tareas actualmente en ejecución. No programe ninguna tarea nueva mientras espera. Elimine los nodos cuando se hayan completado todas las tareas.<br /><br /> **Retaineddata**: permite que finalicen las tareas actualmente en ejecución, luego espera que caduquen los períodos de retención de datos de todas las tareas. No programe ninguna tarea nueva mientras espera. Elimine los nodos cuando hayan caducado los períodos de retención de todas las tareas.<br /><br /> El valor predeterminado es requeue.<br /><br /> Si aumenta el tamaño del grupo, entonces el valor se establece en **invalid**.|
|currentDedicated|Int32|El número de nodos de proceso actualmente asignados al grupo.|
|targetDedicated|Int32|El número de nodos de proceso solicitados para el grupo.|
|enableAutoScale|Booleano|Especifica si el tamaño del grupo se ajusta automáticamente con el tiempo.|
|isAutoPool|Booleano|Especifica si se ha creado el grupo a través del mecanismo AutoPool de un trabajo.|
|startTime|DateTime|La hora en que se inició el cambio de tamaño del grupo.|
|endTime|DateTime|La hora en que finalizó el cambio de tamaño del grupo.|
|resultCode|String|El resultado del cambio de tamaño.|
|resultMessage|String|El error de cambio de tamaño incluye los detalles del resultado.<br /><br /> Si el cambio de tamaño finalizó sin problemas, esto indica que la operación se completó correctamente.|