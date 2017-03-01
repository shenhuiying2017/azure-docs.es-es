---
title: "Límites y configuración de Logic Apps | Microsoft Docs"
description: "Información general de los límites de servicio y los valores de configuración disponibles para Logic Apps."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 084919e9dbe3647c45cf67e6836a8c9de6a5cd70
ms.openlocfilehash: 307a70b5efb22642e54212d8d3517bf5d9070505
ms.lasthandoff: 02/17/2017


---
# <a name="logic-app-limits-and-configuration"></a>Límites y configuración de Logic Apps

A continuación se muestra información sobre los límites actuales y los detalles de configuración de Azure Logic Apps.

## <a name="limits"></a>Límites

### <a name="http-request-limits"></a>Límites de solicitudes HTTP

Son los límites de una única solicitud HTTP o llamada a un conector.

#### <a name="timeout"></a>Tiempo de espera

|Nombre|Límite|Notas|
|----|----|----|
|Tiempo de espera de solicitud|90 segundos|Un [patrón asincrónico](../logic-apps/logic-apps-create-api-app.md) o un [bucle until](logic-apps-loops-and-scopes.md) se pueden compensar, según sea necesario.|

#### <a name="message-size"></a>Tamaño del mensaje

|Nombre|Límite|Notas|
|----|----|----|
|Tamaño del mensaje|50 MB|Puede que algunos conectores y API no admitan 50 MB |
|Límite de evaluación de expresiones|131&072; caracteres.|`@concat()`, `@base64()` y `string` no pueden tener un valor más largo.|

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
|Ejecuciones de acciones por segundo (ráfagas)|1000|Puede distribuir cargas de trabajo entre varias aplicaciones, según sea necesario.|
|Ejecuciones de acciones por hora (sostenidas)|1&000;&000;|Puede distribuir cargas de trabajo entre varias aplicaciones, según sea necesario.|

### <a name="definition-limits"></a>Límites de definición

Son los límites de una definición de aplicación lógica.

|Nombre|Límite|Notas|
|----|----|----|
|Acciones por flujo de trabajo|250|Puede agregar flujos de trabajo anidados para ampliar esta función, según sea necesario.|
|Niveles de anidamiento de acciones permitidos|5|Puede agregar flujos de trabajo anidados para ampliar esta función, según sea necesario.|
|Flujos por región y suscripción|1000||
|Desencadenadores por flujo de trabajo|10||
|Número máximo de caracteres por expresión|8192||
|Tamaño máximo de `trackedProperties` expresado en caracteres|16&000;|
|`action`/`trigger` |80||
|`description` |256||
|`parameters` limit|50||
|`outputs` limit|10||

### <a name="integration-account-limits"></a>Límites de cuenta de integración

Hay límites para los artefactos que se agregan a la cuenta de integración

|Nombre|Límite|Notas|
|----|----|----|
|Esquema|8 MB|Puede usar el [identificador URI del blob](logic-apps-enterprise-integration-schemas.md) para cargar archivos mayores de 2 MB |
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

#### <a name="logic-app-service"></a>Servicio de aplicación lógica

Las llamadas realizadas desde una aplicación lógica directamente (es decir, a través de [HTTP](../connectors/connectors-native-http.md) o [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)) o de otras solicitudes HTTP procederán de la dirección IP especificada a continuación:

|Región de aplicación lógica|IP de salida|
|-----|----|
|Australia Oriental|13.75.153.66, 104.210.89.222, 104.210.89.244, 13.75.149.4, 104.210.91.55, 104.210.90.241|
|Sudeste de Australia|13.73.115.153, 40.115.78.70, 40.115.78.237, 13.73.114.207, 13.77.3.139, 13.70.159.205|
|Sur de Brasil|191.235.86.199, 191.235.95.229, 191.235.94.220, 191.235.82.221, 191.235.91.7, 191.234.182.26|
|India Central|52.172.157.194, 52.172.184.192, 52.172.191.194, 52.172.154.168, 52.172.186.159, 52.172.185.79|
|Central EE. UU.:|13.67.236.76, 40.77.111.254, 40.77.31.87, 13.67.236.125, 104.208.25.27, 40.122.170.198|
|Asia oriental|168.63.200.173, 13.75.89.159, 23.97.68.172, 13.75.94.173, 40.83.127.19, 52.175.33.254|
|Este de EE. UU.|137.135.106.54, 40.117.99.79, 40.117.100.228, 13.92.98.111, 40.121.91.41, 40.114.82.191|
|Este de EE. UU. 2|40.84.25.234, 40.79.44.7, 40.84.59.136, 40.84.30.147, 104.208.155.200, 104.208.158.174|
|Este de Japón|13.71.146.140, 13.78.84.187, 13.78.62.130, 13.71.158.3, 13.73.4.207, 13.71.158.120|
|Oeste de Japón|40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.140.4, 104.214.137.243, 138.91.26.45|
|Centro-Norte de EE. UU|168.62.249.81, 157.56.12.202, 65.52.211.164, 168.62.248.37, 157.55.210.61, 157.55.212.238|
|Europa del Norte|13.79.173.49, 52.169.218.253, 52.169.220.174, 40.113.12.95, 52.178.165.215, 52.178.166.21|
|Centro-Sur de EE. UU|13.65.98.39, 13.84.41.46, 13.84.43.45, 104.210.144.48, 13.65.82.17, 13.66.52.232|
|Sudeste asiático|52.163.93.214, 52.187.65.81, 52.187.65.155, 13.76.133.155, 52.163.228.93, 52.163.230.166|
|Sur de la India|52.172.9.47, 52.172.49.43, 52.172.51.140, 52.172.50.24, 52.172.55.231, 52.172.52.0|
|Europa occidental|13.95.155.53, 52.174.54.218, 52.174.49.6, 40.68.222.65, 40.68.209.23, 13.95.147.65|
|Oeste de la India|104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.164.80, 104.211.162.205, 104.211.164.136|
|Oeste de EE. UU.|52.160.90.237, 138.91.188.137, 13.91.252.184, 52.160.92.112, 40.118.244.241, 40.118.241.243|

#### <a name="connectors"></a>Conectores

Las llamadas realizadas desde un [conector](../connectors/apis-list.md) procederán de la dirección IP especificada a continuación:

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

- Para comenzar con las aplicaciones lógicas, siga el tutorial [Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md) .  
- [Ejemplos de aplicaciones lógicas y escenarios comunes](../logic-apps/logic-apps-examples-and-scenarios.md)
- [Con las aplicaciones lógicas puede automatizar procesos empresariales.](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Obtenga información acerca de cómo integrar sus sistemas con Aplicaciones lógicas](http://channel9.msdn.com/Events/Build/2016/P462)

