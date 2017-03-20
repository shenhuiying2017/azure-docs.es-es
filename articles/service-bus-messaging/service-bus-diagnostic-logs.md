---
title: "Registros de diagnóstico de Azure Service Bus | Microsoft Docs"
description: "Aprenda cómo configurar registros de diagnóstico para Service Bus en Azure."
keywords: 
documentationcenter: 
services: service-bus-messaging
author: banisadr
manager: 
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/17/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: abcb0eee979853948cf6d981ff8f3a457eeeeef0
ms.openlocfilehash: 65fe81dc90f2dc7a251860adfdd8374912cb8d73
ms.lasthandoff: 03/01/2017


---
# <a name="service-bus-diagnostic-logs"></a>Registros de diagnóstico de Service Bus

Puede ver dos tipos de registros para Azure Service Bus:
* **[Registros de actividad](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)**. Estos registros contienen información sobre las operaciones realizadas en un trabajo. Los registros están siempre activados.
* **[Registros de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)**. Puede configurar registros de diagnóstico para obtener información más completa sobre todo lo que ocurre con un trabajo. Los registros de diagnóstico incluyen la actividad desde el momento en que se crea el trabajo hasta que este se elimina, incluidas las actualizaciones y actividades que se realizan durante la ejecución del trabajo.

## <a name="turn-on-diagnostic-logs"></a>Activación de los registros de diagnóstico
Los registros de diagnóstico están **desactivados** de forma predeterminada. Para activar los registros de diagnóstico:

1.    En Azure Portal, vaya a la hoja de trabajo de streaming.

2.    En **Supervisión**, vaya a la hoja **Registros de diagnóstico**.

    ![navegación por la hoja a los registros de diagnósticos](./media/service-bus-diagnostic-logs/image1.png)  

3.    Seleccione **Activar diagnósticos**.

    ![activar los registros de diagnósticos](./media/service-bus-diagnostic-logs/image2.png)

4.    En **Estado**, seleccione **Activo**.

    ![cambiar el estado de los registros de diagnósticos](./media/service-bus-diagnostic-logs/image3.png)

5.    Establezca el destino de archivo que quiera, por ejemplo, una cuenta de almacenamiento, un centro de eventos o Azure Log Analytics.

6.    Seleccione las categorías de registros que quiere recopilar, por ejemplo, **Ejecución** o **Creación**.

7.    Guarde la nueva configuración de diagnóstico.

La nueva configuración surte efecto en unos 10 minutos. Después, los registros aparecen en el destino de archivo configurado, en la hoja **Registros de diagnóstico**.

Para más información sobre el diagnóstico de configuraciones, vea la [información general sobre los registros de diagnóstico de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

## <a name="diagnostic-logs-schema"></a>Esquema de registros de diagnósticos

Todos los registros se almacenan en el formato de notación de objetos JavaScript (JSON). Cada entrada tiene campos de cadena que usan el formato descrito en el ejemplo siguiente.

## <a name="operation-logs-example"></a>Ejemplo de registros de operaciones

Los registros de la categoría **OperationalLogs** capturan lo que sucede durante la operación de Service Bus. En concreto, estos registros capturan el tipo de operación, incluida la creación de colas, los recursos usados y el estado de la operación.

Las cadenas JSON de registros de operaciones incluyen elementos enumerados en la tabla siguiente:

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

Este es un ejemplo de una cadena JSON de registro de operaciones:

```json
Example:
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
* [Introducción a Service Bus](service-bus-messaging-overview.md)
* [Introducción a Service Bus](service-bus-create-namespace-portal.md)

