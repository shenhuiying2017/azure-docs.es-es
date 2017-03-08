---
title: "Supervisión de mensajes en transacciones B2B - Azure Logic Apps | Microsoft Docs"
description: "Supervise y realice un seguimiento de los mensajes durante comunicaciones B2B entre procesos y aplicaciones que usan Logic Apps en su cuenta de integración."
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
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5913c81088724ef946ae147f4f3154fa6aefd22e
ms.openlocfilehash: dc760b4c08d0e1afff3bc1276f6ed2367d67629e
ms.lasthandoff: 03/01/2017


---
# <a name="start-or-enable-logging-of-as2-x12-and-edifact-messages-to-monitor-success-errors-and-message-properties"></a>Inicio o habilitación del registro de mensajes AS2, X12 y EDIFACT para supervisar propiedades de mensaje, errores y éxitos

La comunicación B2B implica intercambios de mensajes entre dos aplicaciones o procesos empresariales en ejecución. La relación define un contrato entre los procesos empresariales. Una vez establecida la comunicación, puede configurar la supervisión de mensajes para comprobar que la comunicación funciona según lo previsto. Puede configurar la cuenta de integración para que use diagnósticos, con el fin de aumentar el grado de detalle y realizar la depuración.

El seguimiento de mensajes está disponible para estos protocolos B2B: AS2, X12 y EDIFACT. 

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free).
* Una cuenta de integración; puede crear una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md).
* Una aplicación lógica; puede crear una [aplicación lógica](logic-apps-create-a-logic-app.md) y [habilitar el registro](logic-apps-monitor-your-logic-apps.md).

## <a name="enable-logging-for-an-integration-account"></a>Habilitación del registro en una cuenta de integración

El registro en una cuenta de integración se puede habilitar con **Azure Portal** o con **Monitor**.

### <a name="enable-logging-with-azure-portal"></a>Habilitación del registro con Azure Portal

1. Seleccione su cuenta de integración y, a continuación, seleccione **Registros de diagnósticos**.

    ![Selección de la cuenta de integración](media/logic-apps-monitor-b2b-message/pic5.png)

2. Seleccione su **suscripción** y **grupo de recursos**. En **Tipo de recurso**, seleccione **Cuentas de integración**. En **Recurso**, seleccione su cuenta de integración. Haga clic en **Activar diagnósticos** para habilitar los diagnósticos en la cuenta de integración seleccionada.

    ![Configuración de diagnósticos para la cuenta de integración](media/logic-apps-monitor-b2b-message/pic2.png)

3. Seleccione el estado **ACTIVADO**.

    ![Activación del estado de diagnóstico](media/logic-apps-monitor-b2b-message/pic3.png)

4. Seleccione **Send to Log Analytics** (Enviar a Log Analytics) y configure Log Analytics para que emita datos.

    ![Envío de datos de diagnóstico a un registro](media/logic-apps-monitor-b2b-message/pic4.png)

### <a name="enable-logging-with-monitor"></a>Habilitación del registro con Monitor

0. Seleccione **Monitor** y, a continuación, **Registros de diagnósticos**.

    ![Selección de Monitor y Registros de diagnósticos](media/logic-apps-monitor-b2b-message/pic1.png)

0. Seleccione su **suscripción** y **grupo de recursos**. En **Tipo de recurso**, seleccione **Cuentas de integración**. En **Recurso**, seleccione su cuenta de integración. Haga clic en **Activar diagnósticos** para habilitar los diagnósticos en la cuenta de integración seleccionada.

    ![Configuración de diagnósticos para la cuenta de integración](media/logic-apps-monitor-b2b-message/pic2.png)

0. Seleccione el estado **ACTIVADO**.

    ![Activación del estado de diagnóstico](media/logic-apps-monitor-b2b-message/pic3.png) 

0. Seleccione **Send to Log Analytics** (Enviar a Log Analytics) y configure **Log Analytics** para que emita datos.

    ![Envío de datos de diagnóstico a un registro](media/logic-apps-monitor-b2b-message/pic4.png)

## <a name="extend-your-solutions"></a>Ampliación de las soluciones

Además de **Log Analytics**, puede configurar su cuenta de integración y [Logic Apps](./logic-apps-monitor-your-logic-apps.md) en un centro de eventos o una cuenta de almacenamiento.

![Configuración de Diagnósticos de Azure](./media/logic-apps-monitor-your-logic-apps/diagnostics.png)

Esta telemetría del Centro de eventos o de Storage se puede usar en otros servicios, como [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) y [Power BI](https://powerbi.com), para supervisar en tiempo real los flujos de trabajo de integración.

## <a name="supported-tracking-schema"></a>Esquema de seguimiento que se admite

Se admiten los siguientes tipos de esquema de seguimiento, de los que todos, salvo el tipo personalizado, tienen esquemas fijos.

* [Esquema de seguimiento personalizado](logic-apps-track-integration-account-custom-tracking-schema.md)
* [Esquema de seguimiento de AS2](logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquema de seguimiento de X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Pasos siguientes

[Seguimiento de mensajes B2B en el Portal de OMS](logic-apps-track-b2b-messages-omsportal.md "Seguimiento de mensajes B2B")

[Más información sobre Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")


