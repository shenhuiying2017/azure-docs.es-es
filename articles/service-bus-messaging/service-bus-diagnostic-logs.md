---
title: "Registros de diagnóstico de Azure Service Bus | Microsoft Docs"
description: "Aprenda cómo configurar registros de diagnóstico para Service Bus en Azure."
keywords: 
documentationcenter: .net
services: service-bus-messaging
author: banisadr
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/16/2017
ms.author: sethm
ms.openlocfilehash: 2bf65b7c5b0518da59e767db18fe6f4193e0ab6e
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2017
---
# <a name="service-bus-diagnostic-logs"></a>Registros de diagnóstico de Service Bus

Puede ver dos tipos de registros para Azure Service Bus:
* **[Registros de actividad](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)**. Estos registros contienen información relativa a las operaciones realizadas en un trabajo. Los registros están siempre habilitados.
* **[Registros de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)**. Puede configurar registros de diagnóstico para obtener información más completa sobre todo lo que ocurre en un trabajo. Los registros de diagnóstico incluyen la actividad desde el momento en que se crea el trabajo hasta que este se elimina, incluidas las actualizaciones y actividades que se realizan durante la ejecución del trabajo.

## <a name="turn-on-diagnostic-logs"></a>Activación de los registros de diagnóstico

Los registros de diagnóstico están inhabilitados de forma predeterminada. Para habilitarlos, siga estos pasos:

1.  En [Azure Portal](https://portal.azure.com), en **Supervisión y administración**, haga clic en **Registros de diagnóstico**.

    ![navegación por la hoja a los registros de diagnósticos](./media/service-bus-diagnostic-logs/image1.png)

2. Haga clic en el recurso que quiere supervisar.  

3.  Haga clic en **Activar diagnóstico**.

    ![activar los registros de diagnósticos](./media/service-bus-diagnostic-logs/image2.png)

4.  En **Estado**, haga clic en **Activado**.

    ![cambiar el estado de los registros de diagnósticos](./media/service-bus-diagnostic-logs/image3.png)

5.  Establezca el destino de archivo que quiera; por ejemplo, una cuenta de almacenamiento, un centro de eventos o Azure Log Analytics.

6.  Guarde la nueva configuración de diagnóstico.

La nueva configuración surte efecto en unos 10 minutos. Después, los registros aparecen en el destino de archivo configurado, en la hoja **Registros de diagnóstico**.

Para obtener más información sobre el diagnóstico de configuraciones, consulte la [información general sobre los registros de diagnóstico de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

## <a name="diagnostic-logs-schema"></a>Esquema de registros de diagnósticos

Todos los registros se almacenan en el formato de notación de objetos JavaScript (JSON). Cada entrada tiene campos de cadena que usan el formato descrito en la siguiente sección.

## <a name="operational-logs-schema"></a>Esquema de registros operativos

Los registros de la categoría **OperationalLogs** capturan lo que sucede durante las operaciones de Service Bus. En concreto, estos registros capturan el tipo de operación, incluida la creación de colas, los recursos usados y el estado de la operación.

Las cadenas JSON de registros operativos incluyen elementos enumerados en la tabla siguiente:

Nombre | Descripción
------- | -------
ActivityId | El identificador interno, usado con fines de seguimiento
EventName | Nombre de la operación           
resourceId | El identificador de recursos de Azure Resource Manager
SubscriptionId | Id. de suscripción
EventTimeString | Hora de la operación
EventProperties | Propiedades de la operación
Estado | Estado de la operación
Autor de llamada | Autor de la llamada de la operación (Azure Portal o Management Client)
categoría | OperationalLogs

Este es un ejemplo de una cadena JSON de registro operativo:

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Pasos siguientes

Visite los siguientes vínculos para más información acerca de Service Bus:

* [Introducción a Service Bus](service-bus-messaging-overview.md)
* [Introducción a Service Bus](service-bus-dotnet-get-started-with-queues.md)
