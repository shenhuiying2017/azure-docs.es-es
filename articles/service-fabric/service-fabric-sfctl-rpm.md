---
title: 'CLI de Azure Service Fabric: rpm de sfctl | Microsoft Docs'
description: Se describen los comandos de rpm de sfctl de la CLI de Service Fabric.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/26/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: f032af4714ad458fa6ad6fb0741f689d44f4098b
ms.contentlocale: es-es
ms.lasthandoff: 09/27/2017

---

# <a name="sfctl-rpm"></a>rpm de sfctl
Consulte y envíe comandos al servicio del administrador de reparaciones.

## <a name="commands"></a>Comandos:
|Comando|Descripción|
| --- | --- |
|    approve-force| Fuerza la aprobación de la tarea de reparación determinada.|
|    delete       | Elimina una tarea de reparación completada.|
|    list         | Obtiene una lista de tareas de reparación que coinciden con los filtros especificados.|

## <a name="sfctl-rpm-delete"></a>sfctl rpm delete
Elimina una tarea de reparación completada.

Esta API es compatible con la plataforma Service Fabric; no está diseñada para utilizarse directamente desde el código. 

### <a name="arguments"></a>Argumentos
|Argumento|Descripción|
| --- | --- |
|    --task-id [Obligatorio]| El identificador de la tarea de reparación completada que se va a eliminar.|
|    --version           | El número de versión actual de la tarea de reparación. Si es distinto de cero, la solicitud se realizará correctamente solo si este valor coincide con la versión actual real de la tarea de reparación. Si es cero, no se realiza ninguna comprobación de la versión.|

### <a name="global-arguments"></a>Argumentos globales
|Argumento|Descripción|
| --- | --- |
|    --debug             | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
|    --help -h           | Muestra este mensaje de ayuda y sale.|
|    --output -o         | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.
|    --query             | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
|    --verbose           | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|


## <a name="sfctl-rpm-list"></a>sfctl rpm list
Obtiene una lista de tareas de reparación que coinciden con los filtros especificados.

Esta API es compatible con la plataforma Service Fabric; no está diseñada para utilizarse directamente desde el código. 

### <a name="arguments"></a>Argumentos
|Argumento|Descripción|
| --- | --- |
|    --executor-filter| El nombre del ejecutar de reparación cuyas tareas notificadas deben incluirse en la lista.|
|    --state-filter   | Una operación OR bit a bit de los siguientes valores, especificando qué estados de tareas deben incluirse en la lista de resultados. - 1 - Created - 2 - Claimed - 4 - Preparing - 8 - Approved - 16 - Executing - 32 - Restoring - 64 - Completed.|
|    --task-id-filter | El prefijo del identificador de tarea de reparación que debe coincidir.|

### <a name="global-arguments"></a>Argumentos globales
|Argumento|Descripción|
| --- | --- |
|    --debug          | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
|    --help -h        | Muestra este mensaje de ayuda y sale.|
|    --output -o      | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado| json.|
|    --query          | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
|    --verbose        | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="next-steps"></a>Pasos siguientes
- [Configuración](service-fabric-cli.md) de la CLI de Service Fabric.
- Obtenga información sobre cómo utilizar la CLI de Service Fabric con los [scripts de ejemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).
