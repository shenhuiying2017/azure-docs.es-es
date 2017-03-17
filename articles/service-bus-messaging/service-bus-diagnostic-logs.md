---
title: "Registros de diagnóstico de Azure Service Bus | Microsoft Docs"
description: "Aprenda a analizar registros de diagnóstico desde Service Bus en Microsoft Azure."
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
ms.sourcegitcommit: 90321171586110a3b60c3df5b749003ebbf70ec9
ms.openlocfilehash: 70205b33e9d52e41f5c1a637fee4da192e2a971a
ms.lasthandoff: 02/22/2017


---
# <a name="service-bus-diagnostic-logs"></a>Registros de diagnóstico de Service Bus

## <a name="introduction"></a>Introducción
Service Bus expone dos tipos de registros: 
* [Registros de actividad](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), que están siempre habilitados y proporcionan información sobre operaciones realizadas en trabajos;
* [Registros de diagnósticos](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), que los puede configurar el usuario y proporcionan una visión más completa de todo lo que ocurre con el trabajo, desde el momento en el que se crea, actualiza, mientras se ejecuta y hasta que se elimina;

## <a name="how-to-enable-diagnostic-logs"></a>Habilitación de registros de diagnósticos
Los registros de diagnósticos están **desactivados** de forma predeterminada. Para habilitarlos, siga estos pasos:

Inicie sesión en Azure Portal, navegue a la hoja de trabajos de streaming y use la hoja "Registros de diagnósticos" bajo "Supervisión".

![navegación por la hoja a los registros de diagnósticos](./media/service-bus-diagnostic-logs/image1.png)  

A continuación, haga clic en el vínculo “Activar diagnósticos”.

![activar los registros de diagnósticos](./media/service-bus-diagnostic-logs/image2.png)

En los diagnósticos abiertos, cambie el estado a "Activado".

![cambiar el estado de los registros de diagnósticos](./media/service-bus-diagnostic-logs/image3.png)

Configure el destino de archivo deseado (cuenta de almacenamiento, centro de eventos, Log Analytics) y seleccione las categorías de registros que desea recopilar (Ejecución o Creación). A continuación, guarde la nueva configuración de diagnósticos.

Una vez guardada, la configuración tardará unos 10 minutos en aplicarse y, después, los registros comenzarán a aparecer en el destino de archivo configurado que puede ver en la hoja “Registros de diagnóstico”:

En la página de [registros de diagnósticos](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) hay disponible más información sobre la configuración de diagnósticos.

## <a name="diagnostic-logs-schema"></a>Esquema de registros de diagnósticos

Todos los registros se almacenan en formato JSON y cada entrada tiene campos de cadena con el siguiente formato.

### <a name="operation-logs"></a>Registros de operaciones

OperationalLogs captura lo que sucede durante el funcionamiento de Service Bus y, en concreto, el tipo de operación, como la creación de colas; los recursos usados y el estado de la operación.

Nombre | Descripción
------- | -------
ActivityId | Identificador interno con fines de seguimiento
EventName | Nombre de la operación             
resourceId | Identificador de recurso de ARM
SubscriptionId | Id. de suscripción
EventTimeString | Hora de la operación
EventProperties | Propiedades de la operación
Estado | Estado de la operación
Autor de llamada | Autor de la llamada de la operación (Portal o Management Client)
categoría | OperationalLogs

#### <a name="example-operation-log"></a>Registro de operaciones de ejemplo

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

