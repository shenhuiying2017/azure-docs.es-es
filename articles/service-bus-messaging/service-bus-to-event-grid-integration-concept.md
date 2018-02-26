---
title: "Introducción a la integración de Azure Service Bus en Event Grid | Microsoft Docs"
description: "Descripción de la integración de la mensajería de Service Bus y Event Grid"
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: 17f6c107ea1adfd4463ff4cc205fe11d111acb84
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="azure-service-bus-to-azure-event-grid-integration-overview"></a>Introducción a la integración de Azure Service Bus en Azure Event Grid

Azure Service Bus inició una nueva integración en Azure Event Grid. El principal escenario que permite esta característica es que aquellas colas o suscripciones de Service Bus que tengan un bajo volumen de mensajes, no estén obligadas a tener un sondeo de receptor de mensajes en todo momento. Service Bus puede ya emitir eventos a Azure Event Grid cuando haya mensajes en una cola o suscripción y no haya ningún receptor presente. Puede crear suscripciones de Azure Event Grid para los espacios de nombres de Service Bus y escuchar estos eventos y reaccionar a ellos iniciando un receptor. Con esta característica, Service Bus se puede usar en modelos de programación reactiva.

Para habilitar la característica, necesita lo siguiente:

* Un espacio de nombres Premium de Azure Service Bus con al menos una cola de Service Bus o un tema de Service Bus que tenga como mínimo una suscripción.
* Acceso de colaborador al espacio de nombres de Azure Service Bus.
* Además, se necesita una suscripción a Azure Event Grid para el espacio de nombres de Service Bus. Esta suscripción recibe la notificación de Azure Event Grid que indica que hay mensajes que se deben recopilar. Los suscriptores típicos pueden ser Logic Apps, Azure Functions, o un webhook que se ponga en contacto con una aplicación web que, posteriormente, procesa los mensajes. 

![19][]

### <a name="verify-that-you-have-contributor-access"></a>Comprobación de que dispone de acceso de colaborador

Vaya al espacio de nombres de Service Bus y seleccione "Control de acceso (IAM)", tal y como se muestra a continuación:

![1][]

### <a name="events-and-event-schemas"></a>Eventos y esquemas de evento

Azure Service Bus envía actualmente eventos en dos escenarios.

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* [DeadletterMessagesAvailable](#dead-lettered-messages-available-event)

Además, usa la seguridad estándar de Azure Event Grid y [mecanismos de autenticación](https://docs.microsoft.com/en-us/azure/event-grid/security-authentication).

Para más información acerca de los esquemas de eventos de Event Grid, siga [este](https://docs.microsoft.com/en-us/azure/event-grid/event-schema) vínculo.

#### <a name="active-messages-available-event"></a>Evento de mensajes activos disponibles

Este evento se genera si hay mensajes activos en una cola o suscripción y no hay receptores a la escucha.

El esquema de este evento es el siguiente:

```JSON
{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}
```

#### <a name="dead-lettered-messages-available-event"></a>Evento de mensajes fallidos disponibles

Obtiene un evento como mínimo por cada cola de mensajes fallidos que contenga mensajes y ningún receptor activo.

El esquema de este evento es el siguiente:

```JSON
[{
     "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="how-often-and-how-many-events-are-emitted"></a>¿Cuál es la frecuencia y la cantidad de eventos que se emiten?

Si tiene varias colas y temas o suscripciones en el espacio de nombres, obtendrá al menos un evento por cola y uno por suscripción. Los eventos se emiten inmediatamente si no hay ningún mensaje en la entidad de Service Bus y llega un mensaje nuevo cada dos minutos salvo que Azure Service Bus detecte un receptor activo. El examen de los mensajes no interrumpe los eventos.

De forma predeterminada, Azure Service Bus emite eventos para todas las entidades del espacio de nombres. Si desea obtener eventos solo para entidades concretas, consulte la siguiente sección de filtrado.

### <a name="filtering-limiting-from-where-you-get-events"></a>Filtrado y limitación de dónde se obtienen los eventos

Por ejemplo, si desea obtener eventos solo para una cola o suscripción del espacio de nombres, puede utilizar los filtros "Comienza por" o "Termina con" que proporciona Azure Event Grid. En algunas interfaces, los filtros se denominan "Prefijo" y "Sufijo". Si desea obtener eventos para varias colas y suscripciones (pero no para todas), puede crear varias suscripciones diferentes de Azure Event Grid y proporcionar un filtro para cada una.

## <a name="how-to-create-azure-event-grid-subscriptions-for-service-bus-namespaces"></a>Creación de suscripciones de Azure Event Grid para espacios de nombres de Service Bus

Hay tres formas distintas de crear suscripciones de Azure Event Grid para los espacios de nombres de Service Bus.

* [Portal de Azure](#portal-instructions)
* [CLI de Azure](#azure-cli-instructions)
* [PowerShell](#powershell-instructions)

## <a name="portal-instructions"></a>Instrucciones de Azure Portal

Para crear una nueva suscripción a Azure Event Grid, vaya al espacio de nombres en Azure Portal y seleccione la hoja Event Grid. Haga clic en "+ Suscripción de eventos". La siguiente captura de pantalla muestra un espacio de nombres que ya tiene varias suscripciones de Event Grid.

![20 ][]

La siguiente captura de pantalla muestra un ejemplo cómo suscribirse a una instancia de Azure Functions o a un webhook sin ningún filtro concreto:

![21][]

## <a name="azure-cli-instructions"></a>Instrucciones para la CLI de Azure

En primer lugar, asegúrese de que tiene instalada la versión 2.0 de la CLI de Azure como mínimo. Puede descargar el instalador desde aquí. A continuación, pulse "Windows + X" y abra una nueva consola de PowerShell con permisos de administrador. Alternativamente, también puede usar un shell de comandos en Azure Portal.

Ejecute el código siguiente:

```PowerShell
Az login

Aa account set -s “THE SUBSCRIPTION YOU WANT TO USE”

$namespaceid=(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name “<yourNamespace>“--resource-group “<Your Resource Group Name>” --query id --output tsv)

az eventgrid event-subscription create --resource-id $namespaceid --name “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” --endpoint “<your_function_url>” --subject-ends-with “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

## <a name="powershell-instructions"></a>Instrucciones para PowerShell

Asegúrese de que tiene instalado Azure PowerShell. Puede encontrarlo aquí. A continuación, pulse "Windows + X" y abra una nueva consola de PowerShell con permisos de administrador. Alternativamente, también puede usar un shell de comandos en Azure Portal.

```PowerShell
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module AzureRM.ServiceBus

$NSID = (Get-AzureRmServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id 

New-AzureRmEVentGridSubscription -EventSubscriptionName “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

Desde aquí, puede explorar las demás opciones de configuración o [comprobar que los eventos fluyen](#test-that-events-are-flowing).

## <a name="next-steps"></a>pasos siguientes

* [Ejemplos](service-bus-to-event-grid-integration-example.md) de Service Bus y Event Grid.
* Más información sobre [Azure Event Grid](https://docs.microsoft.com/en-us/azure/azure-functions/).
* Más información acerca de [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/).
* Obtenga más información sobre [Azure Logic Apps](https://docs.microsoft.com/en-us/azure/logic-apps/).
* Aprenda más sobre [Azure Service Bus](https://docs.microsoft.com/en-us/azure/azure-functions/).

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png