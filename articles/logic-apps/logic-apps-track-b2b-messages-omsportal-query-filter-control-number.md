---
title: Consulta de mensajes B2B en Operations Management Suite - Azure Logic Apps | Microsoft Docs
description: Cree consultas para hacer seguimiento de los mensajes AS2, X12 y EDIFACT en Operations Management Suite
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
ms.openlocfilehash: 2748d3d3daf7c13dca05f663a4a088598e1b3605
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="query-for-as2-x12-and-edifact-messages-in-the-microsoft-operations-management-suite-oms"></a>Consulta de mensajes AS2, X12 y EDIFACT en Microsoft Operations Management Suite (OMS)

Para encontrar los mensajes AS2, X12 o EDIFACT a los que hace seguimiento con [Azure Log Analytics](../log-analytics/log-analytics-overview.md) en [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md), puede crear consultas que filtren las acciones según criterios específicos. Por ejemplo, puede encontrar mensajes según un número de control de intercambio específico.

## <a name="requirements"></a>Requisitos

* Una aplicación lógica configurada con registro de diagnósticos. Obtenga información sobre [cómo crear una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md) y [cómo configurar el registro de esa aplicación lógica](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Una cuenta de integración configurada con supervisión y registro. Obtenga información sobre [cómo crear una cuenta de integración](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) y [cómo configurar la supervisión y el registro de esa cuenta](../logic-apps/logic-apps-monitor-b2b-message.md).

* Si aún no lo ha hecho, [publique datos de diagnóstico para Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) y [establezca el seguimiento de mensajes en OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> Después de haber satisfecho los requisitos anteriores, debería tener un área de trabajo en [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Debería usar la misma área de trabajo de OMS para realizar el seguimiento de la comunicación B2B en OMS. 
>  
> Si no tiene un área de trabajo de OMS, aprenda a [crear un área de trabajo de OMS](../log-analytics/log-analytics-get-started.md).

## <a name="create-message-queries-with-filters-in-the-operations-management-suite-portal"></a>Creación de consultas de mensajes con filtros en el portal de Operations Management Suite

En este ejemplo se muestra cómo puede buscar mensajes según su número de control de intercambio.

> [!TIP] 
> Si conoce el nombre del área de trabajo de OMS, vaya a la página principal del área de trabajo (`https://{your-workspace-name}.portal.mms.microsoft.com`) y empiece en el paso 4. De lo contrario, empiece en el paso 1.

1. En [Azure Portal](https://portal.azure.com), elija **Más servicios**. Busque "log analytics" y luego elija **Log Analytics** como se muestra aquí:

   ![Búsqueda de Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/browseloganalytics.png)

2. En **Log Analytics**, busque y seleccione el área de trabajo de OMS.

   ![Selección del área de trabajo de OMS](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/selectla.png)

3. En **Administración**, elija **Portal de OMS**.

   ![Selección de Portal de OMS](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/omsportalpage.png)

4. En la página principal de OMS, seleccione **Búsqueda de registros**.

   ![Selección de "Búsqueda de registros" en la página principal de OMS](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   O bien

   ![Selección de "Búsqueda de registros" en el menú OMS](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

5. En el cuadro de búsqueda, especifique un campo que quiera buscar y pulse **Entrar**. Cuando empiece a escribir, OMS le mostrará posibles coincidencias y operaciones que puede usar. Más información sobre [cómo buscar datos en Log Analytics](../log-analytics/log-analytics-log-searches.md).

   En este ejemplo se buscan eventos con **Type=AzureDiagnostics**.

   ![Empezar a escribir la cadena de consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-start-query.png)

6. En la barra de la izquierda, elija el período de tiempo que desea consultar. Para agregar un filtro a la consulta, elija **+Agregar**.

   ![Agregar filtro a consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

7. En **Agregar filtros**, escriba el nombre del filtro para poder encontrar el que quiere. Seleccione el filtro y elija **+Agregar**.

   Para buscar el número de control de intercambio, este ejemplo busca la palabra "intercambio" y selecciona **event_record_messageProperties_interchangeControlNumber_s** como filtro.

   ![Selección de filtro](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-add-filter.png)

9. En la barra de la izquierda, seleccione el valor de filtro que quiere usar y elija **Aplicar**.

   En este ejemplo se selecciona el número de control de intercambio para los mensajes que deseamos.

   ![Selección de valor de filtro](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-select-filter-value.png)

10. Ahora vuelva a la consulta que está creando. La consulta se actualizó con el valor y el evento de filtro seleccionados. Los resultados anteriores también se filtraron.

    ![Consulta con los resultados filtrados](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-your-query-for-future-use"></a>Guardar la consulta para futuro uso

1. Desde la consulta en la página **Búsqueda de registros**, elija **Guardar**. Asigne un nombre a la consulta, seleccione una categoría y elija **Guardar**.

   ![Asignación de nombre y categoría a la consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-save.png)

2. Para ver la consulta, elija **Favoritos**.

   ![Elegir "Favoritos"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-favorites.png)

3. En **Búsquedas guardadas**, seleccione la consulta para poder ver los resultados. Edite la consulta para actualizarla y poder encontrar distintos resultados.

   ![Selección de la consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="find-and-run-saved-queries-in-the-operations-management-suite-portal"></a>Búsqueda y ejecución de consultas guardadas en el portal de Operations Management Suite

1. Abra la página principal del área de trabajo de OMS (`https://{your-workspace-name}.portal.mms.microsoft.com`) y elija **Búsqueda de registros**.

   ![Selección de "Búsqueda de registros" en la página principal de OMS](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   O bien

   ![Selección de "Búsqueda de registros" en el menú OMS](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

2. En la página principal de **Búsqueda de registros**, elija **Favoritos**.

   ![Elegir "Favoritos"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-favorites.png)

3. En **Búsquedas guardadas**, seleccione la consulta para poder ver los resultados. Edite la consulta para actualizarla y poder encontrar distintos resultados.

   ![Selección de la consulta](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="next-steps"></a>Pasos siguientes

* [Esquemas de seguimiento de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de seguimiento de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de seguimiento personalizados](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)