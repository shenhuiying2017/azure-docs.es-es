---
title: Seguimiento de mensajes B2B en Operations Management Suite - Azure Logic Apps | Microsoft Docs
description: "Realice un seguimiento de la comunicación B2B de la cuenta de integración y las aplicaciones lógicas en Operations Management Suite (OMS) con Azure Log Analytics"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 128abd504785227c1f27debd329d46d358e6e516
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="track-b2b-communication-in-the-microsoft-operations-management-suite-oms"></a>Seguimiento de la comunicación B2B en Microsoft Operations Management Suite (OMS)

Después de configurar la comunicación B2B entre dos procesos o aplicaciones empresariales mediante la cuenta de integración, esas entidades pueden intercambiar mensajes entre sí. Para comprobar si estos mensajes se procesan correctamente, puede realizar un seguimiento de los mensajes AS2, X12 y EDIFACT con [Azure Log Analytics](../log-analytics/log-analytics-overview.md) en [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Por ejemplo, puede usar estas capacidades de seguimiento basado en web para el seguimiento de mensajes:

* Número y estado de los mensajes
* Del estado de las confirmaciones
* Correlación de mensajes con confirmaciones
* Descripción detallada de errores
* De las funcionalidades de búsqueda

## <a name="requirements"></a>Requisitos

* Una aplicación lógica configurada con registro de diagnósticos. Obtenga información sobre [cómo crear una aplicación lógica](quickstart-create-first-logic-app-workflow.md) y [cómo configurar el registro de esa aplicación lógica](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Una cuenta de integración configurada con supervisión y registro. Aprenda [cómo crear una cuenta de integración](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) y [cómo configurar la supervisión y el registro para esa cuenta](../logic-apps/logic-apps-monitor-b2b-message.md).

* Si aún no lo ha hecho, [publique datos de diagnóstico para Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) en OMS.

> [!NOTE]
> Después de haber satisfecho los requisitos anteriores, debería tener un área de trabajo en [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Debería usar la misma área de trabajo de OMS para realizar el seguimiento de la comunicación B2B en OMS. 
>  
> Si no tiene un área de trabajo de OMS, aprenda [cómo crear un área de trabajo de OMS](../log-analytics/log-analytics-get-started.md).

## <a name="add-the-logic-apps-b2b-solution-to-the-operations-management-suite-oms"></a>Incorporación de la solución B2B de Logic Apps a Operations Management Suite (OMS)

Para que OMS realice el seguimiento de los mensajes B2B de la aplicación lógica, debe agregar la solución **Logic Apps B2B** al portal de OMS. Más información sobre la [incorporación de soluciones a OMS](../log-analytics/log-analytics-get-started.md).

1. En [Azure Portal](https://portal.azure.com), elija **Más servicios**. Busque "log analytics" y luego elija **Log Analytics** como se muestra aquí:

   ![Búsqueda de Log Analytics](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)

2. En **Log Analytics**, busque y seleccione el área de trabajo de OMS. 

   ![Selección del área de trabajo de OMS](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. En **Administración**, elija **Portal de OMS**.

   ![Selección de Portal de OMS](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. Cuando se abra la página principal de OMS, elija **Galería de soluciones**.    

   ![Selección de Galería de soluciones](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. En **Todas las soluciones**, busque y seleccione **Logic Apps B2B**.     

   ![Selección de Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. En **Logic Apps B2B**, elija **Agregar**.

   ![Elección de Agregar](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

   En la página principal de OMS aparece el icono de **Mensajes B2B de Logic Apps**. 
   Este icono actualiza el recuento de mensajes al procesar los mensajes B2B.

   ![Página principal de OMS, icono de Mensajes B2B de Logic Apps](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

<a name="message-status-details"></a>

## <a name="track-message-status-and-details-in-the-operations-management-suite"></a>Seguimiento del estado y los detalles de los mensajes en Operations Management Suite

1. Una vez procesados los mensajes B2B, puede ver el estado y los detalles de esos mensajes. En la página principal de OMS, elija el icono de **Mensajes B2B de Logic Apps**.

   ![Recuento de mensajes actualizado](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

   > [!NOTE]
   > De forma predeterminada, el icono de **Mensajes B2B de Logic Apps** muestra datos basados en un solo día. Para cambiar el ámbito de datos a otro intervalo, elija el control de ámbito de la parte superior de la página de OMS:
   > 
   > ![Cambio del ámbito de datos](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)
   >

2. Después de que aparezca el panel de estado de los mensajes, puede ver más detalles sobre un tipo de mensaje concreto, lo que muestra los datos basados en un solo día. Seleccione el icono de **AS2**, **X12** o **EDIFACT**.

   ![Visualización del estado de los mensajes](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   Aparece una lista de mensajes para el icono seleccionado. 
   Para más información sobre las propiedades de cada tipo de mensaje, vea estas descripciones de las propiedades de los mensajes:

   * [Propiedades de mensajes AS2](#as2-message-properties)
   * [Propiedades de mensajes X12](#x12-message-properties)
   * [Propiedades de mensajes EDIFACT](#EDIFACT-message-properties)

   Por ejemplo, este podría ser el aspecto de una lista de mensajes AS2:

   ![Visualización de mensajes AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Para ver o exportar las entradas y salidas de mensajes concretos, seleccione esos mensajes y elija **Descargar**. Cuando se le pida, guarde el archivo .zip en el equipo local y luego extraiga ese archivo. 

   La carpeta extraída incluye una carpeta para cada mensaje seleccionado. 
   Si configura confirmaciones, la carpeta de mensajes incluye además archivos con detalles de confirmación. 
   Cada carpeta de mensaje tiene como mínimo estos archivos: 
   
   * Archivos de lenguaje natural con los detalles de carga de entrada y carga de salida
   * Archivos codificados con las entradas y salidas

   Aquí encontrará los formatos de nombre de carpeta y archivo de cada tipo de mensaje:

   * [Formatos de nombre de carpeta y archivo AS2](#as2-folder-file-names)
   * [Formatos de nombre de carpeta y archivo X12](#x12-folder-file-names)
   * [Formatos de nombre de carpeta y archivo EDIFACT](#edifact-folder-file-names)

   ![Descarga de archivos de mensajes](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Para ver todas las acciones que tienen el mismo identificador de ejecución, en la página **Búsqueda de registros**, elija un mensaje de la lista de mensajes.

   Puede ordenar estas acciones por columna o buscar resultados concretos.

   ![Acciones con el mismo identificador de ejecución](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Para buscar resultados con consultas predeterminadas, elija **Favoritos**.

   * Aprenda [cómo generar consultas mediante la adición de filtros](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   O bien aprenda [cómo buscar datos con búsquedas de registros en Log Analytics](../log-analytics/log-analytics-log-searches.md).

   * Para cambiar la consulta del cuadro de búsqueda, actualícela con las columnas y los valores que quiera usar como filtros.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Descripciones de propiedades y formatos de nombre de los mensajes AS2, X12 y EDIFACT

Estas son las descripciones de propiedades y los formatos de nombre de los archivos de mensajes descargados por tipo de mensaje.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Descripciones de propiedades de los mensajes AS2

Estas son las descripciones de propiedades de cada mensaje AS2.

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| Remitente | Asociado invitado especificado en **Configuración de recepción** o asociado del host especificado en **Configuración de envío** para un acuerdo de AS2 |
| Receptor | Asociado del host especificado en **Configuración de recepción** o asociado invitado especificado en **Configuración de envío** para un acuerdo de AS2 |
| Aplicación lógica | Aplicación lógica donde se configuran las acciones AS2 |
| Status | Estado del mensaje AS2 <br>Correcto = recibido o enviado un mensaje AS2 válido. No se configura ninguna MDN. <br>Correcto = recibido o enviado un mensaje AS2 válido. Se configura y se recibe una MDN, o se envía. <br>Error = recibido un mensaje AS2 no válido. No se configura ninguna MDN. <br>Pendiente = recibido o enviado un mensaje AS2 válido. Se configura una MDN y se espera una MDN. |
| Ack | Estado del mensaje MDN <br>Aceptado = recibida o enviada una MDN positiva. <br>Pendiente = esperando a recibir o enviar una MDN. <br>Rechazado = recibida o enviada una MDN negativa. <br>No necesario = no configurada ninguna MDN en el acuerdo. |
| Dirección | Dirección del mensaje AS2 |
| Id. de correlación | Identificador que correlaciona todos los desencadenadores y las acciones de una aplicación lógica |
| Id. de mensaje | Identificador del mensaje AS2 a partir de los encabezados de mensajes AS2 |
| Timestamp | Hora a la que la acción AS2 procesó el mensaje |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>Formatos de nombre AS2 para archivos de mensajes descargados

Estos son los formatos de nombre de cada carpeta y archivo de mensajes AS2 descargados.

| Archivo o carpeta | Formato de nombre |
| :------------- | :---------- |
| Carpeta de mensajes | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Archivos de entrada, salida y, si se ha configurado, de confirmación | **Carga de entrada**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Carga de salida**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Entradas**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Salidas**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Descripciones de propiedades de los mensajes X12

Estas son las descripciones de propiedades de cada mensaje X12.

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| Remitente | Asociado invitado especificado en **Configuración de recepción** o asociado del host especificado en **Configuración de envío** para un acuerdo de X12 |
| Receptor | Asociado del host especificado en **Configuración de recepción** o asociado invitado especificado en **Configuración de envío** para un acuerdo de X12 |
| Aplicación lógica | Aplicación lógica donde se configuran las acciones X12 |
| Status | Estado del mensaje X12 <br>Correcto = recibido o enviado un mensaje X12 válido. No se configura ninguna confirmación funcional. <br>Correcto = recibido o enviado un mensaje X12 válido. Se configura y se recibe una confirmación funcional, o se envía una confirmación funcional. <br>Error = recibido o enviado un mensaje X12 no válido. <br>Pendiente = recibido o enviado un mensaje X12 válido. Se configura una confirmación funcional y se espera una confirmación funcional. |
| Ack | Estado de confirmación funcional (997) <br>Aceptado = recibida o enviada una confirmación funcional positiva. <br>Rechazado = recibida o enviada una confirmación funcional negativa. <br>Pendiente = se espera una confirmación funcional, pero no se ha recibido. <br>Pendiente = generada una confirmación funcional, pero no se puede enviar al asociado. <br>No necesario = confirmación funcional no configurada. |
| Dirección | Dirección del mensaje X12 |
| Id. de correlación | Identificador que correlaciona todos los desencadenadores y las acciones de una aplicación lógica |
| Tipo de mensaje | Tipo de mensaje X12 de EDI |
| ICN | Número de control de intercambio del mensaje X12 |
| TSCN | Número de control de conjuntos de transacciones del mensaje X12 |
| Timestamp | Hora a la que la acción X12 procesó el mensaje |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>Formatos de nombre X12 para archivos de mensajes descargados

Estos son los formatos de nombre de cada carpeta y archivo de mensajes X12 descargados.

| Archivo o carpeta | Formato de nombre |
| :------------- | :---------- |
| Carpeta de mensajes | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Archivos de entrada, salida y, si se ha configurado, de confirmación | **Carga de entrada**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Carga de salida**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Entradas**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Salidas**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Descripciones de propiedades de los mensajes EDIFACT

Estas son las descripciones de propiedades de cada mensaje EDIFACT.

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| Remitente | Asociado invitado especificado en **Configuración de recepción** o asociado del host especificado en **Configuración de envío** para un acuerdo de EDIFACT |
| Receptor | Asociado del host especificado en **Configuración de recepción** o asociado invitado especificado en **Configuración de envío** para un acuerdo de EDIFACT |
| Aplicación lógica | Aplicación lógica donde se configuran las acciones EDIFACT |
| Status | Estado del mensaje EDIFACT <br>Correcto = recibido o enviado un mensaje EDIFACT válido. No se configura ninguna confirmación funcional. <br>Correcto = recibido o enviado un mensaje EDIFACT válido. Se configura y se recibe una confirmación funcional, o se envía una confirmación funcional. <br>Error = recibido o enviado un mensaje EDIFACT no válido <br>Pendiente = recibido o enviado un mensaje EDIFACT válido. Se configura una confirmación funcional y se espera una confirmación funcional. |
| Ack | Estado de confirmación funcional (997) <br>Aceptado = recibida o enviada una confirmación funcional positiva. <br>Rechazado = recibida o enviada una confirmación funcional negativa. <br>Pendiente = se espera una confirmación funcional, pero no se ha recibido. <br>Pendiente = generada una confirmación funcional, pero no se puede enviar al asociado. <br>No necesario = confirmación funcional no configurada. |
| Dirección | Dirección del mensaje EDIFACT |
| Id. de correlación | Identificador que correlaciona todos los desencadenadores y las acciones de una aplicación lógica |
| Tipo de mensaje | Tipo del mensaje EDIFACT |
| ICN | Número de control de intercambio del mensaje EDIFACT |
| TSCN | Número de control de conjuntos de transacciones del mensaje EDIFACT |
| Timestamp | Hora a la que la acción EDIFACT procesó el mensaje |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>Formatos de nombre EDIFACT para archivos de mensajes descargados

Estos son los formatos de nombre de cada carpeta y archivo de mensajes EDIFACT descargados.

| Archivo o carpeta | Formato de nombre |
| :------------- | :---------- |
| Carpeta de mensajes | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Archivos de entrada, salida y, si se ha configurado, de confirmación | **Carga de entrada**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Carga de salida**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Entradas**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Salidas**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|          |             |

## <a name="next-steps"></a>pasos siguientes

* [Consulta de mensajes B2B en Operations Management Suite](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [Esquemas de seguimiento de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de seguimiento de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de seguimiento personalizados](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)