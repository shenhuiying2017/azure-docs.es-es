---
title: Seguimiento de mensajes de AS2, X12 y EDIFACT mediante una consulta - Azure Logic Apps | Microsoft Docs
description: Uso de consultas para realizar un seguimiento de mensajes negocio a negocio en el portal de Operations Management Suite
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
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 67313e4ff780e6c64dfc3d767ea49167c2a390f4
ms.openlocfilehash: 701a3bbadb340c9390b486a5295eced056db70a0


---
# <a name="track-b2b-messages-in-the-operations-management-suite-portal-by-using-a-query"></a>Seguimiento de mensajes B2B en el portal de Operations Management Suite mediante una consulta
Para realizar el seguimiento de mensajes de negocio a negocio (B2B) en el portal de Operations Management Suite, puede crear una consulta que filtra los datos de un número de control de intercambio específico.

## <a name="prereqs"></a>Requisitos previos

Para la depuración y para obtener más información de diagnóstico, active los diagnósticos en la [cuenta integración](logic-apps-monitor-b2b-message.md) para las [aplicaciones lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts) que tienen X12 conectores. Después, realice los pasos necesarios para [publicar datos de diagnóstico](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) en el portal de Operations Management Suite.

## <a name="search-for-an-interchange-control-number"></a>Búsqueda de un número de control de intercambio

1. En la página de inicio, seleccione **Búsqueda de registros**.  
![Selección de Búsqueda de registros](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

2. En el cuadro de búsqueda, escriba **Type="AzureDiagnostics"**. Para filtrar los datos, seleccione **Agregar**.  
![Seleccionar consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

3. Escriba **interchange**, seleccione **event_record_messageProperties_interchangeControlNumber_s** y haga clic en **Agregar**.  
![Agregar filtro](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query2.png)

4. Seleccione el número de control por el que desea filtrar los datos y después seleccione **Aplicar**.  
![Buscar en el número de control](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query3.png)

5. Encuentre la consulta creada para filtrar los datos para el número de control seleccionado.   
![Encontrar la consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query4.png)

6. Escriba un nombre para la consulta y luego guárdelo.   
![Guardar la consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query5.png)

7. Para ver la consulta y usarla en futuras búsquedas, seleccione **Favoritos**.  
![Ver la consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query7.png)

8. Puede actualizar la consulta para buscar un nuevo número de control de intercambio.  
![Actualizar la consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query6.png)


## <a name="next-steps"></a>Pasos siguientes
* Más información sobre los [esquemas de seguimiento personalizado](logic-apps-track-integration-account-custom-tracking-schema.md).   
* Más información sobre los [esquemas de seguimiento de AS2](logic-apps-track-integration-account-as2-tracking-schemas.md).    
* Más información sobre los [esquemas de seguimiento de X12](logic-apps-track-integration-account-x12-tracking-schema.md).  
* Más información sobre [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).



<!--HONumber=Jan17_HO5-->


