---
title: Registro de eventos en Azure Event Hubs en API Management de Azure
description: Aprenda a registrar eventos en Azure Event Hubs en Azure API Management.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 77c3e41dd4b1fdf7e518de67b353f69fcb758c60
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Cómo registrar eventos en Azure Event Hubs en Azure API Management
Azure Event Hubs es un servicio de introducción de datos altamente escalable que permite la introducción de millones de eventos por segundo para que pueda procesar y analizar grandes cantidades de datos generados por los dispositivos y aplicaciones conectados. Event Hubs actúa como la "puerta principal" de una canalización de eventos y, una vez que los datos se recopilan en un centro de eventos, se pueden transformar y almacenar con cualquier proveedor de análisis en tiempo real o adaptadores de procesamiento por lotes/almacenamiento. Event Hubs desacopla la producción de un flujo de eventos desde el consumo de los eventos, para que los consumidores de eventos pueden tener acceso a los eventos según su propia programación.

Este artículo es un complemento del vídeo [Integración de Azure API Management con Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) y describe cómo registrar eventos de API Management mediante Azure Event Hubs.

## <a name="create-an-azure-event-hub"></a>Crear un centro de eventos de Azure

Para información detallada sobre cómo crear un centro de eventos y obtener las cadenas de conexión que necesita para enviar y recibir eventos desde y hacia el centro de eventos, consulte [Creación de un espacio de nombres de Event Hubs y un centro de eventos con Azure Portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Creación de un registrador de administración de API
Ahora que tiene un centro de eventos, el siguiente paso es configurar un [registrador](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) en el servicio Administración de API para que se puedan registrar eventos en el centro de eventos.

Los registradores de API Management se configuran mediante la [API de REST de API Management](http://aka.ms/smapi). Antes de usar la API de REST por primera vez, revise los [requisitos previos](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#Prerequisites) y asegúrese de tener [habilitado el acceso a la API de REST](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Para crear un registrador, efectúe una solicitud HTTP PUT con la siguiente plantilla de dirección URL:

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview`

* Sustituya `{your service}` por el nombre de la instancia de servicio de administración de API.
* Sustituya `{new logger name}` por el nombre deseado del nuevo registrador. A este nombre se hace referencia al configurar la directiva [log-to-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub).

Agregue los siguientes encabezados a la solicitud:

* Content-Type : application/json
* Authorization : SharedAccessSignature 58...
  * Para instrucciones sobre cómo generar `SharedAccessSignature` , vea [Autenticación de la API de REST de administración de API de Azure](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Especifique el cuerpo de la solicitud usando la siguiente plantilla:

```json
{
  "loggertype" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the Azure Classic Portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `loggertype` se debe establecer en `AzureEventHub`.
* `description` ofrece una descripción opcional del registrador y puede ser una cadena de longitud cero si lo desea.
* `credentials` contiene el `name` y `connectionString` del centro de eventos de Azure.

Al realizar la solicitud, si se crea el registrador, se devuelve un código de estado de `201 Created` .

> [!NOTE]
> Para conocer otros posibles códigos de retorno y sus razones, vea [Creación de un registrador](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Para conocer la forma de realizar otras operaciones como crear listas, actualizar y eliminar, consulte la documentación de la entidad del [registrador](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity).
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Configuración de directivas log-to-eventhubs

Una vez que el registrador está configurado en la administración de API, puede configurar las directivas de log-to-eventhubs para registrar los eventos oportunos. La directiva log-to-eventhubs puede utilizarse en la sección de las directivas de entrada o de salida.

1. Vaya a la instancia de APIM.
2. Seleccione la pestaña API.
3. Seleccione la API a la que quiere agregar la directiva. En este ejemplo, vamos a agregar una directiva a la **API eco** en el producto **Unlimited** (Sin límites).
4. Seleccione **Todas las operaciones**.
5. En la parte superior de la pantalla, seleccione la pestaña Design (Diseño).
6. En la ventana de procesamiento de entrada o salida, haga clic en el triángulo (al lado del lápiz).
7. Seleccione el Editor de código. Para más información, consulte [Establecimiento o modificación de directivas](set-edit-policies.md).
8. Coloque el cursor en la sección de directiva `inbound` o `outbound`.
9. En la ventana de la derecha, seleccione **Advanced policies** >  (Directivas avanzadas) **Log to EventHub** (Registro en EventHub). Esta acción inserta la plantilla de declaración de directiva `log-to-eventhub`.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Sustituya `logger-id` por el nombre del registrador de administración de API que configuró en el paso anterior.

Puede usar cualquier expresión que devuelva una cadena como valor para el elemento `log-to-eventhub`. En este ejemplo, se registra una cadena que contiene la fecha y la hora, el nombre del servicio, el identificador de la solicitud, la dirección IP de la solicitud y el nombre de la operación.

Haga clic en **Guardar** para guardar la configuración de la directiva actualizada. En el momento de guardarla, la directiva se activa y los eventos se registran en el centro de eventos designado.

## <a name="next-steps"></a>pasos siguientes
* Obtenga más información acerca de Azure Event Hubs
  * [Introducción a Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Recepción de mensajes con EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Guía de programación de Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Obtener más información acerca de la integración de API Management y Event Hubs
  * [Referencia de entidad del registrador](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [referencia de la directiva log-to-eventhub](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Supervisión de las API con Azure API Management, Event Hubs y Runscope](api-management-log-to-eventhub-sample.md)    

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
