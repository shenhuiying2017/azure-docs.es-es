---
title: "CLI de Azure Service Fabric: réplica de sfctl | Microsoft Docs"
description: "Se describen los comandos de réplica de sfctl de la CLI de Service Fabric."
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
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: 422c19dfa9a204d98a898f76bc1af92a05c054d0
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="sfctl-replica"></a>réplica de sfctl
Administre las réplicas que pertenecen a las particiones del servicio.

## <a name="commands"></a>Comandos:

|Get-Help|DESCRIPCIÓN|
| --- | --- |
|    deployed  | Obtiene los detalles de la réplica implementada en un nodo de Service Fabric.|
|    deployed-list| Obtiene la lista de réplicas implementadas en un nodo de Service Fabric.|
|    health    | Obtiene el mantenimiento de una réplica de servicio con estado o de una instancia de servicio sin estado de Service Fabric.|
|    info      | Obtiene la información sobre una réplica de una partición de Service Fabric.|
|    list      | Obtiene la información sobre las réplicas de una partición de servicio de Service Fabric.|
|    remove    | Quita una réplica de servicio que se ejecuta en un nodo.|
|    report-health| Envía un informe de mantenimiento sobre la réplica de Service Fabric.|
|    restart   | Reinicia una réplica de servicio de un servicio persistente que se ejecuta en un nodo.|


## <a name="sfctl-replica-deployed"></a>sfctl replica deployed
Obtiene los detalles de la réplica implementada en un nodo de Service Fabric.

Obtiene los detalles de la réplica implementada en un nodo de Service Fabric. La información incluye el tipo de servicio, el nombre del servicio, la operación de servicio actual, la fecha y hora de inicio de la operación de servicio actual, el identificador de partición, el identificador de réplica/instancia, la carga notificada y otra información.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --node-name [Obligatorio]| El nombre del nodo.|
| --partition-id [Obligatorio]| La identidad de la partición.|
| --replica-id [Obligatorio]| El identificador de la réplica.|
| --timeout -t          | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug               | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h             | Muestra este mensaje de ayuda y sale.|
| --output -o           | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query               | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose             | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-replica-health"></a>sfctl replica health
Obtiene el mantenimiento de una réplica de servicio con estado o de una instancia de servicio sin estado de Service Fabric.

Obtiene el estado de una réplica de Service Fabric. Use EventsHealthStateFilter para filtrar la colección de eventos de estado notificados en la réplica en función del estado de mantenimiento. .

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --partition-id [Obligatorio]| La identidad de la partición.|
| --replica-id [Obligatorio]| El identificador de la réplica.|
| --events-health-state-filter| Permite filtrar la colección de objetos HealthEvent devueltos según el estado de mantenimiento. Los valores posibles para este parámetro incluyen el valor entero de uno de los siguientes estados de mantenimiento. Se devuelven únicamente los eventos que coinciden con el filtro. Todos los eventos se utilizan para evaluar el estado de mantenimiento agregado. Si no se especifica, se devuelven todas las entradas. Los valores de estado se marcan según la enumeración, por lo que el valor puede ser una combinación de estos valores obtenidos mediante el operador bit a bit 'OR'. Por ejemplo, si el valor proporcionado es 6, se devuelven todos los eventos con el valor HealthState de Ok (2) y Warning (4). - Default: valor predeterminado. Coincide con cualquier HealthState. El valor predeterminado es cero. - None: filtro que no coincide con ningún valor de HealthState. Se utiliza para no devolver ningún resultado en una determinada colección de estados. El valor es 1. - Ok: filtro que asocia la entrada con el valor de HealthState de Ok. El valor es 2. - Warning: filtro que asocia la entrada con el valor de HealthState de Warning. El valor es 4. - Error: filtro que asocia la entrada con el valor de HealthState de Error. El valor es 8. - All: filtro que asocia la entrada con cualquier valor de HealthState. El valor es 65535.|
| --timeout -t             | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug                  | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h                | Muestra este mensaje de ayuda y sale.|
| --output -o              | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query                  | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose                | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-replica-info"></a>sfctl replica info
Obtiene la información sobre una réplica de una partición de Service Fabric.

La respuesta incluye el identificador, el rol, el estado, el mantenimiento, el nombre del nodo, el tiempo de actividad y otros detalles sobre la réplica.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --partition-id [Obligatorio]| La identidad de la partición.|
| --replica-id [Obligatorio]| El identificador de la réplica.|
| --continuation-token  | El parámetro del token de continuación se utiliza para obtener el siguiente conjunto de resultados. Un token de continuación con un valor no vacío se incluye en la respuesta de la API cuando los resultados del sistema no caben en una única respuesta. Cuando este valor se pasa a la siguiente llamada API, la API devuelve el siguiente conjunto de resultados. Si no hay ningún resultado más, el token de continuación no contiene un valor. El valor de este parámetro no debe ser la dirección URL codificada.|
| --timeout -t          | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug               | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h             | Muestra este mensaje de ayuda y sale.|
| --output -o           | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query               | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose             | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-replica-list"></a>sfctl replica list
Obtiene la información sobre las réplicas de una partición de servicio de Service Fabric.

El punto de conexión de GetReplicas devuelve información sobre las réplicas de la partición especificada.
La respuesta incluye el identificador, el rol, el estado, el mantenimiento, el nombre del nodo, el tiempo de actividad y otros detalles sobre la réplica.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --partition-id [Obligatorio]| La identidad de la partición.|
| --continuation-token  | El parámetro del token de continuación se utiliza para obtener el siguiente conjunto de resultados. Un token de continuación con un valor no vacío se incluye en la respuesta de la API cuando los resultados del sistema no caben en una única respuesta. Cuando este valor se pasa a la siguiente llamada API, la API devuelve el siguiente conjunto de resultados. Si no hay ningún resultado más, el token de continuación no contiene un valor. El valor de este parámetro no debe ser la dirección URL codificada.|
| --timeout -t          | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug               | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h             | Muestra este mensaje de ayuda y sale.|
| --output -o           | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query               | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose             | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-replica-remove"></a>sfctl replica remove
Quita una réplica de servicio que se ejecuta en un nodo.

Esta API simula un error de réplica de Service Fabric mediante la eliminación de una réplica de un clúster de Service Fabric. La eliminación cierra la réplica, realiza su transición al rol None y elimina del clúster toda la información sobre la réplica. Esta API comprueba la ruta de acceso de eliminación del estado de la réplica y simula la ruta de acceso permanente del error notificado a través de las API de cliente. Advertencia: No se realizan comprobaciones de seguridad cuando se utiliza esta API. El uso incorrecto de esta API puede conllevar la pérdida de datos de los servicios con estado. Además, la marca forceRemove afecta a todas las demás réplicas hospedadas en el mismo proceso.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --node-name [Obligatorio]| El nombre del nodo.|
| --partition-id [Obligatorio]| La identidad de la partición.|
| --replica-id [Obligatorio]| El identificador de la réplica.|
| --force-remove        | Elimina una aplicación o un servicio de Service Fabric de manera forzada sin pasar por la secuencia de apagado correcta. Este parámetro puede usarse para forzar la eliminación de una aplicación o un servicio cuya eliminación normal requiere un tiempo de espera a causa de problemas del código del servicio que impiden el cierre correcto de las réplicas.|
| --timeout -t          | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug               | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h             | Muestra este mensaje de ayuda y sale.|
| --output -o           | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query               | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose             | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-replica-restart"></a>sfctl replica restart
Reinicia una réplica de servicio de un servicio persistente que se ejecuta en un nodo.

Reinicia una réplica de servicio de un servicio persistente que se ejecuta en un nodo. Advertencia: No se realizan comprobaciones de seguridad cuando se utiliza esta API. El uso incorrecto de esta API puede conllevar la pérdida de disponibilidad de los servicios con estado.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --node-name [Obligatorio]| El nombre del nodo.|
| --partition-id [Obligatorio]| La identidad de la partición.|
| --replica-id [Obligatorio]| El identificador de la réplica.|
| --timeout -t          | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug               | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h             | Muestra este mensaje de ayuda y sale.|
| --output -o           | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query               | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose             | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="next-steps"></a>pasos siguientes
- [Configuración](service-fabric-cli.md) de la CLI de Service Fabric.
- Obtenga información sobre cómo utilizar la CLI de Service Fabric con los [scripts de ejemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).
