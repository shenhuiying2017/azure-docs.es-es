---
title: Seguimiento de mensajes B2B en el Portal de OMS | Microsoft Docs
description: Seguimiento de mensajes B2B en el Portal de OMS
author: padmavc
manager: erikre
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
ms.sourcegitcommit: 7f64fe625b7c3b7e58f86154b9d601eff03e31d3
ms.openlocfilehash: 854db453a3af459c0398c4b88a6789c87747e920


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
* Una cuenta de integración; puede crear un [cuenta de integración](app-service-logic-enterprise-integration-create-integration-account.md) y habilitar el registro; [aquí](app-service-logic-monitor-b2b-message.md) puede buscar los pasos que deben darse para hacerlo
* Una aplicación lógica; puede crear un [aplicación lógica](app-service-logic-create-a-logic-app.md) y habilitar el registro; los pasos para hacerlos se encuentran [aquí](app-service-logic-monitor-your-logic-apps.md#azure-diagnostics-and-alerts)

## <a name="adding-logic-apps-b2b-solution-to-oms-portal"></a>Incorporación de una solución B2B de Logic Apps al Portal de OMS

1. Seleccione **Más servicios** en el portal, busque **Log Analytics** y seleccione **Log Analytics**
![Buscar Log Analytics](./media/app-service-logic-track-b2b-messages-omsportal/browseloganalytics.png)  

2. Seleccione **Log Analytics**
![Seleccionar Log Analytics](./media/app-service-logic-track-b2b-messages-omsportal/selectla.png)

3. Seleccione **Portal de OMS**, se abre la página principal del Portal de OMS ![Examinar Portal de OMS](./media/app-service-logic-track-b2b-messages-omsportal/omsportalpage.png)

4. Seleccione **Galería de soluciones**    
![Seleccionar Galería de soluciones](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage1.png)

5. Seleccione **B2B de Logic Apps**
![Seleccionar B2B de Logic Apps](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage2.png)

6. Haga clic en **Agregar** para agregar **Mensajes de Logic Apps B2B** a la página principal ![Seleccionar Agregar](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage3.png)

7. Examine la página principal para ver **Mensajes de Logic Apps B2B**
![Seleccione página de principal](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage4.png)

## <a name="tracking-data-in-oms-portal"></a>Datos de seguimiento en el portal de OMS

1. Proceso posterior al mensaje; la página principal se actualiza con el número de mensajes ![Seleccionar página principal](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage6.png)

2. Si se selecciona **Mensajes de Logic Apps B2B** en la página, pasará al estado de los mensajes de AS2 y X12.  Los datos se basan en el último día.
![Seleccionar mensajes de Logic Apps B2B](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage5.png)

3. Si selecciona los mensajes de AS2 o X12 de estado, pasará a la lista de mensajes ![Seleccionar estado de mensaje de AS2](./media/app-service-logic-track-b2b-messages-omsportal/as2messagelist.png)

    ![Seleccionar estado de mensaje de X12](./media/app-service-logic-track-b2b-messages-omsportal/x12messagelist.png)

4. La selección de una fila de la lista de mensajes de AS2 o X12 le lleva a la búsqueda de registros.  La búsqueda de registros enumera todas las acciones que tienen el mismo **Id. de ejecución**
![Seleccionar estado de mensaje](./media/app-service-logic-track-b2b-messages-omsportal/logsearch.png)

## <a name="queries-in-oms-portal"></a>Consultas en el portal de OMS

En la página Buscar, puede crear una consulta y luego, al buscar, puede filtrar los resultados mediante controles de faceta.

### <a name="how-to-create-a-query"></a>Creación de una consulta

1. En la búsqueda de registros, escriba una consulta y seleccione **Guardar**.  [Estos](app-service-logic-track-b2b-messages-omsportal-query-filter-control-number.md) son los pasos para escribir una consulta. ![Seleccionar la página principal](./media/app-service-logic-track-b2b-messages-omsportal/logsearchaddquery.png)

2. Se abre la opción para **guardar la búsqueda**.  Asigne un **nombre** y una **categoría** y haga clic en **Guardar** .  
![Seleccionar la página principal](./media/app-service-logic-track-b2b-messages-omsportal/logsearchaddquery1.png)

3. Para ver la consulta, seleccione **Favoritos**  .  
![Seleccionar la página principal](./media/app-service-logic-track-b2b-messages-omsportal/logsearchaddquery3.png)

    ![Seleccionar la página principal](./media/app-service-logic-track-b2b-messages-omsportal/logsearchaddquery4.png)

### <a name="how-to-use-a-saved-query"></a>Uso de una consulta guardada

1. En la búsqueda de registros, seleccione **Favoritos** para ver las consultas guardadas.  Al seleccionar uno de los resultados de consulta se obtiene ![Seleccionar la página principal](./media/app-service-logic-track-b2b-messages-omsportal/logsearchaddquery5.png)


## <a name="next-steps"></a>Pasos siguientes
[Esquema de seguimiento personalizado](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Tracking Schema")   
[Esquema de seguimiento de AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Tracking Schema")    
[Esquema de seguimiento de X12](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")  
[Más información acerca de Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Nov16_HO3-->


