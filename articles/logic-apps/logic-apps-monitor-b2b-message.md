---
title: "Supervisión de transacciones B2B y configuración de los registros: Azure Logic Apps | Microsoft Docs"
description: "Supervisión de mensajes AS2, X12 y EDIFACT, inicio del registro de diagnóstico para la cuenta de integración"
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
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: f717dae9a70a96944b623f22b90cf8c5a943f382
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="monitor-and-set-up-diagnostics-logging-for-b2b-communication-in-integration-accounts"></a>Supervisión y configuración del registro de diagnóstico para la comunicación B2B en cuentas de integración

Después de configurar la comunicación B2B entre dos procesos o aplicaciones empresariales mediante la cuenta de integración, esas entidades pueden intercambiar mensajes entre sí. Para confirmar que esta comunicación funciona según lo esperado, puede configurar la supervisión de los mensajes AS2, X12 y EDIFACT, junto con el registro de diagnóstico de la cuenta de integración mediante el servicio [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Este servicio en [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) supervisa los entornos locales y en la nube, lo que le permiten conservar la disponibilidad y el rendimiento, y también recopila detalles de entorno de tiempo de ejecución y eventos para lograr una depuración más completa. También puede [usar los datos de diagnóstico con otros servicios](#extend-diagnostic-data), como Azure Storage y Azure Event Hubs.

## <a name="requirements"></a>Requisitos

* Una aplicación lógica configurada con registro de diagnósticos. Obtenga información sobre [cómo configurar el registro para esa aplicación lógica](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

  > [!NOTE]
  > Una vez satisfecho este requisito, debería tener un área de trabajo en [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Debe usar la misma área de trabajo de OMS cuando configure el registro para la cuenta de integración. Si no tiene un área de trabajo de OMS, aprenda a [crear un área de trabajo de OMS](../log-analytics/log-analytics-get-started.md).

* Una cuenta de integración vinculada a la aplicación lógica. Aprenda a [crear una cuenta de integración con un vínculo a la aplicación lógica](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging-for-your-integration-account"></a>Activación del registro de diagnóstico para la cuenta de integración

Puede activar el registro directamente desde la cuenta de integración o [mediante el servicio de Azure Monitor](#azure-monitor-service). Azure Monitor ofrece supervisión básica con datos de nivel de infraestructura. Más información sobre [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md).

### <a name="turn-on-diagnostics-logging-directly-from-your-integration-account"></a>Activación del registro de diagnóstico directamente desde la cuenta de integración

1. En [Azure Portal](https://portal.azure.com), busque y seleccione la cuenta de integración. En **Supervisión**, elija **Registros de diagnóstico** como se muestra a continuación:

   ![Buscar y seleccionar la cuenta de integración, elegir "Registros de diagnóstico"](media/logic-apps-monitor-b2b-message/integration-account-diagnostics.png)

2. Cuando selecciona la cuenta de integración, los valores siguientes se seleccionan de forma automática. Si los valores son correctos, elija **Activar diagnóstico**. De lo contrario, seleccione los valores que desea:

   1. En **Suscripción**, seleccione la suscripción de Azure que usa con la cuenta de integración.
   2. En **Grupo de recursos**, seleccione el grupo de recursos que usa con la cuenta de integración.
   3. En **Tipo de recurso**, seleccione **Cuentas de integración**. 
   4. En **Resource**, seleccione la cuenta de integración. 
   5. Elija **Activar diagnóstico**.

   ![Configuración de diagnósticos para la cuenta de integración](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. En **Configuración de diagnóstico**, **Estado**, elija **Activado**.

   ![Activación de Diagnósticos de Azure](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Ahora seleccione el área de trabajo de OMS y los datos para usar en el registro de la siguiente manera:

   1. Seleccione **Enviar a Log Analytics**. 
   2. En **Log Analytics**, elija **Configurar**. 
   3. En **Áreas de trabajo de OMS**, seleccione el área de trabajo de OMS que va a usar para el registro.
   4. En **Registro**, seleccione la categoría **IntegrationAccountTrackingEvents**.
   5. Elija **Guardar**.

   ![Configuración de Log Analytics para que pueda enviar datos de diagnóstico a un registro](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Ahora [configure el seguimiento de los mensajes B2B en OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-diagnostics-logging-through-azure-monitor"></a>Activación del registro de diagnóstico mediante Azure Monitor

1. En [Azure Portal](https://portal.azure.com), en el menú principal de Azure, elija **Supervisar**, **Registro de diagnóstico**. Luego, seleccione la cuenta de integración como se muestra aquí:

   ![Elija "Supervisar", "Registros de diagnóstico", seleccione la cuenta de integración.](media/logic-apps-monitor-b2b-message/monitor-service-diagnostics-logs.png)

2. Cuando selecciona la cuenta de integración, los valores siguientes se seleccionan de forma automática. Si los valores son correctos, elija **Activar diagnóstico**. De lo contrario, seleccione los valores que desea:

   1. En **Suscripción**, seleccione la suscripción de Azure que usa con la cuenta de integración.
   2. En **Grupo de recursos**, seleccione el grupo de recursos que usa con la cuenta de integración.
   3. En **Tipo de recurso**, seleccione **Cuentas de integración**.
   4. En **Resource**, seleccione la cuenta de integración.
   5. Elija **Activar diagnóstico**.

   ![Configuración de diagnósticos para la cuenta de integración](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. En **Configuración de diagnóstico**, elija **Activado**.

   ![Activación de Diagnósticos de Azure](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Ahora seleccione el área de trabajo de OMS y la categoría de evento para el registro como se muestra:

   1. Seleccione **Enviar a Log Analytics**. 
   2. En **Log Analytics**, elija **Configurar**. 
   3. En **Áreas de trabajo de OMS**, seleccione el área de trabajo de OMS que va a usar para el registro.
   4. En **Registro**, seleccione la categoría **IntegrationAccountTrackingEvents**.
   5. Cuando termine, seleccione **Guardar**.

   ![Configuración de Log Analytics para que pueda enviar datos de diagnóstico a un registro](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Ahora [configure el seguimiento de los mensajes B2B en OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Extensión de cómo y dónde usa los datos de diagnóstico con otros servicios

Además de con Azure Log Analytics, puede usar los datos de diagnóstico de la aplicación lógica con otros servicios de Azure, por ejemplo: 

* [Archivar registros de Diagnósticos de Azure en Azure Storage](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Transmitir registros de Diagnósticos de Azure a Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Luego puede obtener supervisión en tiempo real mediante la telemetría y los análisis de otros servicios, como [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) y [Power BI](../log-analytics/log-analytics-powerbi.md). Por ejemplo:

* [Transmitir datos de Event Hubs a Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analizar datos que se están transmitiendo con Stream Analytics y crear un panel de análisis en tiempo real en Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Según las opciones que quiera configurar, primero asegúrese de [crear una cuenta de Azure Storage](../storage/common/storage-create-storage-account.md) o [crear un centro de eventos de Azure](../event-hubs/event-hubs-create.md). Luego seleccione las opciones para el envío de los datos de diagnóstico:

![Envío de los datos a una cuenta de Azure Storage o a un centro de eventos](./media/logic-apps-monitor-b2b-message/storage-account-event-hubs.png)

> [!NOTE]
> Los períodos de retención solo se aplican cuando se usa una cuenta de almacenamiento.

## <a name="supported-tracking-schemas"></a>Esquemas de seguimiento compatibles

Azure admite los siguientes tipos de esquema de seguimiento, de los que todos, salvo el tipo personalizado, tienen esquemas fijos.

* [Esquema de seguimiento de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de seguimiento de X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquema de seguimiento personalizado](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Pasos siguientes

* [Seguimiento de mensajes B2B en OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "Seguimiento de mensajes B2B en OMS")
* [Más información sobre Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")

