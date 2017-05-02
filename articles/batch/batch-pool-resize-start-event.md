---
title: "Evento de inicio de cambio de tamaño de grupo - Azure | Microsoft Docs"
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 8bd33e8b-6390-4a34-95dc-2e63d8bce941
caps.latest.revision: 6
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 5064d86063251aeed22688d8fac133c4be1a88f8
ms.lasthandoff: 04/13/2017

---
# <a name="pool-resize-start-event"></a>Evento de inicio de cambio de tamaño de grupo
Cuerpo del registro del evento de inicio de cambio de tamaño de grupo

## <a name="remarks"></a>Comentarios
 Este evento se genera se inicia el cambio de tamaño de un grupo. Puesto que el cambio de tamaño de grupo es un evento asincrónico, puede esperar que se genere un evento completo de cambio de tamaño de grupo cuando se haya completado la operación de cambio de tamaño.

 En el ejemplo siguiente se muestra el cuerpo de un evento de inicio de cambio de tamaño de grupo de 0 a 2 nodos con un cambio de tamaño manual.

```
{
    "poolId": "myPool1",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 0,
    "targetDedicated": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|Elemento|Tipo|Notas|
|-------------|----------|-----------|
|poolId|String|El identificador del grupo.|
|nodeDeallocationOption|String|Especifica cuándo se pueden quitar los nodos del grupo, si disminuye el tamaño del grupo.<br /><br /> Los valores posibles son:<br /><br /> **requeue**: finalizar las tareas en ejecución y volver a ponerlas en cola. Las tareas volverán a ejecutarse cuando se habilite el trabajo. Elimine los nodos en cuanto finalicen las tareas.<br /><br /> **terminate**: finalizar las tareas en ejecución. Las tareas no se ejecutarán de nuevo. Elimine los nodos en cuanto finalicen las tareas.<br /><br /> **taskcompletion**: permita que finalicen las tareas actualmente en ejecución. No programe ninguna tarea nueva mientras espera. Elimine los nodos cuando se hayan completado todas las tareas.<br /><br /> **Retaineddata**: permite que finalicen las tareas actualmente en ejecución, luego espera que caduquen los períodos de retención de datos de todas las tareas. No programe ninguna tarea nueva mientras espera. Elimine los nodos cuando hayan caducado los períodos de retención de todas las tareas.<br /><br /> El valor predeterminado es requeue.<br /><br /> Si aumenta el tamaño del grupo, entonces el valor se establece en **invalid**.|
|currentDedicated|Int32|El número de nodos de proceso actualmente asignados al grupo.|
|targetDedicated|Int32|El número de nodos de proceso solicitados para el grupo.|
|enableAutoScale|Booleano|Especifica si el tamaño del grupo se ajusta automáticamente con el tiempo.|
|isAutoPool|Booleano|Especifica si el grupo se creó a través del mecanismo AutoPool de un trabajo.|

