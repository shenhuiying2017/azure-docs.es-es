---
title: Seguimiento de mensajes B2B en el portal de Operations Management Suite mediante una consulta | Microsoft Docs
description: "Obtenga información sobre cómo realizar un seguimiento de mensajes B2B en el portal de Operations Management Suite mediante una consulta."
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
ms.sourcegitcommit: 684ab31903369070a235b35eb0b8c9adffd309e7
ms.openlocfilehash: 08689b8f57d5b29a47b98eac812eaee285cd13a0


---
# <a name="track-b2b-messages-in-the-operations-management-suite-portal-by-using-a-query"></a>Seguimiento de mensajes B2B en el portal de Operations Management Suite mediante una consulta
Para realizar el seguimiento de mensajes de negocio a negocio (B2B) en el portal de Operations Management Suite, puede crear una consulta que filtra los datos de un número de control de intercambio específico.

## <a name="prerequisites"></a>Requisitos previos

Para la depuración y para obtener más información de diagnóstico, active los diagnósticos en la [cuenta integración](app-service-logic-monitor-b2b-message.md) para las [aplicaciones lógicas](app-service-logic-monitor-your-logic-apps.md#azure-diagnostics-and-alerts) que tienen X12 conectores. Después, realice los pasos necesarios para [publicar datos de diagnóstico](app-service-logic-track-b2b-messages-omsportal.md) en el portal de Operations Management Suite.

## <a name="to-create-a-query-to-search-for-a-specific-interchange-control-number"></a>Para crear una consulta para buscar un número de control de intercambio específico

1. En la página de inicio, seleccione **Búsqueda de registros**.  
![Selección de Búsqueda de registros](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

2. En el cuadro de búsqueda, escriba **Type="AzureDiagnostics"**. Para filtrar los datos, seleccione **Agregar**.  
![Seleccionar consulta](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

3. Escriba **interchange**, seleccione **event_record_messageProperties_interchangeControlNumber_s** y haga clic en **Agregar**.  
![Agregar filtro](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query2.png)

4. Seleccione el número de control por el que desea filtrar los datos y después seleccione **Aplicar**.  
![Buscar en el número de control](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query3.png)

5. Encuentre la consulta creada para filtrar los datos para el número de control seleccionado.   
![Encontrar la consulta](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query4.png)

6. Escriba un nombre para la consulta y luego guárdelo.   
![Guardar la consulta](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query5.png)

7. Para ver la consulta y usarla en futuras búsquedas, seleccione **Favoritos**.  
![Ver la consulta](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query7.png)

8. Puede actualizar la consulta para buscar un nuevo número de control de intercambio.  
![Actualizar la consulta](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query6.png)


## <a name="next-steps"></a>Pasos siguientes
* Más información sobre los [esquemas de seguimiento personalizado](app-service-logic-track-integration-account-custom-tracking-shema.md).   
* Más información sobre los [esquemas de seguimiento de AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md).    
* Más información sobre los [esquemas de seguimiento de X12](app-service-logic-track-integration-account-x12-tracking-shemas.md).  
* Más información sobre [Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md).



<!--HONumber=Dec16_HO3-->


