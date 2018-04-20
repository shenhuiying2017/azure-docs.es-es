---
title: 'CLI de Azure Service Fabric: sfctl chaos | Microsoft Docs'
description: Describe los comandos de sfctl chaos de la CLI Service Fabric.
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
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: 34e4d47b1de509c2053996d9d1078733d7055447
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-chaos"></a>sfctl chaos
Inicie, detenga e informe sobre el servicio de prueba de Chaos.

## <a name="commands"></a>Comandos:

|Get-Help|DESCRIPCIÓN|
| --- | --- |
|    report| Obtiene el siguiente segmento del informe de Chaos basado en el token de continuación pasado o en el intervalo de tiempo pasado.|
|    start | Inicia Chaos en el clúster.|
|    stop  | Detiene Chaos en el clúster si ya se está ejecutando, en caso contrario, no realiza ninguna acción.|


## <a name="sfctl-chaos-report"></a>sfctl chaos report
Obtiene el siguiente segmento del informe de Chaos basado en el token de continuación pasado o en el intervalo de tiempo pasado.

Se puede especificar ContinuationToken para obtener el siguiente segmento del informe de Chaos, o bien se puede especificar el intervalo a través de StartTimeUtc y EndTimeUtc, pero no se pueden especificar ContinuationToken y el intervalo de tiempo en la misma llamada. Cuando hay más de 100 eventos de Chaos, el informe de Chaos se devuelve en segmentos y ningún segmento contiene más de 100 eventos de Chaos. 

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --continuation-token| El parámetro continuation token se utiliza para obtener el siguiente conjunto de resultados. Un continuation token con un valor no vacío se incluye en la respuesta de la API cuando los resultados del sistema no caben en una única respuesta. Cuando este valor se pasa a la siguiente llamada de la API, la API devuelve el siguiente conjunto de resultados. Si no hay ningún resultado más, el continuation token no contiene un valor. El valor de este parámetro no debe ser la dirección URL codificada.|
| --end-time-utc   | El valor de hora del archivo de Windows que representa la hora de finalización del intervalo de tiempo para el que se genera un informe de Chaos. Consulte [Método DateTime.ToFileTimeUtc](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) para más información.|
| --start-time-utc | El valor de hora del archivo de Windows que representa la hora de inicio del intervalo de tiempo para el que se genera un informe de Chaos. Consulte [Método DateTime.ToFileTimeUtc](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) para más información.|
| --timeout -t     | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug          | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h        | Muestra este mensaje de ayuda y sale.|
| --output -o      | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query          | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose        | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-chaos-start"></a>sfctl chaos start
Inicia Chaos en el clúster.

Si Chaos no está aún en ejecución en el clúster, inicia Chaos con los parámetros de Chaos especificados. Si ya se está ejecutando Chaos cuando se realiza esta llamada, se produce un error en la llamada con el código de error FABRIC_E_CHAOS_ALREADY_RUNNING.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --app-type-health-policy-map  | Lista codificada por JSON con porcentaje máximo de aplicaciones en mal estado para los tipos de aplicación específicos. Cada entrada especifica como una clave el nombre del tipo de aplicación y como un valor un entero que representa el porcentaje de MaxPercentUnhealthyApplications usado para evaluar las aplicaciones del tipo de aplicación especificado. Define un asignación con un porcentaje máximo de aplicaciones en mal estado para tipos de aplicación específicos. Cada entrada especifica como clave el nombre del tipo de aplicación y como valor un entero que representa el porcentaje de MaxPercentUnhealthyApplications usado para evaluar las aplicaciones del tipo de aplicación especificado. La asignación de directiva de mantenimiento de tipo de aplicación se puede usar durante la evaluación de mantenimiento del clúster para describir tipos de aplicación especiales. Los tipos de aplicación incluidos en la asignación se evalúan con el porcentaje especificado en la asignación y no con el MaxPercentUnhealthyApplications global definido en la directiva de mantenimiento de clúster. Las aplicaciones de tipos de aplicación especificados en la asignación no se incluyen en el grupo global de aplicaciones. Por ejemplo, si algunas aplicaciones de un tipo son vitales, el administrador de clústeres puede agregar una entrada a la asignación para ese tipo de aplicación y asignarle un valor del 0 % (es decir, no tolerar errores). Todas las demás aplicaciones se pueden evaluar con MaxPercentUnhealthyApplications establecido en el 20 % para tolerar algunos errores entre las miles de instancias de la aplicación. La asignación de directiva de mantenimiento de tipo de aplicación se usa únicamente si el manifiesto de clúster permite la evaluación de mantenimiento de tipo de aplicación con la entrada de configuración para HealthManager/EnableApplicationTypeHealthEvaluation.|
|--chaos-target-filter         | Diccionario JSON codificado con dos claves de tipo de cadena. Las dos claves son NodeTypeInclusionList y ApplicationInclusionList. Los valores para estas dos claves son lista de cadena. chaos_target_filter define todos los filtros para errores de destino de Chaos, por ejemplo, solo determinados tipos de nodo con errores o con solo determinadas aplicaciones con errores. Si no se utiliza chaos_target_filter, Chaos produce un error en todas las entidades de clúster. Si se utiliza chaos_target_filter, Chaos produce un error solo en las entidades que cumplen la especificación de chaos_target_filter. NodeTypeInclusionList y ApplicationInclusionList solo permiten semánticas de unión. No es posible especificar una intersección de NodeTypeInclusionList y ApplicationInclusionList. Por ejemplo, no es posible especificar "Error de esta aplicación solo si se encuentra en ese tipo de nodo". Una vez que una entidad se incluye en NodeTypeInclusionList o ApplicationInclusionList, dicha entidad no se puede excluir mediante ChaosTargetFilter. Aunque la applicationX no aparezca en ApplicationInclusionList, en alguna iteración de Chaos, la applicationX puede dar error porque se produce en un nodo de nodeTypeY que se incluye en NodeTypeInclusionList. Si NodeTypeInclusionList y ApplicationInclusionList están vacías, se produce una excepción ArgumentException. Todos los tipos de errores (reiniciar el nodo, reiniciar el paquete de código, quitar la réplica, reiniciar la réplica, mover principal y mover secundaria) están habilitados para los nodos de estos tipos de nodo. Si un valor tipo de nodo (es decir, NodeTypeX) no aparece en NodeTypeInclusionList, nunca se habilitarán los errores de nivel de nodo (por ejemplo, NodeRestart) para los nodos de NodeTypeX, pero los errores de la réplica y del paquete de código todavía pueden habilitarse para NodeTypeX si se da que una aplicación de ApplicationInclusionList reside en un nodo de NodeTypeX. Como máximo pueden incluirse 100 nombres de tipo de nodo en esta lista. Para aumentar este número, es necesaria una actualización de configuración para la configuración de MaxNumberOfNodeTypesInChaosEntityFilter. Todas las réplicas que pertenecen a los servicios de estas aplicaciones son susceptibles de errores de réplica (reiniciar réplica, quitar réplica, mover principal y mover secundaria) debido a Chaos. Chaos puede reiniciar un paquete de código solo si el paquete de código hospeda réplicas solamente de estas aplicaciones. Si no aparece una aplicación en esta lista, todavía puede registrar un error en alguna iteración de Chaos si la aplicación termina en un nodo de un tipo de nodo que está incluido en NodeTypeInclusionList. Sin embargo, si la applicationX está enlazada a nodeTypeY a través de las restricciones de posición y la applicationX no se encuentra en la ApplicationInclusionList y nodeTypeY no está presente en NodeTypeInclusionList, la applicationX nunca generará un error. Como máximo pueden incluirse 1000 nombres de aplicaciones en esta lista. Para aumentar este número, es necesaria una actualización de configuración para la configuración de MaxNumberOfApplicationsInChaosEntityFilter.|
|--context                     | Asignación JSON codificada de pares de tipo clave-valor (cadena, cadena). El mapa se puede usar para registrar información acerca de la ejecución de Chaos. No puede haber más de 100 de dicho pares y cada cadena (clave o valor) puede tener una longitud máxima de 4095. Este mapa lo establece el inicio de la ejecución de Chaos para almacenar, opcionalmente, el contexto de la ejecución específica.|
| --disable-move-replica-faults | Deshabilita los errores de mover principal y mover secundario.|
| --max-cluster-stabilization| La cantidad máxima de tiempo de espera para que todas las entidades del clúster se vuelvan estables y en buen estado.  Valor predeterminado: 60. Chaos se ejecuta en las iteraciones y al inicio de cada iteración valida el estado de las entidades de clúster. Durante la validación si una entidad de clúster no es estable y está en buen estado dentro de MaxClusterStabilizationTimeoutInSeconds, Chaos genera un evento de error de validación.|
| --max-concurrent-faults    | El número máximo de errores simultáneos inducidos por iteración.           Valor predeterminado: 1. Chaos se ejecuta en las iteraciones y dos iteraciones consecutivas están separadas por una fase de validación. Cuanto mayor sea la simultaneidad, más agresiva será la inserción de errores, lo que induce una serie más compleja de estados para detectar errores. La recomendación es comenzar con un valor de 2 o 3 y tener cuidado al mover hacia arriba.|
| --max-percent-unhealthy-apps  | Al evaluar el estado del clúster durante Chaos, el porcentaje máximo permitido de aplicaciones en mal estado antes de informar de un error. El porcentaje máximo permitido de aplicaciones en mal estado antes de informar de un error. Por ejemplo, para permitir el 10 % de las aplicaciones en mal estado, este valor sería 10. El valor representa el porcentaje máximo tolerado de aplicaciones que pueden ser incorrectas antes de que el clúster se considere erróneo. Si se respeta el porcentaje, pero hay al menos una aplicación en mal estado, el estado se evalúa como Warning. Esto se calcula dividiendo el número de aplicaciones en mal estado sobre el número total de instancias de la aplicación en el clúster, excluidas las aplicaciones de los tipos de aplicación que se incluyen en ApplicationTypeHealthPolicyMap. El cálculo se redondea hacia arriba para tolerar un error en números reducidos de aplicaciones. El porcentaje predeterminado es cero.|
| --max-percent-unhealthy-nodes | Al evaluar el estado del clúster durante Chaos, el porcentaje máximo permitido de nodos en mal estado antes de informar de un error. El porcentaje máximo permitido de nodos en mal estado antes de informar de un error. Por ejemplo, para permitir el 10 % de los nodos en mal estado, este valor sería 10. El valor representa el porcentaje máximo tolerado de nodos que pueden ser incorrectos antes de que el clúster se considere erróneo. Si se respeta el porcentaje, pero hay al menos un nodo en mal estado, el estado se evalúa como Warning. El porcentaje se calcula dividiendo el número de nodos en mal estado sobre el número total de nodos del clúster. El cálculo se redondea hacia arriba para tolerar un error en números reducidos de nodos. El porcentaje predeterminado es cero. En los clústeres grandes, siempre habrá nodos inactivos o inoperativos debido a reparaciones, por lo que este porcentaje debe configurarse para tolerar ese hecho.|
| --time-to-run              | Tiempo total (en segundos) para el que se ejecutará Chaos antes de la detención automática. El valor máximo permitido es 4.294.967.295 (System.UInt32.MaxValue).  Valor predeterminado:4294967295.|
| --timeout -t               | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|
| --wait-time-between-faults | Tiempo de espera (en segundos) entre errores consecutivos dentro de una única iteración.  Valor predeterminado: 20. Cuanto mayor sea el valor, menor será la superposición entre errores y más sencilla será la secuencia de transiciones de estado por las que pasa el clúster. La recomendación es comenzar con un valor entre 1 y 5, y tener cuidado al mover hacia arriba.|
| --wait-time-between-iterations| Separación de tiempo (en segundos) entre dos iteraciones consecutivas de Chaos. Cuanto mayor sea el valor, menor será la tasa de inserción de errores. Valor predeterminado: 30.|
| --warning-as-error         | Al evaluar el estado del clúster durante Chaos, trata las advertencias con el mismo nivel de gravedad que los errores.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug                    | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h                  | Muestra este mensaje de ayuda y sale.|
| --output -o                | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.           Valor predeterminado: json.|
| --query                    | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose                  | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-chaos-stop"></a>sfctl chaos stop
Detiene Chaos en el clúster si ya se está ejecutando, en caso contrario, no realiza ninguna acción.

Detiene Chaos de programar más errores; sin embargo, los errores en proceso no se ven afectados.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --timeout -t| Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug  | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h| Muestra este mensaje de ayuda y sale.|
| --output -o | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query  | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose| Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="next-steps"></a>pasos siguientes
- [Configuración](service-fabric-cli.md) de la CLI de Service Fabric.
- Obtenga información sobre cómo utilizar la CLI de Service Fabric con los [scripts de ejemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).