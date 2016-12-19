---
title: "Información general sobre la mensajería de Service Bus (ejemplos) | Microsoft Docs"
description: "Clasifica y describe ejemplos de mensajería de Service Bus con vínculos a cada uno."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 0b420343-2d2a-4c65-98f1-ee0e39ef55c8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 719403ca2ef90cdcf5f76fe15f7a33095e71a74c


---
# <a name="service-bus-messaging-samples"></a>Ejemplos de mensajería de Service Bus
Los ejemplos de mensajería de Service Bus muestran características importantes en [Mensajería de Service Bus](https://azure.microsoft.com/services/service-bus/) (servicio en la nube) y [Service Bus para Windows Server](https://msdn.microsoft.com/library/dn282144.aspx). En este artículo se categorizan y describen los ejemplos disponibles, con vínculos a cada uno.

> [!NOTE]
> Los ejemplos del Bus de servicio no se instalan con el SDK. Para obtener los ejemplos, visite la [página de ejemplos del SDK de Azure](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
> 
> Además, puede encontrar un conjunto actualizado de ejemplos de mensajería del Bus de servicio [aquí](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) (en el momento de escribir este documento, no se describen en este artículo).  
> 
> 

Para ver ejemplos de retransmisión, consulte [Ejemplos de retransmisión de Service Bus Relay](../service-bus-relay/service-bus-relay-samples.md).

## <a name="service-bus-messaging"></a>Mensajería de Service Bus
Los ejemplos siguientes muestran cómo escribir aplicaciones que usan Service Bus.

Tenga en cuenta que los ejemplos de mensajería requieren una cadena de conexión para acceder al espacio de nombres de Service Bus.

### <a name="to-obtain-a-connection-string-for-azure-service-bus"></a>Para obtener una cadena de conexión para el Bus de servicio de Azure
1. Inicie sesión en [Azure Portal](http://portal.azure.com).
2. En la columna izquierda, haga clic en **Bus de servicio**.
3. En la lista, haga clic en el nombre del espacio de nombres.
4. En la hoja del espacio de nombres, haga clic en **Directivas de acceso compartido**.
5. En la hoja **Directivas de acceso compartido**, haga clic en **RootManageSharedAccessKey**.
6. Copie la cadena de conexión en el Portapapeles.

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>Para obtener una cadena de conexión para el Bus de servicio para Windows Server
1. Ejecute el siguiente cmdlet de PowerShell:
   
    ```
    get-sbClientConfiguration
    ```
2. Pegue la cadena de conexión en el archivo App.config del ejemplo.

### <a name="getting-started-samples"></a>Ejemplos de introducción
Estos ejemplos describen la funcionalidad básica de mensajería.

| Nombre del ejemplo | Description | Versión mínima del SDK | Disponibilidad |
| --- | --- | --- | --- |
| [Introducción: mensajería con colas](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3) |Muestra cómo usar el Bus de servicio de Microsoft Azure para enviar y recibir mensajes de una cola. |1.8 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |
| [Introducción: mensajería con temas](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5) |Muestra cómo usar Microsoft Azure Service Bus para enviar y recibir mensajes desde un tema con varias suscripciones. |1.8 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |

### <a name="exploring-features"></a>Exploración de las características
Los ejemplos siguientes muestran diversas características del Bus de servicio.

| Nombre del ejemplo | Description | Versión mínima del SDK | Disponibilidad |
| --- | --- | --- | --- |
| [Proveedores de tokens HTTP](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5) |Muestra las distintas formas de autenticar a un cliente HTTP/REST con el Bus de servicio. |2.1 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |
| [Cliente HTTP del Bus de servicio](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a) |Muestra cómo enviar y recibir mensajes del Bus de servicio mediante HTTP/REST. |2.3 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |
| [Reenvío automático del Bus de servicio](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b) |Muestra cómo reenviar automáticamente los mensajes de una cola, suscripción o cola de correos procesados como devueltos en otra cola o tema. También muestra cómo enviar un mensaje a una cola o tema mediante una cola de transferencia. |2.3 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |
| [Mensajería asíncrona: ejemplo de sesión de canal de WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451) |Muestra cómo usar el Bus de servicio de Microsoft Azure mediante canales de Windows Communication Foundation (WCF). El ejemplo muestra el uso de canales WCF para enviar y recibir mensajes mediante una cola del Bus de servicio. El ejemplo muestra la sesión y comunicación no de la sesión en el Bus de servicio. |1.8 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |
| [Mensajería asíncrona: transacciones](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e) |Muestra cómo usar las características de mensajería del Bus de servicio de Microsoft Azure dentro de un ámbito de transacción para garantizar que las operaciones de mensajería se confirman atómicamente. |1.8 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |
| [Mensajería asíncrona: operaciones de administración con REST](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88) |Muestra cómo realizar operaciones de administración del Bus de servicio con REST. |1.8 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |
| [Las API de REST del proveedor de recursos](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203) |Muestra cómo usar las nuevas API de REST de RDFE del Bus de servicio para administrar espacios de nombres y entidades de mensajería. |1.8 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |
| [Mensajería asíncrona: ejemplo de sesión del servicio WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2) |Muestra cómo usar el Bus de servicio de Microsoft Azure con el modelo de servicio WCF. El ejemplo muestra el uso del modelo de servicio WCF para llevar a cabo la comunicación basada en sesiones mediante una cola del Bus de servicio. |1.8 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |
| [Mensajería asíncrona: solicitar respuesta](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8) |Muestra cómo usar el Bus de servicio de Microsoft Azure y la funcionalidad de solicitud/respuesta. El ejemplo muestra clientes y servidores simples que se comunican mediante una cola del Bus de servicio. |1.8 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |
| [Mensajería asíncrona: cola de mensajes fallidos](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8) |Muestra cómo usar el Bus de servicio de Microsoft Azure y la funcionalidad de mensajería "cola de mensajes fallidos". El ejemplo muestra un remitente y un receptor simples que se comunican mediante una cola del Bus de servicio. |1.8 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |
| [Mensajería asíncrona: mensajes aplazados](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879) |Muestra cómo usar la característica de aplazamiento de mensajes del Bus de servicio de Microsoft Azure. El ejemplo muestra un remitente y un receptor simples que se comunican mediante una cola del Bus de servicio. |1.8 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |
| [Mensajería asíncrona: mensajes de la sesión](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4) |Muestra cómo usar el Bus de servicio de Microsoft Azure y la funcionalidad de sesión de mensajería. El ejemplo muestra remitentes y receptores simples que se comunican mediante una cola del Bus de servicio. |1.8 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |
| [Mensajería asincrónica: solicitar tema de respuesta](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e) |Muestra cómo implementar el patrón de solicitud/respuesta con temas y suscripciones de Microsoft Azure Service Bus. El ejemplo muestra clientes y servidores simples que se comunican mediante un tema de Service Bus. |1.8 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |
| [Mensajería asíncrona: solicitar cola de respuesta](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf) |Muestra cómo usar el Bus de servicio de Microsoft Azure y la funcionalidad de solicitud/respuesta. El ejemplo muestra clientes y servidores simples que se comunican mediante dos colas del Bus de servicio. |1.8 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |
| [Mensajería asíncrona: detección de duplicados](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25) |Muestra cómo usar la detección de mensajes duplicados del Bus de servicio de Microsoft Azure con colas. Crea dos colas, una con la detección de duplicados activada y otra sin detección de duplicados. |1.8 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |
| [Mensajería asíncrona: mensajería asincrónica](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74) |Muestra cómo usar el Bus de servicio de Microsoft Azure para enviar y recibir mensajes de forma asincrónica desde una cola. La cola ofrece comunicación asincrónica desacoplada entre un remitente y cualquier número de receptores (aquí, un único receptor). |1.8 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |
| [Mensajería asíncrona: filtros avanzados](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) |Muestra cómo usar los filtros avanzados publicación-suscripción del Bus de servicio de Microsoft Azure. Crea un tema y 3 suscripciones con distintas definiciones de filtro, envía mensajes al tema y recibe todos los mensajes de las suscripciones. |1.8 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |
| [Mensajería asíncrona: captura previa de los mensajes](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d) |Muestra cómo usar la característica de captura previa de mensajes del Bus de servicio de Microsoft Azure. Muestra cómo usar la característica de captura previa de mensajes al recibirlos. |1.8 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |

## <a name="service-bus-reference-tools"></a>Herramientas de referencia del Bus de servicio
Los ejemplos siguientes muestran otras características del servicio.

| Nombre del ejemplo | Description | Versión mínima del SDK | Disponibilidad |
| --- | --- | --- | --- |
| [Explorador del Bus de servicio](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a) |El Explorador del Bus de servicio permite a los usuarios conectarse a un espacio de nombres del servicio del Bus de servicio y administrar las entidades de mensajería de una forma sencilla. La herramienta ofrece características avanzadas, como la funcionalidad de importación y exportación y la capacidad de probar las entidades de mensajería y servicios de retransmisión. |1.8 |Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server |
| [Autorización: SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93) |Este ejemplo muestra cómo crear y administrar las identidades del servicio del Control de acceso de Microsoft Azure Active Directory (también llamado Servicio de control de acceso o ACS) para su uso con el Bus de servicio. |N/D |Bus de servicio de Microsoft Azure |

## <a name="next-steps"></a>Pasos siguientes
Consulte los siguientes temas para obtener conceptos generales sobre Service Bus.

* [Introducción a la mensajería del Bus de servicio](service-bus-messaging-overview.md)
* [Arquitectura del Bus de servicio](service-bus-architecture.md)
* [Elementos fundamentales del Bus de servicio](service-bus-fundamentals-hybrid-solutions.md)




<!--HONumber=Nov16_HO3-->


