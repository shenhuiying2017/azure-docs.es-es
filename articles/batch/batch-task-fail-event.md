---
title: Evento de error en tareas - Azure | Microsoft Docs
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 8c16a533-1ac7-4b65-a84e-8eafb937b3d7
caps.latest.revision: 3
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 880fe1cfb0496311d7c386f25762c5d02a6c91f4
ms.lasthandoff: 04/13/2017

---
# <a name="task-fail-event"></a>Evento de error en tareas
Cuerpo del registro del evento de error en tareas

## <a name="remarks"></a>Comentarios
 Este evento se emite cuando una tarea se completa con error. Actualmente todos los códigos de salida distintos de cero se consideran errores. Este evento se emitirá *además de* un evento de tarea completada y se puede usar para detectar cuando una tarea presenta un error.


 En el ejemplo siguiente se muestra el cuerpo de un evento de error en tareas.

```
{
    "jobId": "job-0000000001",
    "id": "task-5",
    "taskType": "User",
    "systemTaskVersion": 0,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 1,
        "retryCount": 2,
        "requeueCount": 0
    }
}
```

|Nombre del elemento|Tipo|Notas|
|------------------|----------|-----------|
|jobId|String|Identificador del trabajo que contiene la tarea.|
|id|String|Identificador de la tarea.|
|taskType|String|Tipo de la tarea. Puede ser "JobManager", que indica que es una tarea del administrador de trabajos, o "User", que indica que no lo es. Este evento no se emite para tareas de preparación de trabajos, tareas de liberación de trabajo ni tareas de inicio.|
|systemTaskVersion|Int32|Se trata del contador interno de reintentos de una tarea. De manera interna, el servicio de Batch puede reintentar una tarea para tener en cuenta los problemas transitorios. Estos problemas pueden incluir errores internos de programación o intentos de recuperación a partir de nodos de proceso en estado no válido.|
|[nodeInfo](#nodeInfo)|Tipo complejo|Contiene información sobre el nodo de ejecución en que se ejecutó la tarea.|
|[multiInstanceSettings](#multiInstanceSettings)|Tipo complejo|Especifica que la tarea es una tarea de instancias múltiples que requiere varios nodos de proceso.  Consulte [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) para detalles.|
|[constraints](#constraints)|Tipo complejo|Restricciones de ejecución que se aplican a esta tarea.|
|[executionInfo](#executionInfo)|Tipo complejo|Contiene información sobre la ejecución de la tarea.|

###  <a name="nodeInfo"></a> nodeInfo

|Nombre del elemento|Tipo|Notas|
|------------------|----------|-----------|
|poolId|String|Identificador del grupo en que se ejecutó la tarea.|
|nodeId|String|Identificador del nodo en que se ejecutó la tarea.|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|Nombre del elemento|Tipo|Notas|
|------------------|----------|-----------|
|numberOfInstances|Int32|Número de nodos de proceso que requiere la tarea.|

###  <a name="constraints"></a> constraints

|Nombre del elemento|Tipo|Notas|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|Número máximo de veces que se puede reintentar la tarea. El servicio de Batch reintenta una tarea su el código de salida es distinto de cero.<br /><br /> Tenga en cuenta que este valor controla específicamente el número de reintentos. El servicio de Batch intentará una vez la tarea y podría reintentarla hasta alcanzar este límite. Por ejemplo, si el conteo de reintentos máximo es 3, Batch intenta una tarea hasta 4 veces (un intento inicial y 3 reintentos).<br /><br /> Si el conteo de intentos máximo es 0, el servicio de Batch no reintenta las tareas.<br /><br /> Si el conteo de intentos máximo es -1, el servicio de Batch reintenta las tareas sin ningún límite.<br /><br /> El valor predeterminado es 0 (sin ningún reintento).|


###  <a name="executionInfo"></a> executionInfo

|Nombre del elemento|Tipo|Notas|
|------------------|----------|-----------|
|startTime|DateTime|Hora a la que empezó a ejecutarse la tarea. "En ejecución" se refiere al estado **running**, por lo que si la tarea especifica archivos de recursos o paquetes de aplicación, la hora inicial refleja la hora a la que la tarea empezó a descargarlos o implementarlos.  Si se reinició o reintentó la tarea, es la hora más reciente a la que comenzó a ejecutarse.|
|endTime|DateTime|Hora a la que finalizó la tarea.|
|exitCode|Int32|Código de salida de la tarea.|
|retryCount|Int32|Cantidad de veces que el servicio de Batch reintentó la tarea. La tarea se reintenta si el código de salida es distinto de cero, hasta el valor MaxTaskRetryCount especificado.|
|requeueCount|Int32|Cantidad de veces que el servicio de Batch volvió a poner en cola la tarea como resultado de una solicitud de usuario.<br /><br /> Cuando el usuario quita nodos de un grupo (ya sea debido a que cambia o disminuye el tamaño del grupo), o bien cuando se deshabilita el trabajo, el usuario puede especificar que las tareas en ejecución en los nodos se vuelvan a poner en cola para su ejecución. Este conteo hace un seguimiento de las veces en que la tarea se volvió a poner en cola por estos motivos.|

