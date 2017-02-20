---
title: "Registros de diagnóstico de Azure Event Hubs | Microsoft Docs"
description: "Aprenda a analizar registros de diagnóstico desde Event Hubs en Microsoft Azure."
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
ms.date: 02/01/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: 4d7d0e1f5ffb395bf91bbdac1ab4b9ec3f9dee1c
ms.openlocfilehash: cb8c7930ee423543c91366de64220ee55609a4cf


---
# <a name="event-hub-diagnostic-logs"></a>Registros de diagnóstico de Event Hubs

## <a name="introduction"></a>Introducción
Event Hubs expone dos tipos de registros: 
* [Registros de actividad](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), que están siempre habilitados y proporcionan información sobre operaciones realizadas en trabajos;
* [Registros de diagnósticos](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), que los puede configurar el usuario y proporcionan una visión más completa de todo lo que ocurre con el trabajo, desde el momento en el que se crea, actualiza, mientras se ejecuta y hasta que se elimina;

## <a name="how-to-enable-diagnostic-logs"></a>Habilitación de registros de diagnósticos
Los registros de diagnósticos están **desactivados** de forma predeterminada. Para habilitarlos, siga estos pasos:

Inicie sesión en Azure Portal, navegue a la hoja de trabajos de streaming y use la hoja "Registros de diagnósticos" bajo "Supervisión".

![navegación por la hoja a los registros de diagnósticos](./media/event-hubs-diagnostic-logs/image1.png)  

A continuación, haga clic en el vínculo “Activar diagnósticos”.

![activar los registros de diagnósticos](./media/event-hubs-diagnostic-logs/image2.png)

En los diagnósticos abiertos, cambie el estado a "Activado".

![cambiar el estado de los registros de diagnósticos](./media/event-hubs-diagnostic-logs/image3.png)

Configure el destino de archivo deseado (cuenta de almacenamiento, centro de eventos, Log Analytics) y seleccione las categorías de registros que desea recopilar (Ejecución o Creación). A continuación, guarde la nueva configuración de diagnósticos.

Una vez guardada, la configuración tardará unos 10 minutos en aplicarse y, después, los registros comenzarán a aparecer en el destino de archivo configurado que puede ver en la hoja “Registros de diagnóstico”:

En la página de [registros de diagnósticos](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) hay disponible más información sobre la configuración de diagnósticos.

## <a name="diagnostic-logs-categories"></a>Categorías de registros de diagnósticos
Existen dos categorías de registros de diagnósticos que se capturan actualmente:

* **ArchivalLogs:** captura los registros relacionados con Event Hubs Archive, específicamente relacionados con errores de Archive.
* **OperationalLogs:** captura lo que sucede durante el funcionamiento de Event Hubs y, en concreto, tipos de operación como la creación de un centro de eventos, los recursos usados y el estado de la operación.

## <a name="diagnostic-logs-schema"></a>Esquema de registros de diagnósticos
Todos los registros se almacenan en formato JSON y cada entrada tiene campos de cadena con el siguiente formato:


### <a name="archive-error-schema"></a>Esquema de errores de Archive
Nombre | Descripción
------- | -------
TaskName | La descripción de la tarea que produjo error
ActivityId | Identificador interno con fines de seguimiento
trackingId | Identificador interno con fines de seguimiento
resourceId | Identificador de recurso de ARM
eventHub | Nombre completo del centro de eventos (incluye el nombre del espacio de nombres)
partitionId | La partición en la que se está escribiendo dentro del centro de eventos
archiveStep | ArchiveFlushWriter
startTime | Hora de inicio del error
errores | Número de veces que se ha producido el error
durationInSeconds | La duración del error
Mensaje | Mensaje de error
categoría | ArchiveLogs

#### <a name="example-archive-log"></a>Registro de Archive de ejemplo

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

### <a name="operation-logs-schema"></a>Esquema de registros de operaciones
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
* [Introducción a la API de Event Hubs](event-hubs-api-overview.md)
* [Introducción a los Centros de eventos](event-hubs-csharp-ephcs-getstarted.md)


<!--HONumber=Feb17_HO1-->


