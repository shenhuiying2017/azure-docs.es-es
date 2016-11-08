---
title: Límites y configuración de aplicaciones lógicas | Microsoft Docs
description: Información general de los límites de servicio y los valores de configuración disponibles para Logic Apps.
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: dwrede
editor: ''

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/22/2016
ms.author: jehollan

---
# Límites y configuración de aplicaciones lógicas
A continuación se muestra información sobre los límites actuales y los detalles de configuración de Azure Logic Apps.

## Límites
### Límites de solicitudes HTTP
Son los límites de una única solicitud HTTP o llamada a un conector.

#### Tiempo de espera
| Nombre | Límite | Notas |
| --- | --- | --- |
| Tiempo de espera de solicitud |1 minuto |Un [patrón asincrónico](app-service-logic-create-api-app.md) o un [bucle until](app-service-logic-loops-and-scopes.md) se pueden compensar, según sea necesario. |

#### Tamaño del mensaje
| Nombre | Límite | Notas |
| --- | --- | --- |
| Tamaño del mensaje |50 MB |Puede que algunos conectores y API no admitan 50 MB. El desencadenador de solicitud admite hasta 25 MB. |
| Límite de evaluación de expresiones |131 072 caracteres. |`@concat()`, `@base64()` y `string` no pueden tener un valor más largo. |

#### Directiva de reintentos
| Nombre | Límite | Notas |
| --- | --- | --- |
| Número de reintentos |4 |Se puede configurar con el [parámetro de directiva de reintento](https://msdn.microsoft.com/es-ES/library/azure/mt643939.aspx). |
| Retraso máximo entre reintentos |1 hora |Se puede configurar con el [parámetro de directiva de reintento](https://msdn.microsoft.com/es-ES/library/azure/mt643939.aspx). |
| Retraso mínimo entre reintentos |20 minutos |Se puede configurar con el [parámetro de directiva de reintento](https://msdn.microsoft.com/es-ES/library/azure/mt643939.aspx). |

### Retención y duración de ejecución
Son los límites de ejecución de una única aplicación lógica.

| Nombre | Límite | Notas |
| --- | --- | --- |
| Duración de la ejecución |90 días | |
| Retención de almacenamiento |90 días |Se cuenta a partir de la hora de inicio de la ejecución. |
| Intervalo de periodicidad mínima |15 segundos | |
| Intervalo de periodicidad máxima |500 días | |

### Límites de bucle y anulación de procesamiento por lotes
Son los límites de ejecución de una única aplicación lógica.

| Nombre | Límite | Notas |
| --- | --- | --- |
| Elementos ForEach |5\.000 |Puede usar la [acción de consulta](../connectors/connectors-native-query.md) para filtrar matrices más grandes, según sea necesario. |
| Iteraciones Until |10\.000 | |
| Elementos SplitOn |10\.000 | |
| Paralelismo de ForEach |20 | |

### Límites de rendimiento
Son los límites de una instancia de aplicación lógica.

| Nombre | Límite | Notas |
| --- | --- | --- |
| Desencadenadores por segundo |100 |Puede distribuir los flujos de trabajo entre varias aplicaciones, según sea necesario. |

### Límites de definición
Son los límites de una definición de aplicación lógica.

| Nombre | Límite | Notas |
| --- | --- | --- |
| Acciones de ForEach |1 |Puede agregar flujos de trabajo anidados para ampliar esta función, según sea necesario. |
| Acciones por flujo de trabajo |60 |Puede agregar flujos de trabajo anidados para ampliar esta función, según sea necesario. |
| Niveles de anidamiento de acciones permitidos |5 |Puede agregar flujos de trabajo anidados para ampliar esta función, según sea necesario. |
| Flujos por región y suscripción |1000 | |
| Desencadenadores por flujo de trabajo |10 | |
| Número máximo de caracteres por expresión |8192 | |
| Tamaño máximo de `trackedProperties` expresado en caracteres |16 000 | |
| Límite de nombre de `action`/`trigger` |80 | |
| Límite de longitud de `description` |256 | |
| Límite de `parameters` |50 | |
| Límite de `outputs` |10 | |

## Configuración
### Dirección IP
Las llamadas realizadas desde una [conector](../connectors/apis-list.md) procederán de la dirección IP especificada a continuación.

Las llamadas realizadas directamente desde una aplicación lógica (es decir, a través de [HTTP](../connectors/connectors-native-http.md) o [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)) pueden proceder de cualquiera de los [intervalos de direcciones IP de centro de datos de Azure](https://www.microsoft.com/es-ES/download/details.aspx?id=41653).

| Región de aplicación lógica | IP de salida |
| --- | --- |
| Australia Oriental |40\.126.251.213 |
| Sudeste de Australia |40\.127.80.34 |
| Sur de Brasil |191\.232.38.129 |
| India Central |104\.211.98.164 |
| Central EE. UU.: |40\.122.49.51 |
| Asia oriental |23\.99.116.181 |
| Este de EE. UU. |191\.237.41.52 |
| Este de EE. UU. 2 |104\.208.233.100 |
| Este de Japón |40\.115.186.96 |
| Oeste de Japón |40\.74.130.77 |
| Centro-Norte de EE. UU |65\.52.218.230 |
| Europa del Norte |104\.45.93.9 |
| Centro-Sur de EE. UU |104\.214.70.191 |
| Sudeste asiático |13\.76.231.68 |
| Sur de la India |104\.211.227.225 |
| Europa occidental |40\.115.50.13 |
| Oeste de la India |104\.211.161.203 |
| Oeste de EE. UU. |104\.40.51.248 |

## Pasos siguientes
* Para comenzar con las aplicaciones lógicas, siga el tutorial [Creación de una aplicación lógica](app-service-logic-create-a-logic-app.md).
* [Ejemplos de aplicaciones lógicas y escenarios comunes](app-service-logic-examples-and-scenarios.md)
* [Con las aplicaciones lógicas puede automatizar procesos empresariales.](http://channel9.msdn.com/Events/Build/2016/T694)
* [Obtenga información acerca de cómo integrar sus sistemas con Aplicaciones lógicas](http://channel9.msdn.com/Events/Build/2016/P462)

<!---HONumber=AcomDC_0831_2016-->