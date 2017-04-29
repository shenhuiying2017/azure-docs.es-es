---
title: "Evento de finalización de cambio de tamaño del grupo: Azure | Microsoft Docs"
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: dfee89e3-510f-41a0-ace7-737527f40d20
caps.latest.revision: 4
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 623b6cae00ee0f3b0c5073b1a8bee68d2bf17b72
ms.lasthandoff: 04/13/2017

---
# <a name="pool-resize-complete-event"></a>Evento de finalización de cambio de tamaño del grupo
Cuerpo del registro del evento de finalización de cambio de tamaño del grupo

## <a name="remarks"></a>Comentarios
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
