---
title: "CLI de Azure Service Fabric: aplicación de sfctl | Microsoft Docs"
description: "Se describen los comandos de aplicación de sfctl de la CLI de Service Fabric."
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
ms.openlocfilehash: dc57c813a6aecabc21ac3931b7294bce909778d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-application"></a>aplicación de sfctl
Cree, elimine y administre aplicaciones y tipos de aplicaciones.

## <a name="commands"></a>Comandos:

|Comando|Descripción|
| --- | --- |
| create       | Crea una aplicación de Service Fabric con la descripción especificada.|
| delete       | Elimina una aplicación existente de Service Fabric.|
| deployed     | Obtiene la información sobre una aplicación implementada en un nodo de Service Fabric.|
| deployed-health | Obtiene la información sobre el estado de una aplicación implementada en un nodo de
                      Service Fabric.|
| deployed-list| Obtiene la lista de las aplicaciones implementadas en un nodo de Service Fabric.|
| health       | Obtiene el estado de la aplicación de Service Fabric.|
| info         | Obtiene la información sobre una aplicación de Service Fabric.|
| list         | Obtiene la lista de aplicaciones creadas en el clúster de Service Fabric que coinciden con los
                      filtros especificados como parámetro.|
| load | Obtiene la información de carga sobre una aplicación de Service Fabric. |
| manifest     | Obtiene el manifiesto que describe un tipo de aplicación.|
| provision    | Aprovisiona o registra un tipo de aplicación de Service Fabric en el clúster.|
| report-health| Envía un informe de estado sobre la aplicación de Service Fabric.|
| type         | Obtiene la lista de tipos de aplicaciones del clúster de Service Fabric que coinciden
                      exactamente con el nombre especificado.|
| type-list    | Obtiene la lista de tipos de aplicaciones del clúster de Service Fabric.|
| unprovision  | Elimina un tipo de aplicación de Service Fabric del clúster o anula su registro.|
| upgrade      | Inicia la actualización de una aplicación en el clúster de Service Fabric.|
| upgrade-resume  | Reanuda la actualización de una aplicación en el clúster de Service Fabric.|
| upgrade-rollback| Inicia la reversión de la actualización en curso de una aplicación en el
                      clúster de Service Fabric.|
| upgrade-status  | Obtiene los detalles de la última actualización de esta aplicación.|
| upload       | Copia un paquete de aplicación de Service Fabric en el almacén de imágenes.|

## <a name="sfctl-application-create"></a>sfctl application create
Crea una aplicación de Service Fabric con la descripción especificada.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --app-name [Obligatorio]| El nombre de la aplicación, incluido el esquema URI 'fabric:'.|
| --app-type [Obligatorio]| El nombre del tipo de aplicación que se encuentra en el manifiesto de aplicación.|
| --app-version [Obligatorio]| La versión del tipo de aplicación, tal como se define en el manifiesto de aplicación.|
| --max-node-count     | El número máximo de nodos que Service Fabric reserva como capacidad para esta aplicación. Esto no significa que los servicios de esta aplicación estén colocados en todos estos nodos.|
| --metrics            | Una lista codificada de JSON con las descripciones de las métricas de capacidad de la aplicación. Una métrica se define como un nombre, asociado con un conjunto de funcionalidades de cada nodo en que se encuentra la aplicación.|
| --min-node-count     | El número mínimo de nodos que Service Fabric reserva como capacidad para esta aplicación. Esto no significa que los servicios de esta aplicación estén colocados en todos estos nodos.|
| --parameters         | Una lista codificada de JSON con los reemplazos de los parámetros de la aplicación que se aplicarán al crear la aplicación.|
| --timeout -t         | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug              | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h            | Muestra este mensaje de ayuda y sale.|
| --output -o          | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query              | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose            | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-application-delete"></a>sfctl application delete
Elimina una aplicación existente de Service Fabric.

Elimina una aplicación existente de Service Fabric. Una aplicación debe crearse para poder eliminarla. Al eliminar una aplicación, se eliminan todos los servicios que forman parte de ella. De forma predeterminada, Service Fabric intenta cerrar las réplicas del servicio de forma adecuada y después lo elimina. Sin embargo, si el servicio tiene problemas para cerrar la réplica correctamente, la operación de eliminación puede tardar mucho tiempo o quedarse bloqueada. Use la marca ForceRemove opcional para omitir la secuencia de cierre correcto y forzar la eliminación de la aplicación y de todos sus servicios.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --application-id [Obligatorio]| La identidad de la aplicación. Suele ser el nombre completo de la aplicación sin el esquema URI 'fabric:'. A partir de
                                 version 6.0, hierarchical names are delimited with the "~"
                                 character. For example, if the application name is
                                 "fabric://myapp/app1", the application identity would be
                                 "myapp~app1" in 6.0+ and "myapp/app1" in previous versions.|
| --force-remove | Elimina una aplicación o un servicio de Service Fabric de manera forzada sin pasar por la secuencia de apagado correcta. Este parámetro puede usarse para forzar la eliminación de una aplicación o un servicio cuya eliminación normal requiere un tiempo de espera a causa de problemas del código del servicio que impiden el cierre correcto de las réplicas.| | --timeout -t | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug                 | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h               | Muestra este mensaje de ayuda y sale.|
| --output -o             | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query                 | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose               | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-application-deployed"></a>sfctl application deployed
Obtiene la información sobre una aplicación implementada en un nodo de Service Fabric.|
|     
### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --application-id [Obligatorio]| La identidad de la aplicación. Suele ser el nombre completo de la aplicación sin el esquema URI 'fabric:'. A partir de
                                 version 6.0, hierarchical names are delimited with the "~"
                                 character. For example, if the application name is
                                 "fabric://myapp/app1", the application identity would be
                                 "myapp~app1" in 6.0+ and "myapp/app1" in previous versions.|
| --node-name [Obligatorio]| El nombre del nodo.| | --timeout -t | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug                 | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h               | Muestra este mensaje de ayuda y sale.|
| --output -o             | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query                 | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose               | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-application-health"></a>sfctl application health
Obtiene el estado de la aplicación de Service Fabric.

Devuelve el estado de mantenimiento de la aplicación de Service Fabric. La respuesta notifica un estado de mantenimiento correcto, con error o con una advertencia. Si la entidad no se encuentra en el almacén de estado, devuelve un error.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --application-id [Obligatorio]| La identidad de la aplicación. Suele ser el nombre completo de la aplicación sin el esquema URI 'fabric:'. A partir de la versión 6.0,
                                                 hierarchical names are delimited with the "~"
                                                 character. For example, if the application name is
                                                 "fabric://myapp/app1", the application identity
                                                 would be "myapp~app1" in 6.0+ and "myapp/app1" in
                                                 previous versions.|
| --deployed-applications-health-state-filter| Permite filtrar los objetos de estado de mantenimiento de las aplicaciones implementadas devueltas en el resultado de la consulta de estado de la aplicación según su estado de mantenimiento. Los valores posibles para este parámetro incluyen el valor entero de uno de los siguientes estados de mantenimiento. Solo se devolverán las aplicaciones implementadas que coincidan con el filtro. Todas las aplicaciones implementadas se utilizan para evaluar el estado de mantenimiento agregado. Si no se especifica, se devuelven todas las entradas. Los valores de estado se marcan según la enumeración, por lo que el valor puede ser una combinación de estos valores obtenidos mediante el operador bit a bit 'OR'.                        Por ejemplo, si el valor proporcionado es “6, se devuelve el estado de mantenimiento de las aplicaciones implementadas con el valor HealthState de Ok (2) y Warning (4). - Default: valor predeterminado. Coincide con cualquier HealthState.                        El valor predeterminado es cero. - None: filtro que no coincide con ningún valor de HealthState. Se utiliza para no devolver ningún resultado en una determinada colección de estados.                        El valor es 1. - Ok: filtro que asocia la entrada con el valor de HealthState de Ok. El valor es 2. - Warning: filtro que asocia la entrada con el valor de HealthState de Warning. El valor es 4. - Error: filtro que asocia la entrada con el valor de HealthState de Error. El valor es 8. - All: filtro que asocia la entrada con cualquier valor de HealthState. El valor es 65535.| | --events-health-state-filter | Permite filtrar la colección de objetos HealthEvent devueltos según el estado de mantenimiento. Los valores posibles para este parámetro incluyen el valor entero de uno de los siguientes estados de mantenimiento. Se devuelven únicamente los eventos que coinciden con el filtro. Todos los eventos se utilizan para evaluar el estado de mantenimiento agregado. Si no se especifica, se devuelven todas las entradas.                        Los valores de estado se marcan según la enumeración, por lo que el valor puede ser una combinación de estos valores obtenidos mediante el operador bit a bit 'OR'. Por ejemplo, si el valor proporcionado es 6, se devuelven todos los eventos con el valor HealthState de Ok (2) y Warning (4). - Default: valor predeterminado. Coincide con cualquier HealthState. El valor predeterminado es cero. - None: filtro que no coincide con ningún valor de HealthState. Se utiliza para no devolver ningún resultado en una determinada colección de estados. El valor es 1. - Ok: filtro que asocia la entrada con el valor de HealthState de Ok. El valor es 2. - Warning: filtro que asocia la entrada con el valor de HealthState de Warning. El valor es 4. - Error: filtro que asocia la entrada con el valor de HealthState de Error. El valor es 8. - All: filtro que asocia la entrada con cualquier valor de HealthState. El valor es 65535. .| |--exclude-health-statistics | Indica si las estadísticas de mantenimiento se deben devolver como parte del resultado de la consulta. El valor predeterminado es false. Las estadísticas muestran el número de entidades secundarias en estado de mantenimiento Ok, Warning y Error.| | --services-health-state-filter | Permite filtrar los objetos del estado de mantenimiento de los servicios devueltos en el resultado de la consulta del estado de mantenimiento según el estado de mantenimiento. Los valores posibles para este parámetro incluyen el valor entero de uno de los siguientes estados de mantenimiento. Se devuelven únicamente los servicios que coinciden con el filtro. Todos los servicios se utilizan para evaluar el estado de mantenimiento agregado.                        Si no se especifica, se devuelven todas las entradas. Los valores de estado se marcan según la enumeración, por lo que el valor puede ser una combinación de estos valores obtenidos mediante el operador bit a bit 'OR'. Por ejemplo, si el valor proporcionado es 6, se devuelve el estado de mantenimiento de los servicios con el valor HealthState de Ok (2) y Warning (4). - Default: valor predeterminado. Coincide con cualquier HealthState. El valor predeterminado es cero.                        - None: filtro que no coincide con ningún valor de HealthState. Se utiliza para no devolver ningún resultado en una determinada colección de estados. El valor es 1. - Ok: filtro que asocia la entrada con el valor de HealthState de Ok. El valor es 2. - Warning: filtro que asocia la entrada con el valor de HealthState de Warning. El valor es 4. - Error: filtro que asocia la entrada con el valor de HealthState de Error. El valor es 8. - All: filtro que asocia la entrada con cualquier valor de HealthState. El valor es 65535. | | --timeout -t | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug                                 | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h                               | Muestra este mensaje de ayuda y sale.|
| --output -o                             | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query                                 | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose                               | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-application-info"></a>sfctl application info
Obtiene la información sobre una aplicación de Service Fabric.

Devuelve la información sobre la aplicación que se creó o que está en proceso de creación en el clúster de Service Fabric y cuyo nombre coincide con el especificado como parámetro. La respuesta incluye el nombre, el tipo, el estado, los parámetros y otros detalles sobre la aplicación.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --application-id [Obligatorio]| La identidad de la aplicación. Suele ser el nombre completo de la aplicación sin el esquema URI 'fabric:'. A partir de la versión 6.0, los nombres jerárquicos se delimitan
                                      with the "~" character. For example, if the application name
                                      is "fabric://myapp/app1", the application identity would be
                                      "myapp~app1" in 6.0+ and "myapp/app1" in previous versions.|
| --exclude-application-parameters| La marca que especifica si los parámetros de la aplicación se excluirán del resultado.| | --timeout -t | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug                      | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h                    | Muestra este mensaje de ayuda y sale.|
| --output -o                  | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.             Valor predeterminado: json.|
| --query                      | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose                    | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-application-list"></a>sfctl application list
Obtiene la lista de aplicaciones creadas en el clúster de Service Fabric que coinciden con los filtros especificados como parámetro.

Obtiene la información sobre las aplicaciones que se crearon o que están en proceso de creación en el clúster de Service Fabric y que coinciden con los filtros especificados como parámetro. La respuesta incluye el nombre, el tipo, el estado, los parámetros y otros detalles sobre la aplicación. Si las aplicaciones no caben en una página, se devuelve una página de resultados, así como un token de continuación que puede usarse para obtener la página siguiente.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
|--application-definition-kind-filter| Se utiliza para filtrar por ApplicationDefinitionKind para
                                          application query operations. - Default - Default value.
                                          Filter that matches input with any
                                          ApplicationDefinitionKind value. The value is 0. - All -
                                          Filter that matches input with any
                                          ApplicationDefinitionKind value. The value is 65535. -
                                          ServiceFabricApplicationDescription - Filter that matches
                                          input with ApplicationDefinitionKind value
                                          ServiceFabricApplicationDescription. The value is 1. -
                                          Compose - Filter that matches input with
                                          ApplicationDefinitionKind value Compose. The value is 2.
                                          Default: 65535.|
| --application-type-name | El nombre del tipo de aplicación utilizado para filtrar las aplicaciones que se van a consultar. Este valor no debe contener la versión del tipo de aplicación.| | --continuation-token | El parámetro del token de continuación se utiliza para obtener el siguiente conjunto de resultados. Un token de continuación con un valor no vacío se incluye en la respuesta de la API cuando los resultados del sistema no caben en una única respuesta. Cuando este valor se pasa a la siguiente llamada API, la API devuelve el siguiente conjunto de resultados. Si no hay ningún resultado más, el token de continuación no contiene un valor. El valor de este parámetro no debe ser una dirección URL codificada.| | --exclude-application-parameters| La marca que especifica si los parámetros de la aplicación se excluirán del resultado.| | --timeout -t | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug                      | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h                    | Muestra este mensaje de ayuda y sale.|
| --output -o                  | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.             Valor predeterminado: json.|
| --query                      | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose                    | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-application-load"></a>sfctl application load
Obtiene la información de carga sobre una aplicación de Service Fabric.

        Returns the load information about the application that was created or in the process of
        being created in the Service Fabric cluster and whose name matches the one specified as the
        parameter. The response includes the name, minimum nodes, maximum nodes, the number of nodes
        the app is occupying currently, and application load metric information about the
        application.

### <a name="arguments"></a>Argumentos
|Argumento|Descripción|
| --- | --- |
|--application-id [Obligatorio]| La identidad de la aplicación. Suele ser el nombre completo de
                                 the application without the 'fabric:' URI scheme. Starting from
                                 version 6.0, hierarchical names are delimited with the "~"
                                 character. For example, if the application name is
                                 "fabric://myapp/app1", the application identity would be
                                 "myapp~app1" in 6.0+ and "myapp/app1" in previous versions. |
| --timeout -t | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales
|Argumento|Descripción|
| --- | --- |
|--debug                    | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
    --help -h                  | Muestra este mensaje de ayuda y sale.|
    --output -o                | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado:
                                 json.|
    --query                    | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más
                                 información y ejemplos.|
    --verbose                  | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-application-manifest"></a>sfctl application manifest
Obtiene el manifiesto que describe un tipo de aplicación.
        
Obtiene el manifiesto que describe un tipo de aplicación. La respuesta contiene el manifiesto de aplicación XML como una cadena.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --application-type-name [Obligatorio]| Nombre del tipo de aplicación.|
| --application-type-version [Obligatorio]| La versión del tipo de aplicación.|
| --timeout -t                      | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug                           | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h                         | Muestra este mensaje de ayuda y sale.|
| --output -o                       | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.                  Valor predeterminado: json.|
| --query                           | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose                         | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-application-provision"></a>sfctl application provision
Aprovisiona o registra un tipo de aplicación de Service Fabric en el clúster.
        
Aprovisiona o registra un tipo de aplicación de Service Fabric en el clúster. Es necesario para poder crear instancias de las aplicaciones nuevas.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --application-type-build-path [Obligatorio]| Ruta de acceso relativa del almacén de imágenes al paquete de aplicación.|
| --timeout -t                         | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug                              | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h                            | Muestra este mensaje de ayuda y sale.|
| --output -o                          | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query                              | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose                            | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-application-type"></a>sfctl application type

Obtiene la lista de tipos de aplicaciones del clúster de Service Fabric que coinciden exactamente con el nombre especificado.

Devuelve la información sobre los tipos de aplicaciones aprovisionados o en proceso de aprovisionamiento en el clúster de Service Fabric. Estos resultados son los tipos de aplicaciones cuyo nombre coincide exactamente con el especificado como parámetro y que coinciden con los parámetros de consulta establecidos. Se devuelven todas las versiones del tipo de aplicación que coinciden con el nombre del tipo de aplicación, y cada versión se devuelve como un tipo de aplicación. La respuesta incluye el nombre, la versión, el estado y otros detalles sobre el tipo de la aplicación. Se trata de una consulta paginada, lo que significa que, si no caben todos los tipos de aplicaciones en una página, se devuelve una página de resultados, así como un token de continuación que puede usarse para obtener la página siguiente. Por ejemplo, si hay diez tipos de aplicaciones, pero en una página solo caben tres o si el número máximo de resultados está establecido en tres, entonces solo se devuelven tres. Para acceder a los demás resultados, recupere las páginas siguientes con el token de continuación devuelto en la siguiente consulta. Si no existen páginas sucesivas, se devuelve un token de continuación vacío.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --application-type-name [Obligatorio]| Nombre del tipo de aplicación.|
| --continuation-token           | El parámetro del token de continuación se utiliza para obtener el siguiente conjunto de resultados. Un token de continuación con un valor no vacío se incluye en la respuesta de la API cuando los resultados del sistema no caben en una única respuesta. Cuando este valor se pasa a la siguiente llamada API, la API devuelve el siguiente conjunto de resultados. Si no hay ningún resultado más, el token de continuación no contiene un valor. El valor de este parámetro no debe ser la dirección URL codificada.|
| --exclude-application-parameters  | La marca que especifica si los parámetros de la aplicación se excluirán del resultado.|
| --max-results                  | El número máximo de resultados que se devuelven como parte de las consultas paginadas. Este parámetro define el límite superior en el número de resultados devueltos. Los resultados devueltos pueden ser menos que el número máximo de resultados especificado si no caben en el mensaje según las restricciones del tamaño máximo del mensaje definidas en la configuración. Si este parámetro es cero o no se especifica, la consulta paginada incluye tantos resultados como quepan en el mensaje devuelto.|
| --timeout -t                   | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug                        | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h                      | Muestra este mensaje de ayuda y sale.|
| --output -o                    | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.               Valor predeterminado: json.|
| --query                        | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose                      | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-application-unprovision"></a>sfctl application unprovision
Elimina un tipo de aplicación de Service Fabric del clúster o anula su registro.

Elimina un tipo de aplicación de Service Fabric del clúster o anula su registro. Esta operación solo puede realizarse si se han eliminado todas las instancias de la aplicación del tipo de aplicación. Una vez anulado el registro del tipo de aplicación, no se pueden crear instancias de la aplicación para este tipo de aplicación concreto.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --application-type-name [Obligatorio]| Nombre del tipo de aplicación.|
| --application-type-version [Obligatorio]| La versión del tipo de aplicación.|
| --timeout -t                      | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug                           | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h                         | Muestra este mensaje de ayuda y sale.|
| --output -o                       | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.                  Valor predeterminado: json.|
| --query                           | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose                         | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-application-upgrade"></a>sfctl application upgrade
Inicia la actualización de una aplicación en el clúster de Service Fabric.

Valida los parámetros de actualización de la aplicación proporcionados e inicia la actualización de la aplicación si los parámetros son válidos. Tenga en cuenta que la descripción de la actualización reemplaza la descripción existente. Esto significa que, si no se especifican los parámetros, los que ya existen en la aplicación se sobrescribirán con la lista de parámetros vacía. Esto da lugar a que la aplicación use el valor predeterminado de los parámetros del manifiesto de aplicación.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --app-id [Obligatorio]| La identidad de la aplicación. Suele ser el nombre completo de la aplicación sin el esquema URI 'fabric:'. A partir de la versión 6.0, los nombres jerárquicos se delimitan con el carácter "~". Para
        example, if the application name is 'fabric://myapp/app1', the application identity would be
        'myapp~app1' in 6.0+ and 'myapp/app1' in previous versions.|
| --app-version [Obligatorio]| Versión de la aplicación de destino.| | --parameters [Obligatorio]| Una lista codificada de JSON con los reemplazos de los parámetros de la aplicación que se aplicarán al actualizar la aplicación.| | --default-service-health-policy| La especificación codificada de JSON de la directiva de estado utilizada de forma predeterminada para evaluar el estado de un tipo de servicio.| | --failure-action | La acción que se ejecutará cuando una actualización supervisada detecta infracciones de la directiva de mantenimiento o de la directiva de supervisión.| | --force-restart | Reinicio forzado de los procesos durante la actualización incluso cuando la versión del código no ha cambiado.| | --health-check-retry-timeout| La cantidad de tiempo para reintentar las evaluaciones de estado cuando la aplicación o el clúster tienen un estado incorrecto antes de ejecutar la acción que genera el error. Se mide en milisegundos.  Valor predeterminado: PT0H10M0S.| | --health-check-stable-duration | La cantidad de tiempo que la aplicación o el clúster deben tener un estado correcto antes de que la actualización continúe con el siguiente dominio de actualización.            Se mide en milisegundos.  Valor predeterminado: PT0H2M0S.| | --health-check-wait-duration| La cantidad de tiempo de espera después de completar un dominio de actualización que debe transcurrir antes de aplicar directivas de mantenimiento. Se mide en milisegundos.            Valor predeterminado: 0.| | --max-unhealthy-apps | El porcentaje máximo permitido de aplicaciones implementadas con un estado incorrecto. Se representa con un número entre 0 y 100.| | --mode | El modo utilizado para supervisar el estado durante una actualización gradual.            Valor predeterminado: UnmonitoredAuto.| | --replica-set-check-timeout | El período de tiempo máximo para bloquear el procesamiento de un dominio de actualización y evitar la pérdida de disponibilidad cuando hay problemas inesperados. Se mide en segundos.| | --service-health-policy | Mapa codificado de JSON con una directiva de mantenimiento de tipo de servicio por cada nombre de tipo de servicio. El mapa está vacío de forma predeterminada.| | --timeout -t | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.| | --upgrade-domain-timeout | El período de tiempo del que dispone cada dominio de actualización para completarse antes de la ejecución de FailureAction. Se mide en milisegundos.  Valor predeterminado: P10675199DT02H48M05.4775807S.| | --upgrade-timeout | El período de tiempo del que dispone la actualización global para completarse antes de la ejecución de FailureAction. Se mide en milisegundos.  Valor predeterminado: P10675199DT02H48M05.4775807S.| | --warning-as-error | Tratar las advertencias de evaluación de estado con la misma gravedad que los errores.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug                     | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h                   | Muestra este mensaje de ayuda y sale.|
| --output -o                 | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.            Valor predeterminado: json.|
| --query                     | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose                   | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-application-upload"></a>sfctl application upload
Copia un paquete de aplicación de Service Fabric en el almacén de imágenes.

Opcionalmente, se muestra el progreso de carga de cada archivo en el paquete. El progreso de carga se envía a `stderr`.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --path [Obligatorio]| Ruta de acceso al paquete de aplicación local.|
|--imagestore-string| Almacén de imágenes de destino en el que cargar el paquete de aplicación.  Valor predeterminado:
                         fabric:ImageStore.|
| --show-progress  | Muestra el progreso de la carga de archivos para paquetes grandes.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug       | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h     | Muestra este mensaje de ayuda y sale.|
| --output -o   | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query       | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y
                       ejemplos.|
| --verbose     | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="next-steps"></a>Pasos siguientes
- [Configuración](service-fabric-cli.md) de la CLI de Service Fabric.
- Obtenga información sobre cómo utilizar la CLI de Service Fabric con los [scripts de ejemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).