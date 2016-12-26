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
ms.sourcegitcommit: 6e56dae0ab30612a7794bd39e6eba2f84bcecc19
ms.openlocfilehash: eae37a357e3e1c00310c7c638d3164118403ee9f


---
# <a name="create-a-query-in-oms-portal"></a>Creación de una consulta en el portal de OMS 
Pasos para crear una consulta que filtra los datos para un número de control de intercambio específico

## <a name="prerequisites"></a>Requisitos previos

Habilite los diagnósticos para su [cuenta de integración](app-service-logic-monitor-b2b-message.md), [aplicaciones lógicas](app-service-logic-monitor-your-logic-apps.md#azure-diagnostics-and-alerts) que tengan conectores X12 para unos detalles y depuración más enriquecidos.  Siga los pasos [aquí](app-service-logic-track-b2b-messages-omsportal.md) para publicar datos de diagnóstico en el portal de OMS.

## <a name="create-a-query-to-search-data-for-a-specific-interchange-control-number"></a>Creación de una consulta para buscar datos para un número de control de intercambio específico

1. Selección de **Búsqueda de registros** en la página principal  
![Selección de Búsqueda de registros](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

2. Escriba **Type="AzureDiagnostics"** en la ventana de búsqueda; se extraen todos los datos.  Haga clic en **Agregar** para filtrar los datos.  
![Seleccionar consulta](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

3. Escriba **interchange**, seleccione **event_record_messageProperties_interchangeControlNumber_s** y haga clic en **Agregar**  
![Seleccionar número de control](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query2.png)

4. Seleccione el número de control por el que desea filtrar los datos y haga clic en **Aplicar**.  
![Seleccionar número de control](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query3.png)

5. Puede encontrar la consulta creada para filtrar los datos para el número de control seleccionado.   
![Seleccionar número de control](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query4.png)

6. Asigne un nombre a la consulta y guárdela.   
![Seleccionar número de control](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query5.png) 

7. Seleccione **favoritos** para ver la consulta y utilizarla en búsquedas futuras.  
![Seleccionar número de control](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query7.png)

8. La consulta se puede actualizar para buscar un nuevo número de control de intercambio.  
![Seleccionar número de control](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query6.png) 


## <a name="next-steps"></a>Pasos siguientes
[Esquema de seguimiento personalizado](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Tracking Schema")   
[Esquema de seguimiento de AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Tracking Schema")    
[Esquema de seguimiento de X12](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")  
[Más información acerca de Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Nov16_HO3-->


