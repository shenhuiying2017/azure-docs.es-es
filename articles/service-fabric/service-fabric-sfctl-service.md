---
title: 'CLI de Azure Service Fabric: servicio de sfctl | Microsoft Docs'
description: Se describen los comandos del servicio de sfctl de la CLI de Service Fabric.
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
ms.openlocfilehash: 5c1f485812918397b5b52e650611032c9058e3ee
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="sfctl-service"></a>servicio de sfctl
Cree, elimine y administre servicios, tipos de servicio y paquetes de servicio.

## <a name="commands"></a>Comandos:

|Get-Help|DESCRIPCIÓN|
| --- | --- |
|    app-name       | Obtiene el nombre de la aplicación de Service Fabric para un servicio.|
|    code-package-list | Obtiene la lista de paquetes de código implementados en un nodo de Service Fabric.|
|    create         | Crea el servicio de Service Fabric especificado a partir de la descripción.|
|    delete         | Elimina un servicio existente de Service Fabric.|
|    deployed-type  | Obtiene la información sobre un tipo de servicio especificado de la aplicación implementada en un nodo en un clúster de Service Fabric.|
|    deployed-type-list| Obtiene la lista que contiene información sobre los tipos de servicio desde las aplicaciones implementadas en un nodo en el clúster de Service Fabric.|
|    Descripción    | Obtiene la descripción de un servicio de Service Fabric existente.|
|    health         | Obtiene el estado del servicio de Service Fabric especificado.|
|    info           | Obtiene la información sobre el servicio específico que pertenece a una aplicación de Service Fabric.|
|    list           | Obtiene la información sobre todos los servicios que pertenecen a la aplicación especificados por el identificador de la aplicación.|
|    manifest       | Obtiene el manifiesto que describe un tipo de servicio.|
|    package-deploy | Descarga los paquetes asociados con el manifiesto de servicio especificado en la memoria caché de imágenes en el nodo especificado.|
|    package-health | Obtiene la información sobre el estado de un paquete de servicio para una aplicación específica implementada para una aplicación y nodo de Service Fabric.|
|    package-info   | Obtiene la lista de paquetes de servicio implementados en un nodo de Service Fabric que coincide exactamente con el nombre especificado.|
|    package-list   | Obtiene la lista de paquetes de servicio implementados en un nodo de Service Fabric.|
|    recover        | Indica al clúster de Service Fabric que debería intentar recuperar el servicio especificado, que está actualmente está atascado en pérdida de cuórum.|
|    report-health  | Envía un informe de mantenimiento sobre el servicio de Service Fabric.|
|    resolve        | Resuelve una partición de Service Fabric.|
|    type-list      | Obtiene la lista que contiene la información sobre los tipos de servicio que son compatibles con el tipo de aplicación aprovisionada en un clúster de Service Fabric.|
|    update         | Actualiza el servicio especificado con la descripción de actualización determinada.|


## <a name="sfctl-service-create"></a>sfctl service create
Crea el servicio de Service Fabric especificado a partir de la descripción.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --app-id [Obligatorio]| La identidad de la aplicación principal. Suele ser el identificador completo de la aplicación sin el esquema URI "fabric:"1. A partir de la versión 6.0, los nombres jerárquicos se delimitan con el carácter "~". Por ejemplo, si el nombre de la aplicación es "fabric://myapp/app1", la identidad de la aplicación sería "myapp~app1' en 6.0+ y "myapp/app1" en las versiones anteriores.|
| --name [Obligatorio]| Nombre del servicio. Debe ser un elemento secundario del identificador de la aplicación.           Se trata del nombre completo, incluido el URI `fabric:`. Por ejemplo, el servicio `fabric:/A/B` es un elemento secundario de la aplicación `fabric:/A`.|
| --service-type [Obligatorio]| Nombre del tipo de servicio.|
| --activation-mode     | El modo de activación para el paquete de servicio.|
| --constraints         | Las restricciones de colocación como una cadena. Las restricciones de colocación son expresiones booleanas en las propiedades del nodo y permiten restringir un servicio en nodos concretos según los requisitos de servicio. Por ejemplo, para colocar un servicio en los nodos en los que NodeType está en azul,  especifique lo siguiente: "NodeColor == blue".|
| --correlated-service  | Nombre del servicio de destino con el que realizar la correlación.|
| --correlation         | Correlaciona el servicio con un servicio existente con un afinidad de alineación.|
| --dns-name            | El nombre DNS del servicio que se va a crear. El servicio del sistema DNS de Service Fabric debe activarse para esta configuración.|
| --instance-count      | El recuento de instancias. Esto se aplica solo a servicios sin estado.|
| --int-scheme          | Indica que el servicio debe particionarse de forma uniforme en un rango de enteros sin signo.|
| --int-scheme-count    | El número de particiones dentro del rango de clave de tipo entero (para un esquema de partición de entero uniforme) que crear.|
| --int-scheme-high     | El final del rango de entero de clave, si se usa un esquema de partición de entero uniforme.|
| --int-scheme-low      | El inicio del rango de entero de clave, si se usa un esquema de partición de entero uniforme.|
| --load-metrics        | Lista con codificación JSON de métricas usadas al realizar el equilibrio de carga de servicios en los nodos.|
| --min-replica-set-size| El tamaño del conjunto de réplica mínimo como número. Esto se aplica solo a servicios con estado.|
| --move-cost           | Especifica el costo de movimiento de un servicio. Los valores posibles son: "Zero", "Low", "Medium" y "High".|
| --named-scheme        | Indica que el servicio debe tener varias particiones con nombre.|
| --named-scheme-list   | Lista con codificación JSON de nombres para la partición del servicio, si se usa el esquema de partición con nombre.|
| --no-persisted-state  | Si es true, esto indica que el servicio no tiene un estado persistente almacenado en el disco local o solo almacena el estado en la memoria.|
| --placement-policy-list  | Lista con codificación JSON de directivas de colocación para el servicio y cualquier nombre de dominio asociado. Las directivas pueden ser una o más de: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain` y  `RequireDomainDistribution`.|
| --quorum-loss-wait    | La duración máxima, en segundos, que se permite que la partición esté en un estado de pérdida de cuórum. Esto se aplica solo a servicios con estado.|
| --replica-restart-wait| La duración, en segundos, entre que una réplica deja de funcionar y se crea una nueva. Esto se aplica solo a servicios con estado.|
| --singleton-scheme    | Indica que el servicio debe tener una sola partición o ser un servicio sin particiones.|
| --stand-by-replica-keep  | La duración máxima, en segundos, para las que las réplicas de StandBy se mantienen antes de eliminarse. Esto se aplica solo a servicios con estado.|
| --stateful            | Indica que el servicio es un servicio con estado.|
| --stateless           | Indica que el servicio es un servicio sin estado.|
| --target-replica-set-size| El tamaño del conjunto de réplica de destino como número. Esto se aplica solo a servicios con estado.|
| --timeout -t          | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug               | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h             | Muestra este mensaje de ayuda y sale.|
| --output -o           | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query               | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose             | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-service-delete"></a>sfctl service create
Elimina un servicio existente de Service Fabric.

Elimina un servicio existente de Service Fabric. Una servicio debe crearse para poder eliminarlo. De forma predeterminada, Service Fabric intenta cerrar las réplicas del servicio de forma adecuada y después lo elimina. Sin embargo, si el servicio tiene problemas para cerrar la réplica correctamente, la operación de eliminación puede tardar mucho tiempo o quedarse bloqueada. Use la marca ForceRemove opcional para omitir la secuencia de cierre correcto y forzar la eliminación del servicio.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --service-id [Obligatorio]| La identidad del servicio. Este suele ser el nombre completo del servicio sin el esquema de URI "fabric:". A partir de la versión 6.0, los nombres jerárquicos se delimitan con el carácter "~". Por ejemplo, si el nombre del servicio es "fabric://myapp/app1/svc1", la identidad del servicio sería "myapp~app1~svc1" en 6.0+ y "myapp/app1/svc1" en las versiones anteriores.|
| --force-remove      | Elimina una aplicación o un servicio de Service Fabric de manera forzada sin pasar por la secuencia de apagado correcta. Este parámetro puede usarse para forzar la eliminación de una aplicación o un servicio cuya eliminación normal requiere un tiempo de espera a causa de problemas del código del servicio que impiden el cierre correcto de las réplicas.|
| --timeout -t        | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug             | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h           | Muestra este mensaje de ayuda y sale.|
| --output -o         | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query             | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose           | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-service-description"></a>Descripción del servicio sfctl
Obtiene la descripción de un servicio de Service Fabric existente.

Obtiene la descripción de un servicio de Service Fabric existente. Debe crearse un servicio para poder obtener su descripción.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --service-id [Obligatorio]| La identidad del servicio. Este suele ser el nombre completo del servicio sin el esquema de URI "fabric:". A partir de la versión 6.0, los nombres jerárquicos se delimitan con el carácter "~". Por ejemplo, si el nombre del servicio es "fabric://myapp/app1/svc1", la identidad del servicio sería "myapp~app1~svc1" en 6.0+ y "myapp/app1/svc1" en las versiones anteriores.|
| --timeout -t        | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug             | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h           | Muestra este mensaje de ayuda y sale.|
| --output -o         | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query             | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose           | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-service-health"></a>sfctl service health
Obtiene el estado del servicio de Service Fabric especificado.

Obtiene la información de estado del servicio especificado. Use EventsHealthStateFilter para filtrar la colección de eventos de estado notificados en el servicio en función del estado de mantenimiento. Use el elemento PartitionsHealthStateFilter para filtrar la colección de particiones devueltas. Si especifica un servicio que no existe en el almacén de estado, este cmdlet devuelve un error. .

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --service-id [Obligatorio]| La identidad del servicio. Este suele ser el nombre completo del servicio sin el esquema de URI "fabric:". A partir de la versión 6.0, los nombres jerárquicos se delimitan con el carácter "~". Por ejemplo, si el nombre del servicio es "fabric://myapp/app1/svc1", la identidad del servicio sería "myapp~app1~svc1" en 6.0+ y "myapp/app1/svc1" en las versiones anteriores.|
| --events-health-state-filter | Permite filtrar la colección de objetos HealthEvent devueltos según el estado de mantenimiento. Los valores posibles para este parámetro incluyen el valor entero de uno de los siguientes estados de mantenimiento. Se devuelven únicamente los eventos que coinciden con el filtro. Todos los eventos se utilizan para evaluar el estado de mantenimiento agregado. Si no se especifica, se devuelven todas las entradas. Los valores de estado se marcan según la enumeración, por lo que el valor puede ser una combinación de estos valores obtenidos mediante el operador bit a bit 'OR'. Por ejemplo, si el valor proporcionado es 6, se devuelven todos los eventos con el valor HealthState de Ok (2) y Warning (4). - Default: valor predeterminado. Coincide con cualquier HealthState. El valor predeterminado es cero. - None: filtro que no coincide con ningún valor de HealthState. Se utiliza para no devolver ningún resultado en una determinada colección de estados. El valor es 1. - Ok: filtro que asocia la entrada con el valor de HealthState de Ok. El valor es 2. - Warning: filtro que asocia la entrada con el valor de HealthState de Warning. El valor es 4. - Error: filtro que asocia la entrada con el valor de HealthState de Error. El valor es 8. - All: filtro que asocia la entrada con cualquier valor de HealthState. El valor es 65535.|
|--exclude-health-statistics     | Indica si las estadísticas de mantenimiento se deben devolver como parte del resultado de la consulta. El valor predeterminado es false. Las estadísticas muestran el número de entidades secundarias en estado de mantenimiento Ok, Warning y Error.|
| --partitions-health-state-filter| Permite el filtrado de los objetos de estado de mantenimiento de particiones devueltos en el resultado del la consulta de estado de servicio en función de su estado de mantenimiento. Los valores posibles para este parámetro incluyen el valor entero de uno de los siguientes estados de mantenimiento. Se devuelven únicamente las particiones que coinciden con el filtro. Todas las particiones se utilizan para evaluar el estado de mantenimiento agregado. Si no se especifica, se devuelven todas las entradas. Los valores de estado se marcan según la enumeración, por lo que el valor puede ser una combinación de estos valores obtenidos mediante el operador bit a bit 'OR'. Por ejemplo, si el valor proporcionado es "6", se devuelve el estado de mantenimiento de las particiones con el valor HealthState de Ok (2) y Warning (4). - Default: valor predeterminado. Coincide con cualquier HealthState.                  El valor predeterminado es cero. - None: filtro que no coincide con ningún valor de HealthState. Se utiliza para no devolver ningún resultado en una determinada colección de estados. El valor es 1. - Ok: filtro que asocia la entrada con el valor de HealthState de Ok. El valor es 2. - Warning: filtro que asocia la entrada con el valor de HealthState de Warning. El valor es 4. - Error: filtro que asocia la entrada con el valor de HealthState de Error. El valor es 8. - All: filtro que asocia la entrada con cualquier valor de HealthState. El valor es 65535.|
| --timeout -t                 | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug                      | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h                    | Muestra este mensaje de ayuda y sale.|
| --output -o                  | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.                  Valor predeterminado: json.|
| --query                      | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose                    | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-service-info"></a>sfctl service info
Obtiene la información sobre el servicio específico que pertenece a una aplicación de Service Fabric.

Devuelve la información sobre el servicio específico que pertenece a una aplicación de Service Fabric específica.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --application-id [Obligatorio]| La identidad de la aplicación. Este suele ser el nombre completo de la aplicación sin el esquema de URI "fabric:". A partir de la versión 6.0, los nombres jerárquicos se delimitan con el carácter "~". Por ejemplo, si el nombre de la aplicación es "fabric://myapp/app1", la identidad de la aplicación sería "myapp~app1' en 6.0+ y "myapp/app1" en las versiones anteriores.|
| --service-id [Obligatorio]| La identidad del servicio. Este suele ser el nombre completo del servicio sin el esquema de URI "fabric:". A partir de la versión 6.0, los nombres jerárquicos se delimitan con el carácter "~". Por ejemplo, si el nombre del servicio es "fabric://myapp/app1/svc1", la identidad del servicio sería "myapp~app1~svc1" en 6.0+ y "myapp/app1/svc1" en las versiones anteriores.|
| --timeout -t            | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug                 | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h               | Muestra este mensaje de ayuda y sale.|
| --output -o             | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query                 | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose               | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-service-list"></a>sfctl service list
Obtiene la información sobre todos los servicios que pertenecen a la aplicación especificados por el identificador de la aplicación.

Devuelve la información sobre todos los servicios que pertenecen a la aplicación especificados por el identificador de la aplicación.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --application-id [Obligatorio]| La identidad de la aplicación. Este suele ser el nombre completo de la aplicación sin el esquema de URI "fabric:". A partir de la versión 6.0, los nombres jerárquicos se delimitan con el carácter "~". Por ejemplo, si el nombre de la aplicación es "fabric://myapp/app1", la identidad de la aplicación sería "myapp~app1' en 6.0+ y "myapp/app1" en las versiones anteriores.|
| --continuation-token    | El parámetro del token de continuación se utiliza para obtener el siguiente conjunto de resultados. Un token de continuación con un valor no vacío se incluye en la respuesta de la API cuando los resultados del sistema no caben en una única respuesta. Cuando este valor se pasa a la siguiente llamada API, la API devuelve el siguiente conjunto de resultados. Si no hay ningún resultado más, el token de continuación no contiene un valor. El valor de este parámetro no debe ser la dirección URL codificada.|
| --service-type-name     | El nombre de tipo de servicio usado para filtrar los servicios que se van a consultar.|
| --timeout -t            | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug                 | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h               | Muestra este mensaje de ayuda y sale.|
| --output -o             | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query                 | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose               | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-service-manifest"></a>sfctl service manifest
Obtiene el manifiesto que describe un tipo de servicio.

Obtiene el manifiesto que describe un tipo de servicio. La respuesta contiene el manifiesto de servicio XML como una cadena.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --application-type-name [Obligatorio]| Nombre del tipo de aplicación.|
| --application-type-version [Obligatorio]| La versión del tipo de aplicación.|
| --service-manifest-name [Obligatorio]| El nombre de un manifiesto de servicio registrado como parte de un tipo de aplicación en un clúster de Service Fabric.|
| --timeout -t                      | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug                           | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h                         | Muestra este mensaje de ayuda y sale.|
| --output -o                       | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.                       Valor predeterminado: json.|
| --query                           | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose                         | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-service-recover"></a>sfctl service recover
Indica al clúster de Service Fabric que debería intentar recuperar el servicio especificado, que está actualmente está atascado en pérdida de cuórum.

Indica al clúster de Service Fabric que debería intentar recuperar el servicio especificado, que está actualmente está atascado en pérdida de cuórum. Esta operación solo debe realizarse si no pueden recuperarse las réplicas que están fuera de servicio. El uso incorrecto de esta API puede provocar la pérdida potencial de datos.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --service-id [Obligatorio]| La identidad del servicio. Este suele ser el nombre completo del servicio sin el esquema de URI "fabric:". A partir de la versión 6.0, los nombres jerárquicos se delimitan con el carácter "~". Por ejemplo, si el nombre del servicio es "fabric://myapp/app1/svc1", la identidad del servicio sería "myapp~app1~svc1" en 6.0+ y "myapp/app1/svc1" en las versiones anteriores.|
| --timeout -t        | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug             | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h           | Muestra este mensaje de ayuda y sale.|
| --output -o         | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query             | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose           | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-service-resolve"></a>sfctl service resolve
Resuelve una partición de Service Fabric.

Resuelve una partición de servicio de Service Fabric para obtener los puntos de conexión de las réplicas de servicio.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --service-id [Obligatorio]| La identidad del servicio. Este suele ser el nombre completo del servicio sin el esquema de URI "fabric:". A partir de la versión 6.0, los nombres jerárquicos se delimitan con el carácter "~". Por ejemplo, si el nombre del servicio es "fabric://myapp/app1/svc1", la identidad del servicio sería "myapp~app1~svc1" en 6.0+ y "myapp/app1/svc1" en las versiones anteriores.|
| --partition-key-type| Tipo de clave para la partición. Este parámetro es obligatorio si el esquema de partición para el servicio es Int64Range o Con nombre. Los valores posibles son los siguientes. -Ninguno (1): indica que no se especifica el parámetro PartitionKeyValue. Esto es válido para las particiones con el esquema de partición como Singleton. Este es el valor predeterminado. El valor es 1. -Int64Range (2): indica que el parámetro PartitionKeyValue es una clave de partición int64. Esto es válido para las particiones con el esquema de partición como Int64Range. El valor es 2. - Con nombre (3): indica que el parámetro PartitionKeyValue es un nombre de la partición. Esto es válido para las particiones con el esquema de partición como Con nombre. El valor es 3.|
| --partition-key-value  | Clave de partición. Es obligatoria si el esquema de partición para el servicio es Int64Range o Con nombre.|
| --previous-rsp-version | Valor en el campo Versión de la respuesta que se recibió anteriormente. Es obligatorio si el usuario sabe que el resultado que se obtuvo anteriormente está obsoleto.|
| --timeout -t        | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug             | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h           | Muestra este mensaje de ayuda y sale.|
| --output -o         | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query             | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose           | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-service-update"></a>sfctl service update
Actualiza el servicio especificado con la descripción de actualización determinada.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --service-id [Obligatorio]| Servicio de destino que se va a actualizar. Este suele ser el identificador completo del servicio sin el esquema de URI "fabric:". A partir de la versión 6.0, los nombres jerárquicos se delimitan con el carácter "~". Por ejemplo, si el nombre del servicio es "fabric://myapp/app1/svc1", la identidad del servicio sería "myapp~app1~svc1" en 6.0+ y "myapp/app1/svc1" en las versiones anteriores.|
| --constraints         | Las restricciones de colocación como una cadena. Las restricciones de colocación son expresiones booleanas en las propiedades del nodo y permiten restringir un servicio en nodos concretos según los requisitos de servicio. Por ejemplo, para colocar un servicio en los nodos en los que NodeType está en azul,  especifique lo siguiente: "NodeColor == blue".|
| --correlated-service  | Nombre del servicio de destino con el que realizar la correlación.|
| --correlation         | Correlaciona el servicio con un servicio existente con una afinidad de alineación.|
| --instance-count      | El recuento de instancias. Esto se aplica solo a servicios sin estado.|
| --load-metrics        | Lista con codificación JSON de métricas usadas al realizar el equilibrio de carga en los nodos.|
| --min-replica-set-size| El tamaño del conjunto de réplica mínimo como número. Esto se aplica solo a servicios con estado.|
| --move-cost           | Especifica el costo de movimiento de un servicio. Los valores posibles son: "Zero", "Low", "Medium" y "High".|
| --placement-policy-list  | Lista con codificación JSON de directivas de colocación para el servicio y cualquier nombre de dominio asociado. Las directivas pueden ser una o más de: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain` y  `RequireDomainDistribution`.|
| --quorum-loss-wait    | La duración máxima, en segundos, que se permite que la partición esté en un estado de pérdida de cuórum. Esto se aplica solo a servicios con estado.|
| --replica-restart-wait| La duración, en segundos, entre que una réplica deja de funcionar y se crea una nueva. Esto se aplica solo a servicios con estado.|
| --stand-by-replica-keep  | La duración máxima, en segundos, para las que las réplicas de StandBy se mantienen antes de eliminarse. Esto se aplica solo a servicios con estado.|
| --stateful            | Indica que el servicio de destino es un servicio con estado.|
| --stateless           | Indica que el servicio de destino es un servicio sin estado.|
| --target-replica-set-size| El tamaño del conjunto de réplica de destino como número. Esto se aplica solo a servicios con estado.|
| --timeout -t          | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug               | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h             | Muestra este mensaje de ayuda y sale.|
| --output -o           | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query               | Cadena de consulta de JMESPath. Para obtener más información y ejemplos, consulte http://jmespath.org/.|
| --verbose             | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="next-steps"></a>pasos siguientes
- [Configuración](service-fabric-cli.md) de la CLI de Service Fabric.
- Obtenga información sobre cómo utilizar la CLI de Service Fabric con los [scripts de ejemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).