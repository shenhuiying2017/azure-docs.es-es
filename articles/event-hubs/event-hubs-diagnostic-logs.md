---
title: "Registros de diagnóstico de Azure Event Hubs | Microsoft Docs"
description: "Aprenda a configurar registros de diagnósticos para centros de eventos en Azure."
keywords: 
documentationcenter: 
services: event-hubs
author: banisadr
manager: 
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: bcc8427d57a001f73d321fbf35c5226a047b68d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-diagnostic-logs"></a>Registros de diagnósticos de Event Hubs

Puede ver dos tipos de registros para Event Hubs de Azure:
* **[Registros de actividad](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)**. Estos registros contienen información sobre las operaciones realizadas en un trabajo. Los registros están siempre habilitados.
* **[Registros de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)**. Puede configurar registros de diagnóstico para obtener una vista más completa de todo lo que ocurre con un trabajo. Los registros de diagnóstico incluyen la actividad desde el momento en que se crea el trabajo hasta que este se elimina, incluidas las actualizaciones y actividades que se realizan durante la ejecución del trabajo.

## <a name="turn-on-diagnostic-logs"></a>Activación de los registros de diagnóstico

Los registros de diagnóstico están inhabilitados de forma predeterminada. Para habilitar registros de diagnóstico:

1.  En [Azure Portal](https://portal.azure.com), en **Supervisión y administración**, haga clic en **Registros de diagnóstico**.

    ![Navegación por la hoja de Registros de diagnósticos](./media/event-hubs-diagnostic-logs/image1.png)

2.  Haga clic en el recurso que quiere supervisar.

3.  Haga clic en **Activar diagnóstico**.

    ![Activación de los registros de diagnósticos](./media/event-hubs-diagnostic-logs/image2.png)

4.  En **Estado**, haga clic en **Activado**.

    ![Cambio del estado de registros de diagnósticos](./media/event-hubs-diagnostic-logs/image3.png)

5.  Establezca el destino de archivo que quiera; por ejemplo, una cuenta de almacenamiento, un centro de eventos o Azure Log Analytics.

6.  Guarde la nueva configuración de diagnóstico.

La nueva configuración surte efecto en unos 10 minutos. Después, los registros aparecen en el destino de archivo configurado, en la hoja **Registros de diagnóstico**.

Para obtener más información sobre el diagnóstico de configuraciones, consulte la [información general sobre los registros de diagnóstico de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

## <a name="diagnostic-logs-categories"></a>Categorías de registros de diagnósticos
Event Hubs captura registros de diagnósticos de dos categorías:

* **ArchiveLogs**: registros relacionados con archivos de Event Hubs, en concreto, registros relacionados con errores de archivo.
* **OperationalLogs**: información sobre lo que sucede durante el funcionamiento de Event Hubs, en concreto, el tipo de operación, como la creación de centros de eventos, los recursos usados y el estado de la operación.

## <a name="diagnostic-logs-schema"></a>Esquema de registros de diagnósticos
Todos los registros se almacenan en el formato de notación de objetos JavaScript (JSON). Cada entrada tiene campos de cadena que usan el formato descrito en las secciones siguientes.

### <a name="archive-logs-schema"></a>Esquema de registros de archivo

Las cadenas JSON de registros de archivo incluyen elementos enumerados en la tabla siguiente:

Nombre | Descripción
------- | -------
TaskName | La descripción de la tarea que generó el error.
ActivityId | El identificador interno, usado con fines de seguimiento.
trackingId | El identificador interno, usado con fines de seguimiento.
resourceId | Identificador de recursos de Azure Resource Manager.
eventHub | Nombre completo del centro de eventos (incluye el nombre del espacio de nombres).
partitionId | La partición del centro de eventos en la que se escribe.
archiveStep | ArchiveFlushWriter
startTime | Hora de inicio del error.
errores | Número de veces que se ha producido el error.
durationInSeconds | La duración del error.
Mensaje | Mensaje de error.
categoría | ArchiveLogs

El código siguiente es un ejemplo de una cadena JSON de registro de archivo:

```json
{
     "TaskName": "EventHubArchiveUserError",
     "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
     "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
     "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
     "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
     "partitionId": "1",
     "archiveStep": "ArchiveFlushWriter",
     "startTime": "9/22/2016 5:11:21 AM",
     "failures": 3,
     "durationInSeconds": 360,
     "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
     "category": "ArchiveLogs"
}
```

### <a name="operational-logs-schema"></a>Esquema de registros operativos

Las cadenas JSON de registros operativos incluyen elementos enumerados en la tabla siguiente:

Nombre | Descripción
------- | -------
ActivityId | Identificador interno, usado con fines de seguimiento.
EventName | Nombre de la operación.  
resourceId | Identificador de recursos de Azure Resource Manager.
SubscriptionId | Id. de suscripción.
EventTimeString | Hora de la operación.
EventProperties | Propiedades de la operación.
Estado | Estado de la operación.
Autor de llamada | Autor de la llamada de la operación (Azure Portal o Management Client).
categoría | OperationalLogs

El código siguiente es un ejemplo de una cadena JSON de registro operativo:

```json
Example:
{
     "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
     "EventName": "Create EventHub",
     "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
     "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
     "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
     "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
     "Status": "Succeeded",
     "Caller": "ServiceBus Client",
     "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Event Hubs](event-hubs-what-is-event-hubs.md)
* [Introducción a la API de centros de eventos](event-hubs-api-overview.md)
* [Introducción a Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
