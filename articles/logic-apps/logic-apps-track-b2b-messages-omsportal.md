---
title: Seguimiento de mensajes B2B en el portal de Operations Management Suite - Azure | Microsoft Docs
description: Seguimiento de mensajes B2B en el portal de Operations Management Suite
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
ms.sourcegitcommit: 2aa32d692da81dc34fafbc9cb6ea72a806f31e5c
ms.openlocfilehash: a4068c70a01f80086ff3fb55f8e101c7dce95dea
ms.lasthandoff: 02/27/2017


---
# <a name="track-b2b-messages-in-the-operations-management-suite-portal"></a>Seguimiento de mensajes B2B en el portal de Operations Management Suite
La comunicación B2B implica el intercambio de mensajes entre dos aplicaciones o procesos empresariales en ejecución. Utilice las siguientes características de seguimiento basado en web del portal de Operations Management Suite para confirmar si los mensajes se procesan correctamente:

* Número y estado de los mensajes
* Del estado de las confirmaciones
* Correlación de mensajes con confirmaciones
* Descripción detallada de errores
* De las funcionalidades de búsqueda

## <a name="prerequisites"></a>Requisitos previos
* Una cuenta de Azure. Puede crear una [cuenta gratuita](https://azure.microsoft.com/free).
* Una cuenta de integración. Puede crear una [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) y configurar el registro. Para configurar el registro, consulte [Supervisión de mensajes B2B](logic-apps-monitor-b2b-message.md).
* Una aplicación lógica. Puede crear un [aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md) y configurar el registro. Para configurar el registro, consulte [Diagnósticos y alertas de Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts).

## <a name="add-logic-apps-b2b-solution-to-the-operations-management-suite-portal"></a>Incorporación de la solución B2B de Logic Apps al portal de Operations Management Suite

1. En Azure Portal, seleccione **Más servicios**, busque log analytics y seleccione **Log Analytics**.   
![Búsqueda de Log Analytics](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)  

2. Seleccione su **Log Analytics**.  
![Selección de Log Analytics](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. Seleccione **Portal de OMS**. Aparecerá la página principal del portal de Operations Management Suite.   
![Examinar el portal de Operations Management Suite](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. Seleccione **Galería de soluciones**.    
![Seleccionar Galería de soluciones](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. Seleccione **Logic Apps B2B**.     
![Selección de Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. Seleccione **Agregar** para agregar mensajes de **Logic Apps B2B** a la página principal.  
![Selecciona Agregar](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

7. **Logic Apps B2B Messages** (Mensajes de Logic Apps B2B) aparece en la página principal.   
![Seleccionar la página principal](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

## <a name="track-data-in-the-operations-management-suite-portal"></a>Seguimiento de datos en el portal de Operations Management Suite

1. Una vez procesados los mensajes, aparece el número de mensajes actualizado.   
![Mensajes actualizados](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

2. Seleccione **Logic Apps B2B Messages** (Mensajes de Logic Apps B2B) en la página principal para ver el estado de los mensajes AS2 y X12.  Los datos se basan en un solo día.
![Seleccionar mensajes de Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

3. Seleccione un mensaje AS2 o X12 por estado para ir a la lista de mensajes. La siguiente captura de pantalla muestra el estado del mensaje AS2. Las descripciones de propiedades del estado de los mensajes AS2 y X12 aparecen más adelante en "Descripciones de propiedades de la lista de mensajes".  
![Selección del estado de mensaje AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)
4. Seleccione una fila en la lista de mensajes AS2 o X12 para ir a la búsqueda de registros.  La búsqueda de registros enumera todas las acciones que tienen el mismo identificador de ejecución.
![Selección del estado del mensaje](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

## <a name="message-list-property-descriptions"></a>Descripciones de propiedades de la lista de mensajes

#### <a name="as2-message-list-property-descriptions"></a>Descripciones de propiedades de la lista de mensajes AS2

| Propiedad | Descripción |
| --- | --- |
| Remitente | Asociado invitado que se define en la configuración de recepción o asociado anfitrión que se define en la configuración de envío de un contrato AS2. |
| Receptor | Asociado anfitrión que se define en la configuración de recepción o asociado invitado que se define en la configuración de envío de un contrato AS2. |
| Aplicación lógica | Aplicación lógica donde se configuran las acciones AS2. |
| Estado | Estado del mensaje AS2 <br>Correcto = Se ha recibido o enviado un mensaje AS2 bueno; MDN sin configurar <br>Correcto = Se ha recibido o enviado un mensaje AS2 bueno; MDN se ha configurado y recibido o se ha enviado MDN <br>Error = Se ha recibido un mensaje AS2 malo; MDN sin configurar <br>Pendiente = Se ha recibido o enviado un mensaje AS2 bueno; MDN se ha configurado y se espera una confirmación funcional |
| Ack | Estado del mensaje MDN. <br>Aceptado = Se ha recibido o enviado una MDN positiva <br>Pendiente = Esperando a recibir o enviar una MDN <br>Rechazado = Se ha recibido o enviado una MDN negativa <br>No necesario = MDN no se ha configurado en el contrato |
| Dirección | Dirección del mensaje AS2. |
| Id. de correlación | Identificador para poner en correlación todos los desencadenadores y acciones dentro de una aplicación lógica. |
| Id. de mensaje |  Identificador del mensaje AS2, de los encabezados del mensaje AS2. |
| Timestamp | Hora en la que la acción AS2 procesa el mensaje. |

#### <a name="x12-message-list-property-descriptions"></a>Descripciones de propiedades de la lista de mensajes X12

| Propiedad | Descripción |
| --- | --- |
| Remitente | Asociado invitado que se define en la configuración de recepción o asociado anfitrión que se define en la configuración de envío de un contrato AS2. |
| Receptor | Asociado anfitrión que se define en la configuración de recepción o asociado invitado que se define en la configuración de envío de un contrato AS2. |
| Aplicación lógica | Aplicación lógica donde se configuran las acciones AS2. |
| Estado | Estado del mensaje X12 <br>Correcto = Se ha recibido o enviado un mensaje X12 bueno; no se ha configurado una confirmación funcional <br>Correcto = Se ha recibido o enviado un mensaje X12 bueno; se ha configurado y recibido una confirmación funcional o se ha enviado una confirmación funcional <br>Error = Se ha recibido o enviado un mensaje X12 malo <br>Pendiente = Se ha recibido o enviado un mensaje X12 bueno; se ha configurado una confirmación funcional y se espera una confirmación funcional. |
| Ack | Estado de confirmación funcional (997) <br>Aceptada = Se ha recibido o enviado una confirmación funcional positiva <br>Rechazada = Se ha recibido o enviado una confirmación funcional negativa <br>Pendiente = Se esperaba una confirmación funcional, pero no se recibió <br>Pendiente = Confirmación funcional generada, pero no se pudo enviar al asociado <br>No necesaria = No se ha configurado una confirmación funcional |
| Dirección | Dirección del mensaje X12. |
| Id. de correlación | Identificador para poner en correlación todos los desencadenadores y acciones dentro de una aplicación lógica. |
| Tipo de mensaje |  Tipo de mensaje EDI X12. |
| ICN | Número de control de intercambio del mensaje X12. |
| TSCN | Número de control de conjunto de transacciones del mensaje X12. |
| Timestamp | Hora en la que la acción X12 procesa el mensaje. |

## <a name="queries-in-the-operations-management-suite-portal"></a>Consultas en el portal de Operations Management Suite

En la página de búsqueda, puede crear una consulta. Al realizar una búsqueda, puede filtrar los resultados mediante controles de faceta.

### <a name="create-a-query"></a>Creación de una consulta

1. En la búsqueda de registros, escriba una consulta y seleccione **Guardar**. Aparece la opción **Guardar búsqueda**. Para escribir una consulta, consulte [Seguimiento de mensajes B2B en el portal de Operations Management Suite mediante una consulta](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md).
![Seleccionar la página principal](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery.png)

2. En **Guardar búsqueda** agregue un **nombre** y **categoría** y luego seleccione **Guardar**.   
![Seleccionar la página principal](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery1.png)

3. Para ver la consulta, seleccione **Favoritos**.    
![Seleccionar la página principal](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery3.png)

    ![Seleccionar la página principal](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery4.png)

### <a name="use-a-saved-query"></a>Uso de una consulta guardada

* En la búsqueda de registros, seleccione **Favoritos** para ver las consultas guardadas.  Para ver los resultados de una consulta, seleccione una consulta.
![Seleccionar la página principal](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery5.png)


## <a name="next-steps"></a>Pasos siguientes
[Esquema de seguimiento personalizado](logic-apps-track-integration-account-custom-tracking-schema.md "Learn about Custom Tracking Schema")   
[Esquema de seguimiento de AS2](logic-apps-track-integration-account-as2-tracking-schemas.md "Learn about AS2 Tracking Schema")    
[Esquema de seguimiento de X12](logic-apps-track-integration-account-x12-tracking-schema.md "Learn about X12 Tracking Schema")  
[Más información acerca de Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")

