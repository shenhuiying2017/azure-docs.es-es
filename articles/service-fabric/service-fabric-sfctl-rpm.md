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
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/26/2017
ms.author: ryanwi
ms.openlocfilehash: 2a9a565cf2c20490475d1b73b0f99fbe63e57dd3
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="sfctl-rpm"></a>rpm de sfctl
Consulte y envíe comandos al servicio del administrador de reparaciones.

## <a name="commands"></a>Comandos:
|Get-Help|DESCRIPCIÓN|
| --- | --- |
|    approve-force| Fuerza la aprobación de la tarea de reparación determinada.|
|    delete       | Elimina una tarea de reparación completada.|
|    list         | Obtiene una lista de tareas de reparación que coinciden con los filtros especificados.|

## <a name="sfctl-rpm-delete"></a>sfctl rpm delete
Elimina una tarea de reparación completada.

Esta API es compatible con la plataforma Service Fabric; no está diseñada para utilizarse directamente desde el código. 

### <a name="arguments"></a>Argumentos
|Argumento|DESCRIPCIÓN|
| --- | --- |
|    --task-id [Obligatorio]| El identificador de la tarea de reparación completada que se va a eliminar.|
|    --version           | El número de versión actual de la tarea de reparación. Si es distinto de cero, la solicitud se realizará correctamente solo si este valor coincide con la versión actual real de la tarea de reparación. Si es cero, no se realiza ninguna comprobación de la versión.|

### <a name="global-arguments"></a>Argumentos globales
|Argumento|DESCRIPCIÓN|
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
|Argumento|DESCRIPCIÓN|
| --- | --- |
|    --executor-filter| El nombre del ejecutar de reparación cuyas tareas notificadas deben incluirse en la lista.|
|    --state-filter   | Una operación OR bit a bit de los siguientes valores, especificando qué estados de tareas deben incluirse en la lista de resultados. - 1 - Created - 2 - Claimed - 4 - Preparing - 8 - Approved - 16 - Executing - 32 - Restoring - 64 - Completed.|
|    --task-id-filter | El prefijo del identificador de tarea de reparación que debe coincidir.|

### <a name="global-arguments"></a>Argumentos globales
|Argumento|DESCRIPCIÓN|
| --- | --- |
|    --debug          | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
|    --help -h        | Muestra este mensaje de ayuda y sale.|
|    --output -o      | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado| json.|
|    --query          | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
|    --verbose        | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="next-steps"></a>pasos siguientes
- [Configuración](service-fabric-cli.md) de la CLI de Service Fabric.
- Obtenga información sobre cómo utilizar la CLI de Service Fabric con los [scripts de ejemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).