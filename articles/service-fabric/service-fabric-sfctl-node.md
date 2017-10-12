---
title: 'CLI de Azure Service Fabric: sfctl node | Microsoft Docs'
description: Describe los comandos de sfctl node de la CLI Service Fabric.
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
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: 76037c7b4a2f7ada314a9360e3990245e6fbc06c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-node"></a>sfctl node
Administre los nodos que forman un clúster.

## <a name="commands"></a>Comandos:

|Comando|Descripción|
| --- | --- |
|    disable       | Desactive un nodo de clúster de Service Fabric con la intención de desactivación especificada.|
|    enable        | Active un nodo de clúster de Service Fabric, que está actualmente desactivado.|
|    health        | Obtiene el estado de un nodo de Service Fabric.|
|    info          | Obtiene la lista de nodos del clúster de Service Fabric.|
|    list          | Obtiene la lista de nodos del clúster de Service Fabric.|
|    load          | Obtiene la información de carga de un nodo de Service Fabric.|
|    remove-state  | Notifica a Service Fabric que el estado persistente en un nodo se quitó o perdió de forma permanente.|
|    report-health | Envía un informe de estado sobre el nodo de Service Fabric.|
|    restart       | Reinicia un nodo de clúster de Service Fabric.|
|    transition    | Inicia o detiene un nodo de clúster.|
|    transition-status| Obtiene el progreso de una operación iniciada mediante StartNodeTransition.|


## <a name="sfctl-node-disable"></a>sfctl node disable
Desactive un nodo de clúster de Service Fabric con la intención de desactivación especificada.

Desactive un nodo de clúster de Service Fabric con la intención de desactivación especificada. Una vez que la desactivación está en curso, la intención de desactivación puede aumentar, pero no disminuir (por ejemplo, un nodo que está desactivado con la intención Pause puede desactivarse aún más con Restart, pero no al revés). Los nodos deben reactivarse mediante la operación Activate a node en cualquier momento después de la desactivación. Si la desactivación no se completa, esto cancela la desactivación. Un nodo que deja de funcionar y vuelve a activarse mientras está desactivado todavía debe reactivarse antes de que puedan colocarse servicios en ese nodo.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --node-name [Obligatorio]| El nombre del nodo.|
| --deactivation-intent | Describe la intención o el motivo para desactivar el nodo. Los valores posibles son los siguientes. - Pause: indica que se debe pausar el nodo. El valor es 1. - Restart: indica que la intención es que el nodo se reinicie tras un breve período. El valor es 2. - RemoveData: indica que la intención es que el nodo quite los datos. El valor es 3. .|
| --timeout -t       | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug            | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h          | Muestra este mensaje de ayuda y sale.|
| --output -o        | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query            | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose          | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-node-enable"></a>sfctl node enable
Active un nodo de clúster de Service Fabric, que está actualmente desactivado.

Active un nodo de clúster de Service Fabric, que está actualmente desactivado. Una vez activado, el nodo vuelve a convertirse en un destino viable para colocar las nuevas réplicas, y se vuelvan a activar las réplicas desactivadas restantes en el nodo.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --node-name [Obligatorio]| El nombre del nodo.|
| --timeout -t       | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug            | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h          | Muestra este mensaje de ayuda y sale.|
| --output -o        | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query            | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose          | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-node-health"></a>sfctl node health
Obtiene el estado de un nodo de Service Fabric.

Obtiene el estado de un nodo de Service Fabric. Use EventsHealthStateFilter para filtrar la colección de eventos de estado notificados en el nodo en función del estado de mantenimiento. Si el nodo que se especifica por nombre no existe en el almacén de estado, se devuelve un error.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --node-name [Obligatorio]| El nombre del nodo.|
| --events-health-state-filter| Permite filtrar la colección de objetos HealthEvent devueltos según el estado de mantenimiento. Los valores posibles para este parámetro incluyen el valor entero de uno de los siguientes estados de mantenimiento. Se devuelven únicamente los eventos que coinciden con el filtro. Todos los eventos se utilizan para evaluar el estado de mantenimiento agregado. Si no se especifica, se devuelven todas las entradas. Los valores de estado se marcan según la enumeración, por lo que el valor puede ser una combinación de estos valores obtenidos mediante el operador bit a bit 'OR'. Por ejemplo, si el valor proporcionado es 6, se devuelven todos los eventos con el valor HealthState de Ok (2) y Warning (4). - Default: valor predeterminado. Coincide con cualquier HealthState. El valor predeterminado es cero. - None: filtro que no coincide con ningún valor de HealthState. Se utiliza para no devolver ningún resultado en una determinada colección de estados. El valor es 1. - Ok: filtro que asocia la entrada con el valor de HealthState de Ok. El valor es 2. - Warning: filtro que asocia la entrada con el valor de HealthState de Warning. El valor es 4. - Error: filtro que asocia la entrada con el valor de HealthState de Error. El valor es 8. - All: filtro que asocia la entrada con cualquier valor de HealthState. El valor es 65535.|
| --timeout -t             | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug                  | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h                | Muestra este mensaje de ayuda y sale.|
| --output -o              | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query                  | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose                | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-node-info"></a>sfctl node info
Obtiene la lista de nodos del clúster de Service Fabric.

Obtiene la información sobre un nodo específico en el clúster de Service Fabric. La respuesta incluye el nombre, el estado, el id., el mantenimiento, el tiempo de actividad y otros detalles acerca del nodo.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --node-name [Obligatorio]| El nombre del nodo.|
| --timeout -t       | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug            | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h          | Muestra este mensaje de ayuda y sale.|
| --output -o        | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query            | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose          | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-node-list"></a>sfctl node list
Obtiene la lista de nodos del clúster de Service Fabric.

El punto de conexión de los nodos devuelve información sobre los nodos en el clúster de Service Fabric. La respuesta incluye el nombre, el estado, el id., el mantenimiento, el tiempo de actividad y otros detalles acerca del nodo.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --continuation-token| El parámetro continuation token se utiliza para obtener el siguiente conjunto de resultados. Un continuation token con un valor no vacío se incluye en la respuesta de la API cuando los resultados del sistema no caben en una única respuesta.      Cuando este valor se pasa a la siguiente llamada de la API, la API devuelve el siguiente conjunto de resultados. Si no hay ningún resultado más, el continuation token no contiene un valor. El valor de este parámetro no debe ser la dirección URL codificada.|
| --node-status-filter| Permite filtrar los nodos según NodeStatus. Se devuelven únicamente los nodos que coinciden con el valor de filtro especificado. El valor de filtro puede ser uno de los siguientes. - default: este valor de filtro coincide con todos los nodos, excepto aquellas con estados Unknown o Removed. - all: este valor de filtro coincide con todos los nodos. - up: este valor de filtro coincide con los nodos con estado Up (en funcionamiento). - down: este valor de filtro coincide con los nodos con estado Down (fuera de servicio). - enabling: este valor de filtro coincide con los nodos que están en proceso de habilitación con estado Enabling (en habilitación). - disabling: este valor de filtro coincide con los nodos que están en proceso de deshabilitación con estado Disabling (en deshabilitación). - disabled: este valor de filtro coincide con los nodos con estado Disabled (deshabilitados). - unknown: este valor de filtro coincide con nodos cuyo estado es Unknown (desconocido). Un nodo estaría en estado Unknown si Service Fabric no tiene información de autorización sobre ese nodo. Esto puede ocurrir si el sistema aprende sobre un nodo en tiempo de ejecución. - removed: este valor de filtro coincide con nodos cuyo estado es Removed (quitado). Estos son los nodos que se quitaron del clúster con la API RemoveNodeState. .      Valor predeterminado: default.|
| --timeout -t     | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug          | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h        | Muestra este mensaje de ayuda y sale.|
| --output -o      | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query          | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose        | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-node-load"></a>sfctl node load
Obtiene la información de carga de un nodo de Service Fabric.

Obtiene la información de carga de un nodo de Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --node-name [Obligatorio]| El nombre del nodo.|
| --timeout -t       | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug            | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h          | Muestra este mensaje de ayuda y sale.|
| --output -o        | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query            | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose          | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-node-restart"></a>sfctl node restart
Reinicia un nodo de clúster de Service Fabric.

Reinicia un nodo de clúster de Service Fabric que ya se inició.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --node-name [Obligatorio]| El nombre del nodo.|
| --create-fabric-dump  | Especifique en True para crear un volcado de memoria del proceso de nodo de tejido. Distingue mayúsculas de minúsculas.  Valor predeterminado: False.|
| --node-instance-id | El identificador de instancia del nodo de destino. Si se especificó el identificador de instancia, se reinicia el nodo solo si coincide con la instancia actual del nodo. Un valor predeterminado de "0" coincidiría con cualquier identificador de instancia. El identificador de instancia puede obtenerse mediante la consulta get node.  Valor predeterminado: 0.|
| --timeout -t       | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug            | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h          | Muestra este mensaje de ayuda y sale.|
| --output -o        | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query            | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose          | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-node-transition"></a>sfctl node transition
Inicia o detiene un nodo de clúster.

Inicia o detiene un nodo de clúster.  Un nodo de clúster es un proceso, no la propia instancia del sistema operativo.
Para iniciar un nodo, pase "Start" para el parámetro NodeTransitionType. Para detener un nodo, pase "Stop" para el parámetro NodeTransitionType. Esta API inicia la operación: cuando la API devuelve el nodo, puede no haya terminado de realizar la transición aún. Llame a GetNodeTransitionProgress con el mismo OperationId para obtener el progreso de la operación. .

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --node-instance-id [Obligatorio]| El identificador de instancia del nodo de destino. Puede determinarse a través de la API GetNodeInfo.|
| --node-name [Obligatorio]| El nombre del nodo.|
| --node-transition-type [Obligatorio]| Indica el tipo de transición que se va a realizar.                       NodeTransitionType.Start inicia un nodo detenido.                       NodeTransitionType.Stop detiene un nodo que está en funcionamiento. - Invalid: reservado.  No se pasa a la API. -Start: realizar una transición de un nodo detenido a en funcionamiento. -Stop: realizar una transición de un nodo en funcionamiento a detenido. .|
| --operation-id [Obligatorio]| Un GUID que identifica una llamada de esta API.  Se pasa a la API GetProgress correspondiente.|
| --stop-duration-in-seconds [Obligatorio]| La duración, en segundos, para conservar el nodo detenido.  El valor mínimo es 600; y el máximo, 14400. Después de que expire este tiempo, el nodo automáticamente vuelve a estar en funcionamiento.|
| --timeout -t                      | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug                           | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h                         | Muestra este mensaje de ayuda y sale.|
| --output -o                       | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.                       Valor predeterminado: json.|
| --query                           | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose                         | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="next-steps"></a>Pasos siguientes
- [Configuración](service-fabric-cli.md) de la CLI de Service Fabric.
- Obtenga información sobre cómo utilizar la CLI de Service Fabric con los [scripts de ejemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).