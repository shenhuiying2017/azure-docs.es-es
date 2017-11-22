---
title: "Configuración de las notificaciones de estado para sistemas de administración de problemas existentes mediante un webhook | Microsoft Docs"
description: "Obtenga notificaciones personalizadas sobre los eventos del estado de servicio en el sistema de administración de problemas existente."
author: shawntabrizi
manager: scotthit
editor: 
services: service-health
documentationcenter: service-health
ms.assetid: 
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: b6a5f61f61675b825dcfe9c706c80944f5890538
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="configure-health-notifications-for-existing-problem-management-systems-using-a-webhook"></a>Configuración de las notificaciones de estado para los sistemas de administración de problemas existentes mediante un webhook

En este artículo se muestra cómo configurar las alertas de estado del servicio para enviar datos a través de webhooks al sistema de notificación existente.

Hoy en día, puede configurar las alertas de estado del servicio para que cuando un incidente de servicio de Azure le afecte, reciba notificaciones mediante mensajes de texto o correos electrónicos.
Sin embargo, puede que ya tenga un sistema notificación externo implementado que le gustaría utilizar.
Este documento muestra las partes más importantes de la carga de webhook y cómo puede crear alertas personalizadas para obtener notificaciones cuando los problemas de servicio le afecten.

Si desea usar una integración preconfigurada, vea los siguientes procedimientos:
* [Configure alerts with ServiceNow](service-health-alert-webhook-servicenow.md) (Configuración de alertas con ServiceNow)
* [Configure alerts with PagerDuty](service-health-alert-webhook-pagerduty.md) (Configuración de alertas con PagerDuty)
* [Configure alerts with OpsGenie](service-health-alert-webhook-opsgenie.md) (Configuración de alertas con OpsGenie)

## <a name="configuring-a-custom-notification-using-the-service-health-webhook-payload"></a>Configuración de una notificación personalizada con la carga de webhook de estado del servicio
Si desea configurar su propia integración de webhook personalizada, tiene que analizar la carga JSON enviada durante las notificaciones de Estado del servicio.

Consulte [aquí para ver un ejemplo](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md) del aspecto de la carga de webhook `Service Health`.

Puede detectar si trata de una alerta de Estado del servicio examinando `context.eventSource == "ServiceHealth"`. A partir de ahí, las propiedades que son más importantes en cuanto a la introducción son:
 * `data.context.activityLog.status`
 * `data.context.activityLog.level`
 * `data.context.activityLog.subscriptionId`
 * `data.context.activityLog.properties.title`
 * `data.context.activityLog.properties.impactStartTime`
 * `data.context.activityLog.properties.communication`
 * `data.context.activityLog.properties.impactedServices`
 * `data.context.activityLog.properties.trackingId`

## <a name="creating-a-direct-link-to-azure-service-health-for-an-incident"></a>Creación de un vínculo directo a Estado del servicio de Azure para un incidente
Puede crear un vínculo directo al incidente personalizado de Estado del servicio de Azure en el escritorio o en el móvil generando una dirección URL especializada. Use `trackingId`, así como los tres primeros y últimos dígitos de `subscriptionId` para formar:
```
https://app.azure.com/h/<trackingId>/<first and last three digits of subscriptionId>
```

Por ejemplo, si `subscriptionId` es `bba14129-e895-429b-8809-278e836ecdb3` y `trackingId` es `0DET-URB`, la dirección URL personalizada de Estado del servicio de Azure es:

```
https://app.azure.com/h/0DET-URB/bbadb3
```

## <a name="using-the-level-to-detect-the-severity-of-the-issue"></a>Uso del nivel para detectar la gravedad del problema
Desde la gravedad más leve a la más grave, la propiedad `level` en la carga puede ser `Informational`, `Warning`, `Error` y `Critical`.

## <a name="parsing-the-impacted-services-to-understand-the-full-scope-of-the-incident"></a>Análisis de los servicios afectados para entender el ámbito completo del incidente
Las alertas de estado del servicio pueden informar acerca de los problemas en varias regiones y servicios. Para obtener detalles completos, debe analizar el valor de `impactedServices`.
El contenido interior es una cadena con [caracteres de escape JSON](http://json.org/), cuando no tiene caracteres de escape, contiene otro objeto JSON que puede analizarse regularmente.

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

Se convierte en:

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

Esto indica que hay problemas con "Alerts & Metrics" en el Este de Australia y Sudeste de Australia, así como problemas con "App Service" en el Sudeste de Australia.


## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Prueba de la integración de webhook a través de una solicitud HTTP POST
1. Cree la carga de estado del servicio que desee enviar. Puede encontrar una carga de webhook de estado del servicio de ejemplo en [Webhooks para alertas del registro de actividad de Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Cree una solicitud HTTP POST de la siguiente manera:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <Service Health payload>
    ```
3. Debe recibir una respuesta `2XX - Successful`.

4. Vaya a [PagerDuty](https://www.pagerduty.com/) para confirmar que la integración se ha configurado correctamente.

## <a name="next-steps"></a>Pasos siguientes
- Revise el [Esquema de webhook de alertas del registro de actividad](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Más información acerca de las [Notificaciones del estado del servicio](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Más información sobre los [grupos de acciones](../monitoring-and-diagnostics/monitoring-action-groups.md).