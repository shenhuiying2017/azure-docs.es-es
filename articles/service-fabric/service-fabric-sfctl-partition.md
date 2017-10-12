---
title: 'CLI de Azure Service Fabric: sfctl partition | Microsoft Docs'
description: Describe los comandos de sfctl partition de la CLI Service Fabric.
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
ms.openlocfilehash: a5d0ff59803212403281063f47e706433cee64b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-partition"></a>sfctl partition
Consulte y administre las particiones para cualquier servicio.

## <a name="commands"></a>Comandos:

|Comando|Descripción|
| --- | --- |
|    data-loss      | Esta API induce la pérdida de datos para la partición especificada.|
|    data-loss-status  | Obtiene el progreso de una operación de pérdida de datos de partición que se ha iniciado mediante la API StartDataLoss.|
|    health         | Obtiene el estado de la partición de Service Fabric especificada.|
|    info           | Obtiene la información sobre una partición de Service Fabric.|
|    list           | Obtiene la lista de particiones de un servicio de Service Fabric.|
|    load           | Obtiene la carga de la partición de Service Fabric especificada.|
|    load-reset     | Restablece la carga actual de una partición de Service Fabric.|
|    quorum-loss    | Induce la pérdida de cuórum para una partición determinada del servicio con estado.|
|    quorum-loss-status| Obtiene el progreso de una operación de pérdida de cuórum en una partición iniciada mediante la API StartQuorumLoss.|
|    recover        | Indica al clúster de Service Fabric que debería intentar recuperar una partición específica que actualmente está atascada en pérdida de cuórum.|
|    recover-all    | Indica al clúster de Service Fabric que debería intentar recuperar cualquier servicio (incluidos los servicios del sistema) que actualmente esté atascado en pérdida de cuórum.|
|    report-health  | Envía un informe de estado sobre la partición de Service Fabric.|
|    restart        | Esta API reinicia algunas o todas las réplicas o instancias de la partición especificada.|
|    restart-status | Obtiene el progreso de una operación PartitionRestart iniciada mediante StartPartitionRestart.|
|    svc-name       | Obtiene el nombre del servicio de Service Fabric para una partición.|


## <a name="sfctl-partition-health"></a>sfctl partition health
Obtiene el estado de la partición de Service Fabric especificada.

Obtiene la información de mantenimiento de la partición especificada. Use EventsHealthStateFilter para filtrar la colección de eventos de estado notificados en el servicio en función del estado de mantenimiento.
Use ReplicasHealthStateFilter para filtrar la colección de objetos ReplicaHealthState en la partición. Si especifica una partición que no existe en el almacén de estado, este cmdlet devuelve un error. .

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --partition-id [Obligatorio]| La identidad de la partición.|
| --events-health-state-filter  | Permite filtrar la colección de objetos HealthEvent devueltos según el estado de mantenimiento. Los valores posibles para este parámetro incluyen el valor entero de uno de los siguientes estados de mantenimiento.                Se devuelven únicamente los eventos que coinciden con el filtro. Todos los eventos se utilizan para evaluar el estado de mantenimiento agregado. Si no se especifica, se devuelven todas las entradas. Los valores de estado se marcan según la enumeración, por lo que el valor puede ser una combinación de estos valores obtenidos mediante el operador bit a bit 'OR'. Por ejemplo, si el valor proporcionado es 6, se devuelven todos los eventos con el valor HealthState de Ok (2) y Warning (4). - Default: valor predeterminado. Coincide con cualquier HealthState. El valor predeterminado es cero. - None: filtro que no coincide con ningún valor de HealthState. Se utiliza para no devolver ningún resultado en una determinada colección de estados. El valor es 1. - Ok: filtro que asocia la entrada con el valor de HealthState de Ok. El valor es 2. - Warning: filtro que asocia la entrada con el valor de HealthState de Warning. El valor es 4. - Error: filtro que asocia la entrada con el valor de HealthState de Error. El valor es 8.                - All: filtro que asocia la entrada con cualquier valor de HealthState.                El valor es 65535.|
|--exclude-health-statistics   | Indica si las estadísticas de mantenimiento se deben devolver como parte del resultado de la consulta. El valor predeterminado es false. Las estadísticas muestran el número de entidades secundarias en estado de mantenimiento Ok, Warning y Error.|
| --replicas-health-state-filter| Permite filtrar la colección de objetos ReplicaHealthState en la partición. El valor puede obtenerse de los miembros o de las operaciones bit a bit en miembros de HealthStateFilter. Se devuelven únicamente las réplicas que coinciden con el filtro. Todas las réplicas se utilizan para evaluar el estado de mantenimiento agregado. Si no se especifica, se devuelven todas las entradas. Los valores de estado se marcan según la enumeración, por lo que el valor puede ser una combinación de estos valores obtenidos mediante el operador bit a bit 'OR'. Por ejemplo, si el valor proporcionado es 6, se devuelven todos los eventos con el valor HealthState de Ok (2) y Warning (4). Los valores posibles para este parámetro incluyen el valor entero de uno de los siguientes estados de mantenimiento. - Default: valor predeterminado. Coincide con cualquier HealthState. El valor predeterminado es cero. - None: filtro que no coincide con ningún valor de HealthState. Se utiliza para no devolver ningún resultado en una determinada colección de estados. El valor es 1. - Ok: filtro que asocia la entrada con el valor de HealthState de Ok. El valor es 2. - Warning: filtro que asocia la entrada con el valor de HealthState de Warning. El valor es 4. - Error: filtro que asocia la entrada con el valor de HealthState de Error. El valor es 8. - All: filtro que asocia la entrada con cualquier valor de HealthState. El valor es 65535.|
| --timeout -t               | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug                    | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h                  | Muestra este mensaje de ayuda y sale.|
| --output -o                | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.                Valor predeterminado: json.|
| --query                    | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/. |
| --verbose                  | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-partition-info"></a>sfctl partition info
Obtiene la información sobre una partición de Service Fabric.

El punto de conexión de las particiones devuelve información sobre la partición especificada. La respuesta incluye el identificador de partición, la información de esquemas de partición, las claves admitidas por la partición, el estado, el mantenimiento y otros detalles acerca de la partición.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --partition-id [Obligatorio]| La identidad de la partición.|
| --timeout -t          | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug               | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h             | Muestra este mensaje de ayuda y sale.|
| --output -o           | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query               | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose             | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-partition-list"></a>sfctl partition list
Obtiene la lista de particiones de un servicio de Service Fabric.

Obtiene la lista de particiones de un servicio de Service Fabric. El identificador de partición, la información de esquemas de partición, las claves admitidas por la partición, el estado, el mantenimiento y otros detalles acerca de la partición.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --service-id [Obligatorio]| La identidad del servicio. Este suele ser el nombre completo del servicio sin el esquema de URI "fabric:". A partir de la versión 6.0, los nombres jerárquicos se delimitan con el carácter "~". Por ejemplo, si el nombre del servicio es "fabric://myapp/app1/svc1", la identidad del servicio sería "myapp~app1~svc1" en 6.0+ y "myapp/app1/svc1" en las versiones anteriores.|
| --continuation-token| El parámetro continuation token se utiliza para obtener el siguiente conjunto de resultados.         Un continuation token con un valor no vacío se incluye en la respuesta de la API cuando los resultados del sistema no caben en una única respuesta. Cuando este valor se pasa a la siguiente llamada de la API, la API devuelve el siguiente conjunto de resultados. Si no hay ningún resultado más, el continuation token no contiene un valor. El valor de este parámetro no debe ser la dirección URL codificada.|
| --timeout -t        | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug             | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h           | Muestra este mensaje de ayuda y sale.|
| --output -o         | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query             | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose           | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-partition-load"></a>sfctl partition load
Obtiene la carga de la partición de Service Fabric especificada.

Devuelve información sobre la partición especificada. La respuesta incluye una lista de información de carga. Cada información incluye el nombre de la métrica de carga, el valor y la última hora notificada en UTC. .

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --partition-id [Obligatorio]| La identidad de la partición.|
| --timeout -t          | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug               | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h             | Muestra este mensaje de ayuda y sale.|
| --output -o           | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query               | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose             | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-partition-recover"></a>sfctl partition recover
Indica al clúster de Service Fabric que debería intentar recuperar una partición específica que actualmente está atascada en pérdida de cuórum.

Indica al clúster de Service Fabric que debería intentar recuperar una partición específica que actualmente está atascada en pérdida de cuórum. Esta operación solo debe realizarse si se sabe que no pueden recuperarse las réplicas que están fuera de servicio. El uso incorrecto de esta API puede provocar la pérdida potencial de datos.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --partition-id [Obligatorio]| La identidad de la partición.|
| --timeout -t          | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug               | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h             | Muestra este mensaje de ayuda y sale.|
| --output -o           | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query               | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose             | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-partition-restart"></a>sfctl partition restart
Esta API reinicia algunas o todas las réplicas o instancias de la partición especificada.

Esta API es útil para probar la conmutación por error. Si se utilizan como destino de una partición de servicio sin estado, RestartPartitionMode debe ser AllReplicasOrInstances. Llame a la API GetPartitionRestartProgress con el mismo OperationId para obtener el progreso. .

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --operation-id [Obligatorio]| Un GUID que identifica una llamada de esta API.  Se pasa a la API GetProgress correspondiente.|
| --partition-id [Obligatorio]| La identidad de la partición.|
| --restart-partition-mode [Obligatorio]| - Invalid: reservado.  No se pasa a la API. - AllReplicasOrInstances: todas las réplicas o instancias de la partición se reinician al mismo tiempo. - OnlyActiveSecondaries: solo se reinician las réplicas secundarias. .|
| --service-id [Obligatorio]| La identidad del servicio. Este suele ser el nombre completo del servicio sin el esquema de URI 'fabric:'. A partir de la versión 6.0, los nombres jerárquicos se delimitan con el carácter "~". Por ejemplo, si el nombre del servicio es "fabric://myapp/app1/svc1", la identidad del servicio sería "myapp~app1~svc1" en 6.0+ y "myapp/app1/svc1" en las versiones anteriores.|
| --timeout -t                    | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug                         | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h                       | Muestra este mensaje de ayuda y sale.|
| --output -o                     | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.                     Valor predeterminado: json.|
| --query                         | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose                       | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="next-steps"></a>Pasos siguientes
- [Configuración](service-fabric-cli.md) de la CLI de Service Fabric.
- Obtenga información sobre cómo utilizar la CLI de Service Fabric con los [scripts de ejemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).