---
title: "Supervisión de API Management con Azure Monitor | Microsoft Docs"
description: Aprenda a supervisar el servicio Azure API Management con Azure Monitor.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 717e033aa4bbd4dd8ebcc727c3f551aee81770dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-api-management-with-azure-monitor"></a>Supervisión de API Management con Azure Monitor
Azure Monitor es un servicio de Azure que proporciona un único origen para la supervisión de todos los recursos de Azure. Con Azure Monitor, puede visualizar, consultar, redirigir y archivar las métricas y los registros procedentes de recursos de Azure como API Management, así como tomar medidas relacionadas. 

En el vídeo siguiente se muestra cómo supervisar API Management con Azure Monitor. Para más información acerca de Azure Monitor, consulte [Introducción a Azure Monitor]. 


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>
 
## <a name="metrics"></a>Métricas
Actualmente, API Management emite cinco métricas y está previsto agregar más en el futuro. Estas métricas se emiten cada minuto, lo que le ofrece visibilidad casi en tiempo real sobre el estado y el mantenimiento de las API. A continuación, se presenta un resumen de las métricas:
* Solicitudes de puerta de enlace en total: el número de solicitudes API en el período. 
* Solicitudes de puerta de enlace correctas: el número de solicitudes API que recibieron correctamente códigos de respuesta HTTP, como 304, 307 y los menores de 301 (por ejemplo, 200). 
* Solicitudes de puerta de enlace con error: el número de solicitudes API que recibieron códigos de respuesta HTTP erróneos, como 400 y cualquiera mayor que 500.
* Solicitudes de puerta de enlace no autorizadas: el número de solicitudes API que recibieron códigos de respuesta HTTP, incluidos 401, 403 y 429. 
* Otras solicitudes de puerta de enlace: el número de solicitudes API que recibieron códigos de respuesta HTTP que no pertenecen a ninguna de las categorías anteriores (por ejemplo, 418).

Puede acceder a métricas en el servicio API Management o a las métricas de todos los recursos de Azure en Azure Monitor. Para ver métricas en el servicio API Management:
1. Abra Azure Portal.
2. Vaya al servicio API Management.
3. Haga clic en **Métricas**.

![Hoja Métricas][metrics-blade]

Para más información sobre cómo usar las métricas, consulte [Información general sobre las métricas].

## <a name="activity-logs"></a>Registros de actividad
Los registros de actividad proporcionan información sobre las operaciones llevadas a cabo en los servicios API Management. Antes se los conocía como "registros de auditoría" o "registros operativos". Con los registros de actividades, puede determinar los interrogantes “qué, quién y cuándo” de las operaciones de escritura (PUT, POST, DELETE) llevadas a cabo en los servicios API Management. 

> [!NOTE]
> Los registros de actividad no incluyen las operaciones de lectura (GET) ni las realizadas en el portal para editores clásico o mediante las API de administración originales.

Puede acceder a registros de actividad en el servicio API Management o a los registros de todos los recursos de Azure en Azure Monitor. Para ver registros de actividad en el servicio API Management:
1. Abra Azure Portal.
2. Vaya al servicio API Management.
3. Haga clic en **Registro de actividad**.

![Hoja Registro de actividad][activity-logs-blade]

Para más información sobre cómo usar los registros de actividad, consulte [Información general sobre el registro de actividad].

## <a name="alerts"></a>Alertas
Puede configurar la recepción de alertas en función de métricas y registros de actividad. Azure Monitor permite configurar una alerta que haga lo siguiente cuando se desencadena:

* Enviar una notificación por correo electrónico
* Llamar a un webhook
* Invocar una aplicación lógica de Azure

Puede configurar reglas de alerta en el servicio API Management o en Azure Monitor. Para configurarlas en API Management: 
1. Abra Azure Portal.
2. Vaya al servicio API Management.
3. Haga clic en **Reglas de alerta**.

![Hoja Reglas de alertas][alert-rules-blade]

Para más información sobre el uso de alertas, consulte la [introducción a las alertas].

## <a name="diagnostic-logs"></a>Registros de diagnóstico
Los registros de diagnóstico proporcionan información valiosa acerca de las operaciones y los errores que son importantes para la auditoría, así como para solucionar problemas. Los registros de diagnóstico son diferentes de los registros de actividad. Los registros de actividad proporcionan información sobre las operaciones llevadas a cabo en los recursos de Azure. Los registros de diagnóstico proporcionan información detallada sobre las operaciones que el mismo recurso realiza.

Actualmente, API Management proporciona registros de diagnóstico (cada hora por lotes) sobre solicitudes API individuales con cada entrada que tenga la estructura siguiente:

```
{
    "Tenant": "",
      "DeploymentName": "",
      "time": "",
      "resourceId": "",
      "category": "GatewayLogs",
      "operationName": "Microsoft.ApiManagement/GatewayLogs",
      "durationMs": ,
      "Level": ,
      "properties": "{
          "ApiId": "",
          "OperationId": "",
          "ProductId": "",
          "SubscriptionId": "",
          "Method": "",
          "Url": "",
          "RequestSize": ,
          "ServiceTime": "",
          "BackendMethod": "",
          "BackendUrl": "",
          "BackendResponseCode": ,
          "ResponseCode": ,
          "ResponseSize": ,
          "Cache": "",
          "UserId"
      }"
 }
```

Puede acceder a registros de diagnóstico en el servicio API Management o a los registros de todos los recursos de Azure en Azure Monitor. Para ver registros de diagnóstico en el servicio API Management:
1. Abra Azure Portal.
2. Vaya al servicio API Management.
3. Haga clic en **Registro de diagnóstico**.

![Hoja Registros de diagnóstico][diagnostic-logs-blade]

Para más información sobre cómo usar los registros de diagnóstico, consulte la [introducción a los registros de diagnóstico].

## <a name="next-step"></a>Paso siguiente

* [Introducción a Azure Monitor]
* [Información general sobre las métricas]
* [Información general sobre el registro de actividad]
* [introducción a los registros de diagnóstico]
* [introducción a las alertas]

[Introducción a Azure Monitor]: ../monitoring-and-diagnostics/monitoring-get-started.md
[Información general sobre las métricas]: ../monitoring-and-diagnostics/monitoring-overview-metrics.md
[Información general sobre el registro de actividad]: ../monitoring-and-diagnostics/monitoring-overview-activity-logs.md
[introducción a los registros de diagnóstico]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[introducción a las alertas]: ../monitoring-and-diagnostics/insights-alerts-portal.md



[metrics-blade]: ./media/api-management-azure-monitor/api-management-metrics-blade.png
[activity-logs-blade]: ./media/api-management-azure-monitor/api-management-activity-logs-blade.png
[alert-rules-blade]: ./media/api-management-azure-monitor/api-management-alert-rules-blade.png
[diagnostic-logs-blade]: ./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png
