---
title: "Límites y configuración - Azure Logic Apps | Microsoft Docs"
description: "Valores de límites y configuración del servicio para Azure Logic Apps"
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
ms.date: 09/25/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 22d0ee242d18d73d1d5825567fd61638fd22cc68
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="logic-apps-limits-and-configuration"></a>Límites y configuración de Logic Apps

En este tema se describen los límites actuales y los detalles de configuración de Azure Logic Apps.

## <a name="limits"></a>límites

### <a name="http-request-limits"></a>Límites de solicitudes HTTP

Estos límites se aplican a una única solicitud HTTP o llamada de conector.

#### <a name="timeout"></a>Tiempo de espera

| NOMBRE | Límite | Notas | 
| ---- | ----- | ----- | 
| Tiempo de espera de solicitud | 120 segundos | Un [patrón asincrónico](../logic-apps/logic-apps-create-api-app.md) o un [bucle until](logic-apps-loops-and-scopes.md) se pueden compensar, según sea necesario. |
|||| 

#### <a name="message-size"></a>Tamaño del mensaje

| NOMBRE | Límite | Notas | 
| ---- | ----- | ----- | 
| Tamaño del mensaje | 100 MB | Puede que algunos conectores y API no admitan 100 MB. | 
| Límite de evaluación de expresiones | 131 072 caracteres. | `@concat()`, `@base64()` y `string` no pueden superar este límite. | 
|||| 

#### <a name="retry-policy"></a>Directiva de reintentos

| NOMBRE | Límite | Notas | 
| ---- | ----- | ----- | 
| Número de reintentos | 90 | El valor predeterminado es 4. Se puede configurar con el [parámetro de directiva de reintento](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Retraso máximo entre reintentos | 1 día | Se puede configurar con el [parámetro de directiva de reintento](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Retraso mínimo entre reintentos | 5 segundos | Se puede configurar con el [parámetro de directiva de reintento](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

### <a name="run-duration-and-retention"></a>Retención y duración de ejecución

Estos límites se aplican a una única ejecución de aplicación lógica.

| NOMBRE | Límite | 
| ---- | ----- | 
| Duración de la ejecución | 90 días | 
| Retención de almacenamiento | 90 días a partir de la hora de inicio de la ejecución | 
| Intervalo de periodicidad mínima | 1 segundo </br>Para las aplicaciones lógicas con un plan de App Service: 15 segundos | 
| Intervalo de periodicidad máxima | 500 días | 
||| 

Si desea superar los límites de la duración de ejecución o la retención de almacenamiento del flujo de procesamiento normal, [póngase en contacto con nosotros](mailto://logicappsemail@microsoft.com) para que podamos ayudarle con sus necesidades.

### <a name="looping-and-debatching-limits"></a>Límites de bucle y anulación de procesamiento por lotes

Estos límites se aplican a una única ejecución de aplicación lógica.

| NOMBRE | Límite | Notas | 
| ---- | ----- | ----- | 
| Elementos ForEach | 100 000 | Puede usar la [acción de consulta](../connectors/connectors-native-query.md) para filtrar matrices más grandes, según sea necesario. | 
| Iteraciones Until | 5.000 | | 
| Elementos SplitOn | 100 000 | | 
| Paralelismo de ForEach | 50 | El valor predeterminado es 20. <p>Para establecer un nivel específico de paralelismo en un bucle ForEach, establezca la propiedad `runtimeConfiguration` en la acción `foreach`. <p>Para ejecutar secuencialmente un bucle ForEach, establezca la propiedad `operationOptions` en "Sequential" en la acción `foreach`. | 
|||| 

### <a name="throughput-limits"></a>Límites de rendimiento

Estos límites se aplican a una única instancia de aplicación lógica.

| NOMBRE | Límite | Notas | 
| ---- | ----- | ----- | 
| Ejecuciones de acciones cada 5 minutos | 100 000 | Puede distribuir cargas de trabajo entre varias aplicaciones, según sea necesario. | 
| Llamadas salientes simultáneas de acciones | Aproximadamente 2500 | Reducir el número de solicitudes simultáneas o reducir la duración según sea necesario. | 
| Punto de conexión en tiempo de ejecución: llamadas entrantes simultáneas | ~1000 | Reducir el número de solicitudes simultáneas o reducir la duración según sea necesario. | 
| Punto de conexión en tiempo de ejecución: llamadas de lectura por 5 minutos | 60 000 | Puede distribuir cargas de trabajo entre varias aplicaciones, según sea necesario. | 
| Punto de conexión en tiempo de ejecución: llamadas de invocación por 5 minutos | 45 000 | Puede distribuir cargas de trabajo entre varias aplicaciones, según sea necesario. | 
|||| 

Si desea superar estos límites en el procesamiento normal, o ejecutar pruebas de carga que puedan superar estos límites, [póngase en contacto con nosotros](mailto://logicappsemail@microsoft.com) para que podamos ayudarle con sus necesidades.

### <a name="logic-app-definition-limits"></a>Límites de definición de aplicación lógica

Estos límites se aplican a una única definición de aplicación lógica.

| NOMBRE | Límite | Notas | 
| ---- | ----- | ----- | 
| Acciones por flujo de trabajo | 500 | Para ampliar este límite, puede agregar flujos de trabajo anidados según sea necesario. |
| Niveles de anidamiento de acciones permitidos | 8 | Para ampliar este límite, puede agregar flujos de trabajo anidados según sea necesario. | 
| Flujos de trabajo por región y suscripción | 1000 | | 
| Desencadenadores por flujo de trabajo | 10 | | 
| Límite de cambio de ámbito de casos | 25 | | 
| Número de variables por flujo de trabajo | 250 | | 
| Número máximo de caracteres por expresión | 8192 | | 
| Tamaño máximo de `trackedProperties` expresado en caracteres | 16 000 | 
| `action`/`trigger` | 80 | | 
| `description` | 256 | | 
| `parameters` limit | 50 | | 
| `outputs` limit | 10 | | 
|||| 

<a name="custom-connector-limits"></a>

### <a name="custom-connector-limits"></a>Límites de conector personalizado

Estos límites se aplican a los conectores personalizados que puede crear a partir de las API web.

| NOMBRE | Límite | 
| ---- | ----- | 
| Número de conectores personalizados que puede crear | 1000 por cada suscripción de Azure | 
| Número de solicitudes por minuto para cada conexión creada por un conector personalizado | 500 solicitudes para cada conexión creada por el conector |
||| 

### <a name="integration-account-limits"></a>Límites de cuenta de integración

Estos límites se aplican a los artefactos que se pueden agregar a una cuenta de integración.

| NOMBRE | Límite | Notas | 
| ---- | ----- | ----- | 
| Esquema | 8 MB | Puede usar el [identificador URI del blob](../logic-apps/logic-apps-enterprise-integration-schemas.md) para cargar archivos mayores de 2 MB. | 
| Asignación (archivo XSLT) | 2 MB | | 
| Punto de conexión en tiempo de ejecución: llamadas de lectura por 5 minutos | 60 000 | Puede distribuir cargas de trabajo entre varias cuentas según sea necesario. | 
| Punto de conexión en tiempo de ejecución: llamadas de invocación por 5 minutos | 45 000 | Puede distribuir cargas de trabajo entre varias cuentas según sea necesario. | 
| Punto de conexión en tiempo de ejecución: llamadas de seguimiento por 5 minutos | 45 000 | Puede distribuir cargas de trabajo entre varias cuentas según sea necesario. | 
| Punto de conexión en tiempo de ejecución: llamadas simultáneas de bloqueo | ~1000 | Reducir el número de solicitudes simultáneas o reducir la duración según sea necesario. | 
|||| 

Estos límites se aplican al número de artefactos que puede agregar a una cuenta de integración.

#### <a name="free-pricing-tier"></a>Plan de tarifa Gratis

| NOMBRE | Límite | Notas | 
| ---- | ----- | ----- | 
| Contratos | 10 | | 
| Otros tipos de artefacto | 25 |Los tipos de artefacto incluyen socios, esquemas, certificados y asignaciones. Cada tipo puede tener el número máximo de artefactos. | 
|||| 

#### <a name="standard-pricing-tier"></a>Plan de tarifa Estándar

| NOMBRE | Límite | Notas | 
| ---- | ----- | ----- | 
| Cualquier tipo de artefacto | 500 | Los tipos de artefacto incluyen acuerdos, socios, esquemas, certificados y asignaciones. Cada tipo puede tener el número máximo de artefactos. | 
|||| 

### <a name="b2b-protocols-as2-x12-edifact-message-size"></a>Tamaño de mensaje de protocolos B2B (AS2, X12 y EDIFACT)

Estos límites se aplican a los protocolos B2B.

| NOMBRE | Límite | Notas | 
| ---- | ----- | ----- | 
| AS2 | 50 MB | Se aplica a la decodificación y la codificación | 
| X12 | 50 MB | Se aplica a la decodificación y la codificación | 
| EDIFACT | 50 MB | Se aplica a la decodificación y la codificación | 
|||| 

<a name="configuration"></a>

## <a name="configuration-ip-addresses"></a>Configuración: direcciones IP

### <a name="logic-apps-service"></a>Servicio Logic Apps

Las llamadas que efectúa directamente una aplicación lógica, esto es, a través de [HTTP](../connectors/connectors-native-http.md) o [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) u otras solicitudes HTTP, proceden de las direcciones IP de esta lista.

|Región de Logic Apps|IP de salida|
|-----------------|-----------|
|Australia Oriental|13.75.149.4, 104.210.91.55, 104.210.90.241|
|Sudeste de Australia|13.73.114.207, 13.77.3.139, 13.70.159.205|
|Sur de Brasil|191.235.82.221, 191.235.91.7, 191.234.182.26|
|Centro de Canadá|52.233.29.92, 52.228.39.241, 52.228.39.244|
|Este de Canadá|52.232.128.155, 52.229.120.45, 52.229.126.25|
|India Central|52.172.154.168, 52.172.186.159, 52.172.185.79|
|Central EE. UU:|13.67.236.125, 104.208.25.27, 40.122.170.198|
|Asia oriental|13.75.94.173, 40.83.127.19, 52.175.33.254|
|Este de EE. UU|13.92.98.111, 40.121.91.41, 40.114.82.191|
|Este de EE. UU. 2|40.84.30.147, 104.208.155.200, 104.208.158.174|
|Este de Japón|13.71.158.3, 13.73.4.207, 13.71.158.120|
|Oeste de Japón|40.74.140.4, 104.214.137.243, 138.91.26.45|
|Centro-Norte de EE. UU|168.62.248.37, 157.55.210.61, 157.55.212.238|
|Europa del Norte|40.113.12.95, 52.178.165.215, 52.178.166.21|
|Centro-Sur de EE. UU|104.210.144.48, 13.65.82.17, 13.66.52.232|
|Sudeste asiático|13.76.133.155, 52.163.228.93, 52.163.230.166|
|Sur de la India|52.172.50.24, 52.172.55.231, 52.172.52.0|
|Centro occidental de EE.UU.|52.161.27.190, 52.161.18.218, 52.161.9.108|
|Europa occidental|40.68.222.65, 40.68.209.23, 13.95.147.65|
|Oeste de la India|104.211.164.80, 104.211.162.205, 104.211.164.136|
|Oeste de EE. UU|52.160.92.112, 40.118.244.241, 40.118.241.243|
|Oeste de EE. UU. 2|13.66.210.167, 52.183.30.169, 52.183.29.132|
|Sur del Reino Unido 2|51.140.74.14, 51.140.73.85, 51.140.78.44|
|Oeste de Reino Unido|51.141.54.185, 51.141.45.238, 51.141.47.136|
| | |

### <a name="connectors"></a>Conectores

Las llamadas que los [conectores](../connectors/apis-list.md) efectúan proceden de las direcciones IP de esta lista.

|Región de Logic Apps|IP de salida|
|-----------------|-----------|
|Australia Oriental|40.126.251.213|
|Sudeste de Australia|40.127.80.34|
|Sur de Brasil|191.232.38.129|
|Centro de Canadá|52.233.31.197, 52.228.42.205, 52.228.33.76, 52.228.34.13|
|Este de Canadá|52.229.123.98, 52.229.120.178, 52.229.126.202, 52.229.120.52|
|India Central|104.211.98.164|
|Central EE. UU:|40.122.49.51|
|Asia oriental|23.99.116.181|
|Este de EE. UU|191.237.41.52|
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
|Oeste de EE. UU|104.40.51.248|
|Sur del Reino Unido 2|51.140.80.51|
|Oeste de Reino Unido|51.141.47.105|
| | | 

## <a name="next-steps"></a>pasos siguientes  

* [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* [Ejemplos y escenarios comunes](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Vídeo: Automate business processes with Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694) (Automatización de procesos empresariales con Logic Apps) 
* [Vídeo: Integrate your systems with Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462) (Integración de los sistemas con Logic Apps)
