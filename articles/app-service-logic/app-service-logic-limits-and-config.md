---
title: "Límites y configuración de Logic Apps | Microsoft Docs"
description: "Información general de los límites de servicio y los valores de configuración disponibles para Logic Apps."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: dwrede
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 47c13d5cbaeb114da18b710c52e2c989df4c0e01
ms.openlocfilehash: 2417d4e3d8033ff8d66ace8b27f398266a7fd101


---
# <a name="logic-app-limits-and-configuration"></a>Límites y configuración de Logic Apps

A continuación se muestra información sobre los límites actuales y los detalles de configuración de Azure Logic Apps.

## <a name="limits"></a>Límites

### <a name="http-request-limits"></a>Límites de solicitudes HTTP

Son los límites de una única solicitud HTTP o llamada a un conector.

#### <a name="timeout"></a>Tiempo de espera

|Nombre|Límite|Notas|
|----|----|----|
|Tiempo de espera de solicitud|90 segundos|Un [patrón asincrónico](app-service-logic-create-api-app.md) o un [bucle until](app-service-logic-loops-and-scopes.md) se pueden compensar, según sea necesario.|

#### <a name="message-size"></a>Tamaño del mensaje

|Nombre|Límite|Notas|
|----|----|----|
|Tamaño del mensaje|50 MB|Puede que algunos conectores y API no admitan 50 MB |
|Límite de evaluación de expresiones|131 072 caracteres.|`@concat()`, `@base64()` y `string` no pueden tener un valor más largo.|

#### <a name="retry-policy"></a>Directiva de reintentos

|Nombre|Límite|Notas|
|----|----|----|
|Número de reintentos|4|Se puede configurar con el [parámetro de directiva de reintento](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Retraso máximo entre reintentos|1 hora|Se puede configurar con el [parámetro de directiva de reintento](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Retraso mínimo entre reintentos|20 segundos|Se puede configurar con el [parámetro de directiva de reintento](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|

### <a name="run-duration-and-retention"></a>Retención y duración de ejecución

Son los límites de ejecución de una única aplicación lógica.

|Nombre|Límite|Notas|
|----|----|----|
|Duración de la ejecución|90 días||
|Retención de almacenamiento|90 días|Se cuenta a partir de la hora de inicio de la ejecución.|
|Intervalo de periodicidad mínima|1 segundo|| 15 segundos para aplicaciones lógicas con un plan de App Service
|Intervalo de periodicidad máxima|500 días||


### <a name="looping-and-debatching-limits"></a>Límites de bucle y anulación de procesamiento por lotes

Son los límites de ejecución de una única aplicación lógica.

|Nombre|Límite|Notas|
|----|----|----|
|Elementos ForEach|5.000|Puede usar la [acción de consulta](../connectors/connectors-native-query.md) para filtrar matrices más grandes, según sea necesario.|
|Iteraciones Until|5.000||
|Elementos SplitOn|5.000||
|Paralelismo de ForEach|20 ||Puede establecerlo en un elemento foreach secuencial si agrega `"operationOptions": "Sequential"` a la acción `foreach`.|


### <a name="throughput-limits"></a>Límites de rendimiento

Son los límites de una instancia de aplicación lógica. 

|Nombre|Límite|Notas|
|----|----|----|
|Desencadenadores por segundo|100|Puede distribuir los flujos de trabajo entre varias aplicaciones, según sea necesario.|

### <a name="definition-limits"></a>Límites de definición

Son los límites de una definición de aplicación lógica.

|Nombre|Límite|Notas|
|----|----|----|
|Acciones por flujo de trabajo|250|Puede agregar flujos de trabajo anidados para ampliar esta función, según sea necesario.|
|Niveles de anidamiento de acciones permitidos|5|Puede agregar flujos de trabajo anidados para ampliar esta función, según sea necesario.|
|Flujos por región y suscripción|1000||
|Desencadenadores por flujo de trabajo|10||
|Número máximo de caracteres por expresión|8192||
|Tamaño máximo de `trackedProperties` expresado en caracteres|16 000|
|`action`/`trigger` |80||
|`description` |256||
|`parameters` limit|50||
|`outputs` limit|10||

### <a name="integration-account-limits"></a>Límites de cuenta de integración

Hay límites para los artefactos que se agregan a la cuenta de integración

|Nombre|Límite|Notas|
|----|----|----|
|Esquema|8 MB|Puede usar el [identificador URI del blob](app-service-logic-enterprise-integration-schemas.md) para cargar archivos mayores de 2 MB |
|Asignación (archivo XSLT)|2 MB| |

### <a name="b2b-protocols-as2-x12-edifact-message-size"></a>Tamaño de mensaje de protocolos B2B (AS2, X12 y EDIFACT)

Son los límites de los protocolos B2B

|Nombre|Límite|Notas|
|----|----|----|
|AS2|50 MB|Aplicable para descodificar y codificar|
|X12|50 MB|Aplicable para descodificar y codificar|
|EDIFACT|50 MB|Aplicable para descodificar y codificar|

## <a name="configuration"></a>Configuración

### <a name="ip-address"></a>Dirección IP

Las llamadas realizadas desde una [conector](../connectors/apis-list.md) procederán de la dirección IP especificada a continuación.

Las llamadas realizadas directamente desde una aplicación lógica (es decir, a través de [HTTP](../connectors/connectors-native-http.md) o [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)) pueden proceder de cualquiera de los [intervalos de direcciones IP de centro de datos de Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

|Región de aplicación lógica|IP de salida|
|-----|----|
|Australia Oriental|40.126.251.213|
|Sudeste de Australia|40.127.80.34|
|Sur de Brasil|191.232.38.129|
|India Central|104.211.98.164|
|Central EE. UU.:|40.122.49.51|
|Asia oriental|23.99.116.181|
|Este de EE. UU.|191.237.41.52|
|Este de EE. UU. 2|104.208.233.100|
|Este de Japón|40.115.186.96|
|Oeste de Japón|40.74.130.77|
|Centro-Norte de EE. UU|65.52.218.230|
|Europa del Norte|104.45.93.9|
|Centro-Sur de EE. UU|104.214.70.191|
|Sudeste asiático|13.76.231.68|
|Sur de la India|104.211.227.225|
|Europa occidental|40.115.50.13|
|Oeste de la India|104.211.161.203|
|Oeste de EE. UU.|104.40.51.248|


## <a name="next-steps"></a>Pasos siguientes  

- Para comenzar con las aplicaciones lógicas, siga el tutorial [Creación de una aplicación lógica](app-service-logic-create-a-logic-app.md) .  
- [Ejemplos de aplicaciones lógicas y escenarios comunes](app-service-logic-examples-and-scenarios.md)
- [Con las aplicaciones lógicas puede automatizar procesos empresariales.](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Obtenga información acerca de cómo integrar sus sistemas con Aplicaciones lógicas](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Nov16_HO3-->


