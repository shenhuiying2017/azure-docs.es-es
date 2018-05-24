---
title: Límites y configuración - Azure Logic Apps | Microsoft Docs
description: Valores de límites y configuración del servicio para Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: ecfan
manager: cfowler
editor: ''
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/14/2018
ms.author: estfan
ms.openlocfilehash: 8c2ac4b8f55d25d5d3fcfdd6a9bcb6f6c8cfc201
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166308"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Información de límites y configuración para Azure Logic Apps

En este artículo, se describen los límites y los detalles de configuración para crear y ejecutar flujos de trabajo automatizados con Azure Logic Apps. Para Microsoft Flow, consulte [Límites y configuración en Microsoft Flow](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Límites de definición

Estos son los límites de una definición de aplicación lógica:

| NOMBRE | Límite | Notas | 
| ---- | ----- | ----- | 
| Acciones por flujo de trabajo | 500 | Para ampliar este límite, puede agregar flujos de trabajo anidados según sea necesario. |
| Niveles de anidamiento permitidos para acciones | 8 | Para ampliar este límite, puede agregar flujos de trabajo anidados según sea necesario. | 
| Flujos de trabajo por región y suscripción | 1000 | | 
| Desencadenadores por flujo de trabajo | 10 | Al trabajar en la vista Código, no en el diseñador | 
| Límite de cambio de ámbito de casos | 25 | | 
| Variables por flujo de trabajo | 250 | | 
| Caracteres por expresión | 8192 | | 
| Tamaño máximo de `trackedProperties` | 16 000 caracteres | 
| Nombre de `action` o `trigger` | 80 caracteres | | 
| Longitud de `description` | 256 caracteres | | 
| Máximo de `parameters` | 50 | | 
| Máximo de `outputs` | 10 | | 
||||  

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Límites de retención y duración de ejecución

Estos son los límites de ejecución de una única aplicación lógica:

| NOMBRE | Límite | Notas | 
|------|-------|-------| 
| Duración de la ejecución | 90 días | Para cambiar este límite, consulte [Cambio de la duración de ejecución](#change-duration). | 
| Retención de almacenamiento | 90 días a partir de la hora de inicio de la ejecución | Para cambiar este límite, consulte [Cambio de la retención de almacenamiento](#change-retention). | 
| Intervalo de periodicidad mínima | 1 segundo | | 
| Intervalo de periodicidad máxima | 500 días | | 
|||| 

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Cambio de la duración de ejecución y la retención de almacenamiento

Puede cambiar este límite a un valor de entre siete y 90 días. En cambio, para superar el límite máximo, [póngase en contacto con el equipo de Logic Apps](mailto://logicappsemail@microsoft.com) para obtener ayuda con sus requisitos.

1. En Azure Portal, en el menú de la aplicación lógica, elija **Configuración del flujo de trabajo**. 

2. En **Opciones del entorno de ejecución**, en la lista **Retención del historial de ejecución en días** elija **Personalizado**. 

3. Escriba o arrastre el control deslizante hasta el número de días que quiera.

<a name="looping-debatching-limits"></a>

## <a name="looping-and-debatching-limits"></a>Límites de bucle y anulación de procesamiento por lotes

Estos son los límites de ejecución de una única aplicación lógica:

| NOMBRE | Límite | Notas | 
| ---- | ----- | ----- | 
| Iteraciones Until | 5.000 | | 
| Elementos ForEach | 100 000 | Puede usar la [acción de consulta](../connectors/connectors-native-query.md) para filtrar matrices más grandes, según sea necesario. | 
| Paralelismo de ForEach | 50 | El valor predeterminado es 20. <p>Para establecer un nivel específico de paralelismo en un bucle ForEach, establezca la propiedad `runtimeConfiguration` en la acción `foreach`. <p>Para ejecutar secuencialmente un bucle ForEach, establezca la propiedad `operationOptions` en "Sequential" en la acción `foreach`. | 
| Elementos SplitOn | 100 000 | | 
|||| 

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Límites de rendimiento

Estos son los límites de ejecución de una única aplicación lógica:

| NOMBRE | Límite | Notas | 
| ----- | ----- | ----- | 
| Ejecuciones de acciones cada 5 minutos | 100 000 | Para aumentar el límite a 300 000, puede ejecutar una aplicación de lógica en modo `High Throughput`. Para configurar el modo de alto rendimiento, en `runtimeConfiguration` del recurso de flujo de trabajo, establezca la propiedad `operationOptions` en `OptimizedForHighThroughput`. <p>**Nota**: El modo de alto rendimiento se encuentra en versión preliminar. Además, puede distribuir una carga de trabajo entre varias aplicaciones, según sea necesario. | 
| Llamadas salientes simultáneas de acciones | Aproximadamente 2500 | Reducir el número de solicitudes simultáneas o reducir la duración según sea necesario. | 
| Punto de conexión en tiempo de ejecución: llamadas entrantes simultáneas | ~1000 | Reducir el número de solicitudes simultáneas o reducir la duración según sea necesario. | 
| Punto de conexión en tiempo de ejecución: llamadas de lectura por 5 minutos  | 60 000 | Puede distribuir cargas de trabajo entre varias aplicaciones, según sea necesario. | 
| Punto de conexión en tiempo de ejecución: llamadas de invocación por 5 minutos| 45 000 |Puede distribuir cargas de trabajo entre varias aplicaciones, según sea necesario. | 
|||| 

Si desea superar estos límites en el procesamiento normal, o ejecutar pruebas de carga que puedan superar estos límites, [póngase en contacto con el equipo de Logic Apps](mailto://logicappsemail@microsoft.com) para que podamos ayudarle con sus necesidades.

<a name="request-limits"></a>

## <a name="http-request-limits"></a>Límites de solicitudes HTTP

Estos son los límites de una única solicitud HTTP o llamada de conector sincrónica:

#### <a name="timeout"></a>Tiempo de espera

Algunas operaciones de conector realizan llamadas asincrónicas o escuchan las solicitudes de webhook, por lo que el tiempo de expiración de estas operaciones puede ser superior a estos límites. Para obtener más información, vea los detalles técnicos del conector concreto y también [Acciones y desencadenadores de flujos de trabajo](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| NOMBRE | Límite | Notas | 
| ---- | ----- | ----- | 
| Solicitud saliente | 120 segundos | Para las operaciones de ejecución más largas, use un [patrón de sondeo asincrónico](../logic-apps/logic-apps-create-api-app.md#async-pattern) o un [bucle Until](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). | 
| Respuesta sincrónica | 120 segundos | Para que la solicitud original obtenga la respuesta, todos los pasos de la respuesta deben terminar dentro del límite, a menos que llame a otra aplicación lógica como un flujo de trabajo anidado. Para obtener más información, consulte [Llamada, desencadenamiento o anidación de aplicaciones lógicas](../logic-apps/logic-apps-http-endpoint.md). | 
|||| 

#### <a name="message-size"></a>Tamaño del mensaje

| NOMBRE | Límite | Notas | 
| ---- | ----- | ----- | 
| Tamaño del mensaje | 100 MB | Para evitar este límite, consulte [Handle large messages with chunking](../logic-apps/logic-apps-handle-large-messages.md) (Controlar mensajes grandes con fragmentación). En cambio, puede que algunos conectores y API no admitan la fragmentación ni el límite predeterminado. | 
| Tamaño del mensaje con fragmentación | 1 GB | Este límite se aplica a las acciones que admiten la fragmentación de forma nativa o pueden tener habilitada la compatibilidad con la fragmentación en su configuración del entorno de ejecución. Para obtener más información, consulte [Handle large messages with chunking](../logic-apps/logic-apps-handle-large-messages.md) (Controlar mensajes grandes con fragmentación). | 
| Límite de evaluación de expresiones | 131 072 caracteres. | Las expresiones `@concat()`, `@base64()` y `@string()` no pueden superar este límite. | 
|||| 

#### <a name="retry-policy"></a>Directiva de reintentos

| NOMBRE | Límite | Notas | 
| ---- | ----- | ----- | 
| Número de reintentos | 90 | El valor predeterminado es 4. Para cambiar el valor predeterminado, use el [parámetro de directiva de reintentos](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Retraso máximo entre reintentos | 1 día | Para cambiar el valor predeterminado, use el [parámetro de directiva de reintentos](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Retraso mínimo entre reintentos | 5 segundos | Para cambiar el valor predeterminado, use el [parámetro de directiva de reintentos](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Límites de conector personalizado

Estos son los límites para los conectores personalizados que puede crear a partir de las API web.

| NOMBRE | Límite | 
| ---- | ----- | 
| Número de conectores personalizados | 1000 por cada suscripción de Azure | 
| Número de solicitudes por minuto para cada conexión creada por un conector personalizado | 500 solicitudes por conexión |
|||| 

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Límites de cuenta de integración

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Límites de artefacto por cuenta de integración

Estos son los límites en cuanto al número de artefactos de cada cuenta de integración. Para obtener más información, consulte [Precios de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

*Nivel Gratis*

| Artefacto | Límite | Notas | 
|----------|-------|-------| 
| Socios comerciales de EDI | 25 | | 
| Acuerdos comerciales de EDI | 10 | | 
| Mapas | 25 | | 
| Esquemas | 25 | 
| Ensamblados | 10 | | 
| Configuraciones por lotes | 5 | 
| Certificados | 25 | | 
|||| 

*Nivel básico*

| Artefacto | Límite | Notas | 
|----------|-------|-------| 
| Socios comerciales de EDI | 2 | | 
| Acuerdos comerciales de EDI | 1 | | 
| Mapas | 500 | | 
| Esquemas | 500 | 
| Ensamblados | 25 | | 
| Configuraciones por lotes | 1 | | 
| Certificados | 2 | | 
|||| 

*Nivel estándar*

| Artefacto | Límite | Notas | 
|----------|-------|-------| 
| Socios comerciales de EDI | 500 | | 
| Acuerdos comerciales de EDI | 500 | | 
| Mapas | 500 | | 
| Esquemas | 500 | 
| Ensamblados | 50 | | 
| Configuraciones por lotes | 5 |  
| Certificados | 50 | | 
|||| 

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Límites de capacidad de artefactos

| NOMBRE | Límite | Notas | 
| ---- | ----- | ----- | 
| Esquema | 8 MB | Para cargar archivos mayores de 2 MB, use el [URI del blob](../logic-apps/logic-apps-enterprise-integration-schemas.md). | 
| Asignación (archivo XSLT) | 2 MB | | 
| Punto de conexión en tiempo de ejecución: llamadas de lectura por 5 minutos | 60 000 | Puede distribuir la carga de trabajo entre varias cuentas según sea necesario. | 
| Punto de conexión en tiempo de ejecución: llamadas de invocación por 5 minutos | 45 000 | Puede distribuir la carga de trabajo entre varias cuentas según sea necesario. | 
| Punto de conexión en tiempo de ejecución: llamadas de seguimiento por 5 minutos | 45 000 | Puede distribuir la carga de trabajo entre varias cuentas según sea necesario. | 
| Punto de conexión en tiempo de ejecución: llamadas simultáneas de bloqueo | ~1000 | Puede reducir el número de solicitudes simultáneas o la duración según sea necesario. | 
||||  

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Tamaño de mensaje de protocolo B2B (AS2, X12 y EDIFACT)

Estos son los límites que se aplican a los protocolos B2B:

| NOMBRE | Límite | Notas | 
| ---- | ----- | ----- | 
| AS2 | 50 MB | Se aplica a la decodificación y la codificación | 
| X12 | 50 MB | Se aplica a la decodificación y la codificación | 
| EDIFACT | 50 MB | Se aplica a la decodificación y la codificación | 
|||| 

<a name="configuration"></a>

## <a name="configuration-ip-addresses"></a>Configuración: direcciones IP

### <a name="azure-logic-apps-service"></a>Servicio Azure Logic Apps

Todas las aplicaciones lógicas en una región usan el mismo intervalo de direcciones IP.
Las llamadas que efectúan directamente las aplicaciones lógicas con [HTTP](../connectors/connectors-native-http.md), [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) u otras solicitudes HTTP proceden de las direcciones IP de esta lista. 

| Región de Logic Apps | IP de salida |
|-------------------|-------------|
| Australia | 13.73.114.207, 13.77.3.139, 13.70.159.205 |
| Australia Oriental | 13.75.149.4, 104.210.91.55, 104.210.90.241 |
| Sur de Brasil | 191.235.82.221, 191.235.91.7, 191.234.182.26 |
| Centro de Canadá | 52.233.29.92, 52.228.39.241, 52.228.39.244 |
| Este de Canadá | 52.232.128.155, 52.229.120.45, 52.229.126.25 |
| India Central | 52.172.154.168, 52.172.186.159, 52.172.185.79 |
| Central EE. UU: | 13.67.236.125, 104.208.25.27, 40.122.170.198 |
| Asia oriental | 13.75.94.173, 40.83.127.19, 52.175.33.254 |
| Este de EE. UU | 13.92.98.111, 40.121.91.41, 40.114.82.191 |
| Este de EE. UU. 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174 |
| Este de Japón | 13.71.158.3, 13.73.4.207, 13.71.158.120 |
| Oeste de Japón | 40.74.140.4, 104.214.137.243, 138.91.26.45 |
| Centro-Norte de EE. UU | 168.62.248.37, 157.55.210.61, 157.55.212.238 |
| Europa del Norte | 40.113.12.95, 52.178.165.215, 52.178.166.21 |
| Centro-Sur de EE. UU | 104.210.144.48, 13.65.82.17, 13.66.52.232 |
| Sur de la India | 52.172.50.24, 52.172.55.231, 52.172.52.0 |
| Sudeste asiático | 13.76.133.155, 52.163.228.93, 52.163.230.166 |
| Centro occidental de EE.UU. | 52.161.27.190, 52.161.18.218, 52.161.9.108 |
| Europa occidental | 40.68.222.65, 40.68.209.23, 13.95.147.65 |
| Oeste de la India | 104.211.164.80, 104.211.162.205, 104.211.164.136 |
| Oeste de EE. UU | 52.160.92.112, 40.118.244.241, 40.118.241.243 |
| Oeste de EE. UU. 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132 |
| Sur del Reino Unido 2 | 51.140.74.14, 51.140.73.85, 51.140.78.44 |
| Oeste de Reino Unido | 51.141.54.185, 51.141.45.238, 51.141.47.136 |
| | |

| Región de Logic Apps | IP entrante |
|-------------------|-------------|
| Australia Oriental | 3.75.153.66, 104.210.89.222, 104.210.89.244 |
| Sudeste de Australia | 13.73.115.153, 40.115.78.70, 40.115.78.237 |
| Sur de Brasil | 191.235.86.199, 191.235.95.229, 191.235.94.220 |
| Centro de Canadá | 13.88.249.209, 52.233.30.218, 52.233.29.79 |
| Este de Canadá | 52.232.129.143, 52.229.125.57, 52.232.133.109 |
| India Central | 52.172.157.194, 52.172.184.192, 52.172.191.194 |
| Central EE. UU: | 13.67.236.76, 40.77.111.254, 40.77.31.87 |
| Asia oriental | 168.63.200.173, 13.75.89.159, 23.97.68.172 |
| Este de EE. UU | 137.135.106.54, 40.117.99.79, 40.117.100.228 |
| Este de EE. UU. 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136 |
| Este de Japón | 13.71.146.140, 13.78.84.187, 13.78.62.130 |
| Oeste de Japón | 40.74.140.173, 40.74.81.13, 40.74.85.215 |
| Centro-Norte de EE. UU | 168.62.249.81, 157.56.12.202, 65.52.211.164 |
| Europa del Norte | 13.79.173.49, 52.169.218.253, 52.169.220.174 |
| Centro-Sur de EE. UU | 52.172.9.47, 52.172.49.43, 52.172.51.140 |
| Sur de la India | 52.172.9.47, 52.172.49.43, 52.172.51.140 |
| Sudeste asiático | 52.163.93.214, 52.187.65.81, 52.187.65.155 |
| Centro occidental de EE.UU. | 52.161.26.172, 52.161.8.128, 52.161.19.82 |
| Europa occidental | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| Oeste de la India | 104.211.164.112, 104.211.165.81, 104.211.164.25 |
| Oeste de EE. UU | 52.160.90.237, 138.91.188.137, 13.91.252.184 |
| Oeste de EE. UU. 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Sur del Reino Unido 2 | 51.140.79.109, 51.140.78.71, 51.140.84.39 |
| Oeste de Reino Unido | 51.141.48.98, 51.141.51.145, 51.141.53.164 |
| | |

### <a name="connectors"></a>Conectores

Las llamadas que los [conectores](../connectors/apis-list.md) efectúan proceden de las direcciones IP de esta lista.

| Región de Logic Apps | IP de salida |
|-------------------|-------------|
| Australia Oriental | 40.126.251.213 |
| Sudeste de Australia | 40.127.80.34 |
| Sur de Brasil | 191.232.38.129 |
| Centro de Canadá | 52.233.31.197, 52.228.42.205, 52.228.33.76, 52.228.34.13 |
| Este de Canadá | 52.229.123.98, 52.229.120.178, 52.229.126.202, 52.229.120.52 |
| India Central | 104.211.98.164 |
| Central EE. UU: | 40.122.49.51 |
| Asia oriental | 23.99.116.181 |
| Este de EE. UU | 191.237.41.52 |
| Este de EE. UU. 2 | 104.208.233.100 |
| Este de Japón | 40.115.186.96 |
| Oeste de Japón | 40.74.130.77 |
| Centro-Norte de EE. UU | 65.52.218.230 |
| Europa del Norte | 104.45.93.9 |
| Centro-Sur de EE. UU | 104.214.70.191 |
| Sur de la India | 104.211.227.225 |
| Sudeste asiático | 13.76.231.68 |
| Europa occidental | 40.115.50.13 |
| Oeste de la India | 104.211.161.203 |
| Oeste de EE. UU | 104.40.51.248 |
| Sur del Reino Unido 2 | 51.140.80.51 |
| Oeste de Reino Unido | 51.141.47.105 |
| | | 

## <a name="next-steps"></a>Pasos siguientes  

* [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* [Ejemplos y escenarios comunes](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Vídeo: Automate business processes with Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694) (Automatización de procesos empresariales con Logic Apps) 
* [Vídeo: Integrate your systems with Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462) (Integración de los sistemas con Logic Apps)
