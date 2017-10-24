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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: 336e74d8f69cb04e6bd0e85fc68ba38b218fabae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-chaos"></a>sfctl chaos
Inicie, detenga e informe sobre el servicio de prueba de Chaos.

## <a name="commands"></a>Comandos:

|Comando|Descripción|
| --- | --- |
|    report| Obtiene el siguiente segmento del informe de Chaos basado en el token de continuación pasado o en el intervalo de tiempo pasado.|
|    start | Si Chaos no está aún en ejecución en el clúster, comienza a ejecutar Chaos con los parámetros de Chaos especificados.|
|    stop  | Detiene Chaos en el clúster si ya se está ejecutando, en caso contrario, no realiza ninguna acción.|


## <a name="sfctl-chaos-report"></a>sfctl chaos report
Obtiene el siguiente segmento del informe de Chaos basado en el token de continuación pasado o en el intervalo de tiempo pasado.

Se puede especificar ContinuationToken para obtener el siguiente segmento del informe de Chaos, o bien se puede especificar el intervalo a través de StartTimeUtc y EndTimeUtc, pero no se pueden especificar ContinuationToken y el intervalo de tiempo en la misma llamada. Cuando hay más de 100 eventos de Chaos, el informe de Chaos se devuelve en segmentos y ningún segmento contiene más de 100 eventos de Chaos. 

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --continuation-token| El parámetro continuation token se utiliza para obtener el siguiente conjunto de resultados. Un continuation token con un valor no vacío se incluye en la respuesta de la API cuando los resultados del sistema no caben en una única respuesta. Cuando este valor se pasa a la siguiente llamada de la API, la API devuelve el siguiente conjunto de resultados. Si no hay ningún resultado más, el continuation token no contiene un valor. El valor de este parámetro no debe ser la dirección URL codificada.|
| --end-time-utc   | El recuento de tics que representa la hora de finalización del intervalo de tiempo para el que se genera un informe de Chaos. Consulte la [propiedad DateTime.Ticks](https://msdn.microsoft.com/en-us/library/system.datetime.ticks%28v=vs.110%29) para más detalles sobre los tics.|
| --start-time-utc | El recuento de tics que representa la hora de inicio del intervalo de tiempo para el que se genera un informe de Chaos. Consulte la [propiedad DateTime.Ticks](https://msdn.microsoft.com/en-us/library/system.datetime.ticks%28v=vs.110%29) para más detalles sobre los tics.|
| --timeout -t     | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug          | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h        | Muestra este mensaje de ayuda y sale.|
| --output -o      | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query          | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose        | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="sfctl-chaos-start"></a>sfctl chaos start
Si Chaos no está aún en ejecución en el clúster, comienza a ejecutar Chaos con los parámetros de Chaos especificados.

### <a name="arguments"></a>Argumentos

|Argumento|Descripción|
| --- | --- |
| --app-type-health-policy-map  | Lista codificada por JSON con porcentaje máximo de aplicaciones en mal estado para los tipos de aplicación específicos. Cada entrada especifica como una clave el nombre del tipo de aplicación y como un valor un entero que representa el porcentaje de MaxPercentUnhealthyApplications usado para evaluar las aplicaciones del tipo de aplicación especificado.|
| --disable-move-replica-faults | Deshabilita los errores de mover principal y mover secundario.|
| --max-cluster-stabilization| La cantidad máxima de tiempo de espera para que todas las entidades del clúster se vuelvan estables y en buen estado.  Valor predeterminado: 60.|
| --max-concurrent-faults    | El número máximo de errores simultáneos inducidos por iteración.           Valor predeterminado: 1.|
| --max-percent-unhealthy-apps  | Al evaluar el estado del clúster durante Chaos, el porcentaje máximo permitido de aplicaciones en mal estado antes de informar de un error.|
| --max-percent-unhealthy-nodes | Al evaluar el estado del clúster durante Chaos, el porcentaje máximo permitido de nodos en mal estado antes de informar de un error.|
| --time-to-run              | Tiempo total (en segundos) para el que se ejecutará Chaos antes de la detención automática. El valor máximo permitido es 4.294.967.295 (System.UInt32.MaxValue).  Valor predeterminado:4294967295.|
| --timeout -t               | Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|
| --wait-time-between-faults | Tiempo de espera (en segundos) entre errores consecutivos dentro de una única iteración.  Valor predeterminado: 20.|
| --wait-time-between-iterations| Separación de tiempo (en segundos) entre dos iteraciones consecutivas de Chaos.  Valor predeterminado: 30.|
| --warning-as-error         | Al evaluar el estado del clúster durante Chaos, trata las advertencias con el mismo nivel de gravedad que los errores.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
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

|Argumento|Descripción|
| --- | --- |
| --timeout -t| Tiempo de espera del servidor en segundos.  Valor predeterminado: 60.|

### <a name="global-arguments"></a>Argumentos globales

|Argumento|Descripción|
| --- | --- |
| --debug  | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración.|
| --help -h| Muestra este mensaje de ayuda y sale.|
| --output -o | Formato de salida.  Valores permitidos: json, jsonc, table y tsv.  Valor predeterminado: json.|
| --query  | Cadena de consulta de JMESPath. Consulte http://jmespath.org/ para obtener más información y ejemplos.|
| --verbose| Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.|

## <a name="next-steps"></a>Pasos siguientes
- [Configuración](service-fabric-cli.md) de la CLI de Service Fabric.
- Obtenga información sobre cómo utilizar la CLI de Service Fabric con los [scripts de ejemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).