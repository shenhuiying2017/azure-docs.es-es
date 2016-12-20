---
title: "Supervisión de mensajes B2B | Microsoft Docs"
description: "Supervisión de una cuenta de integración"
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
ms.date: 10/21/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 8ce3585a557f6b2c9fa4a4c85142cdce9357a90b


---
# <a name="monitor-b2b-messages"></a>Supervisión de mensajes B2B
La comunicación B2B implica intercambios de mensajes entre dos aplicaciones o procesos empresariales en ejecución. La relación define un contrato entre los procesos empresariales. Una vez que se ha establecido la comunicación, es preciso que haya alguna forma de supervisar si la comunicación funciona según lo esperado.  Se ha implementado el seguimiento de mensajes para los protocolos B2B: AS2, X12 y EDIFACT.  Puede configurar la cuenta de integración para que use Diagnostics, con el fin de aumentar el grado de detalle y realizar la depuración

## <a name="prerequisites"></a>Requisitos previos
* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)
* Una cuenta de integración; puede crear un [cuenta de integración](app-service-logic-enterprise-integration-create-integration-account.md)
* Una aplicación lógica; puede crear un [aplicación lógica](app-service-logic-create-a-logic-app.md) y habilitar el registro; los pasos para hacerlos se encuentran [aquí](app-service-logic-monitor-your-logic-apps.md)

## <a name="enable-logging-for-an-integration-account"></a>Habilitación del registro en una cuenta de integración
El registro en una cuenta de integración se puede habilitar con **Azure Portal** o con **Monitor**

### <a name="enable-logging-with-azure-portal"></a>Habilitación del registro con Azure Portal

1. Seleccione **cuenta integración** y seleccione **registros de diagnóstico** 
![seleccionar cuenta de integración](./media/app-service-logic-monitor-integration-account/Pic5.png)  

2. Seleccione su **suscripción** y **grupo de recursos**, **cuenta integración** en Tipo de recurso y seleccione su **cuenta integración** en la lista desplegable Recurso para habilitar los diagnósticos.  Haga clic en **Activar diagnósticos** para habilitar los diagnósticos en la cuenta de integración seleccionada               
![seleccionar cuentas de integración](./media/app-service-logic-monitor-integration-account/Pic2.png) 

3. Seleccione el estado **ACTIVADO**       
![Activar diagnósticos](./media/app-service-logic-monitor-integration-account/Pic3.png) 

4. Seleccione **Send to Log Analytics** (Enviar a Log Analytics) y configure Log Analytics para que emita datos               
![Activar diagnósticos](./media/app-service-logic-monitor-integration-account/Pic4.png)

### <a name="enable-logging-with-monitor"></a>Habilitación del registro con Monitor

1. Seleccione **Monitor** y haga clic en **Registros de diagnósticos**   
![seleccionar Monitor](./media/app-service-logic-monitor-integration-account/Pic1.png)

2. Seleccione su **suscripción** y **grupo de recursos**, **cuenta integración** en Tipo de recurso y seleccione su **cuenta integración** en la lista desplegable Recurso para habilitar los diagnósticos.  Haga clic en **Activar diagnósticos** para habilitar los diagnósticos en la cuenta de integración seleccionada               
![seleccionar cuentas de integración](./media/app-service-logic-monitor-integration-account/Pic2.png)

3. Seleccione el estado **ACTIVADO**       
![Activar diagnósticos](./media/app-service-logic-monitor-integration-account/Pic3.png) 

4. Seleccione **Send to Log Analytics** (Enviar a Log Analytics) y configure Log Analytics para que emita datos ![Activar diagnósticos](./media/app-service-logic-monitor-integration-account/Pic4.png)

## <a name="extending-your-solutions"></a>Ampliación de las soluciones
Además de **Log Analytics**, puede configurar su cuenta de integración y [Logic Apps](./app-service-logic-monitor-your-logic-apps.md) en un Centro de eventos o una cuenta de Storage         
![Configuración de Azure Diagnostics](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

Esta telemetría del Centro de eventos o de Storage se puede usar en otros servicios, como [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) y [Power BI](https://powerbi.com), para supervisar en tiempo real los flujos de trabajo de integración.

## <a name="supported-tracking-schema"></a>Esquema de seguimiento que se admite
Se admiten los siguientes tipos de esquema de seguimiento.  Todos ellos tienen esquemas fijos, excepto el tipo personalizado.

* [Esquema de seguimiento personalizado](app-service-logic-track-integration-account-custom-tracking-shema.md)   
* [Esquema de seguimiento de AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [Esquema de seguimiento de X12](app-service-logic-track-integration-account-x12-tracking-shemas.md)  

## <a name="next-steps"></a>Pasos siguientes
[Tracking B2B messages in OMS portal](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")  (Seguimiento de mensajes B2B en el Portal de OMS)  
[Más información acerca de Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 




<!--HONumber=Nov16_HO3-->


