---
title: Seguimiento de mensajes B2B en el Portal de OMS | Microsoft Docs
description: Seguimiento de mensajes B2B en el Portal de OMS
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
ms.date: 11/13/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 53195091ac4b93ed94f432990c84c407615fc03e
ms.openlocfilehash: 9c3855c7fce5a9f38424f0bb6cd03f7a2c8d36be


---
# <a name="tracking-b2b-messages-in-oms-portal"></a>Seguimiento de mensajes B2B en el Portal de OMS
La comunicación B2B implica intercambios de mensajes entre dos aplicaciones o procesos empresariales en ejecución. El seguimiento de mensajes B2B en el Portal de OMS proporciona gran cantidad de funcionalidades de seguimiento por Internet que permiten para ver si los mensajes se han procesado correctamente.  Se puede realizar un seguimiento

* Del número y estado de los mensajes
* Del estado de las confirmaciones
* De la correlación de mensajes con confirmaciones
* De la descripción detallada de los errores
* De las funcionalidades de búsqueda

## <a name="prerequisites"></a>Requisitos previos
* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)
* Una cuenta de integración; puede crear un [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) y habilitar el registro; [aquí](logic-apps-monitor-b2b-message.md) puede buscar los pasos que deben darse para hacerlo
* Una aplicación lógica; puede crear un [aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md) y habilitar el registro; los pasos para hacerlos se encuentran [aquí](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts)

## <a name="adding-logic-apps-b2b-solution-to-oms-portal"></a>Incorporación de una solución B2B de Logic Apps al Portal de OMS

1. Seleccione **More Services** (Más servicios) en el portal, busque **log analytics** y seleccione **Log Analytics**   
![Búsqueda de Log Analytics](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)  

2. Seleccione su **Log Analytics**.  
![Selección de Log Analytics](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. Seleccione **Portal de OMS**; se abre la página principal del portal de OMS.   
![Examen del portal de OMS](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. Seleccione **Galería de soluciones**    
![Seleccionar Galería de soluciones](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. Seleccione **Logic Apps B2B**     
![Selección de Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. Haga clic en **Agregar** para agregar **Logic Apps B2B Messages** (Mensajes de Logic Apps B2B) a la página principal  
![Selecciona Agregar](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

7. Examine la página principal para ver **Logic Apps B2B Messages**  (Mensajes de Logic Apps B2B)  
![Seleccionar la página principal](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

## <a name="tracking-data-in-oms-portal"></a>Datos de seguimiento en el portal de OMS

1. Proceso posterior al mensaje; la página principal se actualiza con el número de mensajes   
![Seleccionar la página principal](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

2. Si se selecciona **Logic Apps B2B Messages** (Mensajes de Logic Apps B2B) en la página principal, pasará al estado de los mensajes AS2 y X12.  Los datos se basan en el último día.
![Seleccionar mensajes de Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)



3. Si selecciona los mensajes AS2 o X12 por estado, pasará a la lista de mensajes   
![Selección del estado de mensaje AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

| Propiedad | Descripción |
| --- | --- |
| Remitente | Asociado invitado definido en la configuración de recepción o asociado anfitrión definido en la configuración de envío de un acuerdo AS2. |
| Receptor | Asociado anfitrión definido en la configuración de recepción o asociado invitado definido en la configuración de envío de un acuerdo AS2. |
| Aplicación lógica | Lógica de aplicación donde se configuran las acciones AS2. |
| Estado | Estado del mensaje AS2. Operación correcta = Se ha recibido o enviado un mensaje AS2 bueno, no hay MDN configurada; Operación correcta = Se ha recibido o enviado un mensaje AS2 bueno, MDN configurada y recibida o MDN enviada; Error = Se ha recibido un mensaje AS2 malo, no hay MDN configurada; Pendiente = Se ha recibido o enviado un mensaje de AS2 bueno, MDN; configurada y en espera de una confirmación funcional; |
| Ack | Estado del mensaje MDN. |
| Dirección | Dirección del mensaje AS2. |
| Id. de correlación | Identificador para poner en correlación todos los desencadenadores y acciones dentro de una aplicación de lógica. |
| Id. de mensaje |  Identificador del mensaje AS2, de los encabezados del mensaje AS2 |
| Timestamp | Hora en la que la acción AS2 procesa el mensaje. |
|  |  |


![Seleccionar estado de mensaje de X12](media/logic-apps-track-b2b-messages-omsportal/x12messagelist.png)

| Propiedad | Descripción |
| --- | --- |
| Remitente | Asociado invitado definido en la configuración de recepción o asociado anfitrión definido en la configuración de envío de un acuerdo AS2. |
| Receptor | Asociado anfitrión definido en la configuración de recepción o asociado invitado definido en la configuración de envío de un acuerdo AS2. |
| Aplicación lógica | Lógica de aplicación donde se configuran las acciones AS2. |
| Estado | Estado del mensaje X12. Operación correcta = Se ha recibido o enviado un mensaje X12 bueno, no hay confirmación funcional configurada; Operación correcta = Se ha recibido o enviado un mensaje X12 bueno, confirmación funcional configurada y recibida o se ha enviado una confirmación funcional; Error = Se ha recibido un mensaje X12 malo; Pendiente = Se ha recibido o enviado un mensaje X12 bueno, confirmación funcional configurada y en espera de una confirmación funcional |
| Ack | Estado de confirmación funcional (997).  Aceptada = Se ha recibido o enviado una confirmación funcional positiva; Rechazada = Se ha recibido o enviado una confirmación funcional negativa; Pendiente = Se esperaba una confirmación funcional pero no se recibió; Pendiente = Se generó una confirmación funcional, pero no se pudo enviar al asociado |
| Dirección | Dirección del mensaje X12. |
| Id. de correlación | Identificador para poner en correlación todos los desencadenadores y acciones dentro de una aplicación de lógica. |
| Tipo de mensaje |  Tipo de mensaje EDI X12. |
| ICN | Número de control de intercambio del mensaje X12 |
| TSCN | Número de control de conjunto de transacciones del mensaje X12 |
| Timestamp | Hora en la que la acción X12 procesa el mensaje. |
| | |

4. La selección de una fila de la lista de mensajes AS2 o X12 le lleva a la búsqueda de registros.  La búsqueda de registros enumera todas las acciones que tienen el mismo **Id. de ejecución**
![Seleccionar estado de mensaje](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

## <a name="queries-in-oms-portal"></a>Consultas en el portal de OMS

En la página Buscar, puede crear una consulta y luego, al buscar, puede filtrar los resultados mediante controles de faceta.

### <a name="how-to-create-a-query"></a>Creación de una consulta

1. En la búsqueda de registros, escriba una consulta y seleccione **Guardar**.  [Estos](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md) son los pasos para escribir una consulta. ![Seleccionar la página principal](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery.png)

2. Se abre la opción para **guardar la búsqueda**.  Asigne un **nombre** y una **categoría** y haga clic en **Guardar** .  
![Seleccionar la página principal](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery1.png)

3. Para ver la consulta, seleccione **Favoritos**  .  
![Seleccionar la página principal](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery3.png)

    ![Seleccionar la página principal](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery4.png)

### <a name="how-to-use-a-saved-query"></a>Uso de una consulta guardada

* En la búsqueda de registros, seleccione **Favoritos** para ver las consultas guardadas.  Al seleccionar uno de los resultados de consulta se obtiene ![Seleccionar la página principal](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery5.png)


## <a name="next-steps"></a>Pasos siguientes
[Esquema de seguimiento personalizado](logic-apps-track-integration-account-custom-tracking-schema.md "Learn about Custom Tracking Schema")   
[Esquema de seguimiento de AS2](logic-apps-track-integration-account-as2-tracking-schemas.md "Learn about AS2 Tracking Schema")    
[Esquema de seguimiento de X12](logic-apps-track-integration-account-x12-tracking-schema.md "Learn about X12 Tracking Schema")  
[Más información acerca de Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Jan17_HO4-->


