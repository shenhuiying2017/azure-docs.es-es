---
title: "Información general sobre ejemplos de Service Bus Relay | Microsoft Docs"
description: "Se clasifican por categorías y se describen los ejemplos de Service Bus Relay con vínculos a cada uno."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9237a9a2-f126-4d3f-9f9b-604ee6b32153
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0b2206fae964c55e961505b9d0e2ad71fd0b2102


---
# <a name="service-bus-relay-samples"></a>Ejemplos de Service Bus Relay
En los ejemplos de Service Bus Relay se muestran las características principales de [Service Bus Relay](https://azure.microsoft.com/services/service-bus/). En este artículo se categorizan y describen los ejemplos disponibles, con vínculos a cada uno.

> [!NOTE]
> Los ejemplos del Bus de servicio no se instalan con el SDK. Para obtener los ejemplos, visite la [página de ejemplos del SDK de Azure](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
> 
> Además, encontrará un conjunto de ejemplos actualizado de Service Bus Relay [aquí](https://github.com/Azure-Samples/azure-servicebus-relay-samples) (en el momento de escribir este documento, no se describen en este artículo).  
> 
> 

Para obtener ejemplos de mensajes, vea [Ejemplos de mensajería de Service Bus](../service-bus-messaging/service-bus-samples.md).

## <a name="service-bus-relay"></a>Retransmisión del Bus de servicio
En los ejemplos siguientes se muestra cómo escribir aplicaciones que usan el servicio Service Bus Relay.

Tenga en cuenta que los ejemplos de retransmisión necesitan una cadena de conexión para acceder al espacio de nombres de Service Bus.

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

## <a name="service-bus-relay"></a>Retransmisión del Bus de servicio
Ejemplos que muestran retransmisión del Bus de servicio.

### <a name="getting-started"></a>Introducción
| Nombre del ejemplo | Description | Versión mínima del SDK | Disponibilidad |
| --- | --- | --- | --- |
| [Mensajería retransmitida de WCF: Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3) |Muestra cómo ejecutar un cliente y un servicio del Bus de servicio en Azure. Este ejemplo configura el Bus de servicio mediante programación. Solo se almacena información de entorno y de seguridad en los archivos de configuración. |1.8 |Bus de servicio de Microsoft Azure |
| [Autenticación de mensajería retransmitida de WCF: secreto compartido](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02) |Muestra cómo usar un nombre de emisor y el secreto del emisor para autenticar con el Bus de servicio. |1.8 |Bus de servicio de Microsoft Azure |
| [Autenticación de mensajería retransmitida de WCF: WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831) |Muestra cómo exponer un servicio HTTP que no requiere autenticación de los usuarios del cliente. |1.8 |Bus de servicio de Microsoft Azure |
| [Enlaces de mensajería retransmitida de WCF: WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0) |Muestra cómo usar el enlace **WebHttpRelayBinding** para devolver datos binarios mediante el modelo de programación web. |1.8 |Bus de servicio de Microsoft Azure |
| [Enlaces de mensajería retransmitida de WCF: NetTcp retransmitido](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692) |Muestra cómo usar el enlace **NetTcpRelayBinding** . |1.8 |Bus de servicio de Microsoft Azure |

### <a name="exploring-features"></a>Exploración de las características
Ejemplos que muestran diversas características de retransmisión del Bus de servicio.

| Nombre del ejemplo | Description | Versión mínima del SDK | Disponibilidad |
| --- | --- | --- | --- |
| [Autenticación de mensajería retransmitida de WCF: WebToken simple](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392) |Muestra cómo usar una credencial de token web simple para autenticar con el Bus de servicio. El ejemplo es similar al ejemplo Echo, con algunos cambios. En concreto, este ejemplo agrega un comportamiento en las aplicaciones ServiceHost (servicio) y ChannelFactory (cliente). |1.8 |Bus de servicio de Microsoft Azure |
| [Mensajería retransmitida de WCF: equilibrio de carga](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8) |Muestra cómo usar el Bus de servicio de Microsoft Azure para redirigir mensajes a varios receptores. Muestra varias instancias de un servicio simple que se comunica con un cliente mediante el enlace **NetTcpRelayBinding** |1.8 |Bus de servicio de Microsoft Azure |
| [Enlaces de mensajería retransmitida de WCF: evento de red](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977) |Muestra cómo usar el enlace **NetEventRelayBinding** del Bus de servicio de Microsoft Azure. |1.8 |Bus de servicio de Microsoft Azure |
| [Enlaces de mensajería retransmitida de WCF: sesión de WS2007Http](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb) |Muestra cómo usar el enlace **WS2007HttpRelayBinding** con sesiones de confianza activadas. También muestra cómo especificar credenciales del Bus de servicio en el archivo de configuración en lugar de mediante programación. |1.8 |Bus de servicio de Microsoft Azure |
| [Enlaces de mensajería retransmitida de WCF: WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5) |Muestra cómo usar el enlace **WS2007HttpRelayBinding** con seguridad de mensajes para proteger los mensajes de extremo a extremo mientras sigue exigiendo que los clientes se autentiquen con el Bus de servicio. |1.8 |Bus de servicio de Microsoft Azure |
| [Mensajería retransmitida de WCF: intercambio de metadatos](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e) |Muestra cómo exponer un extremo de metadatos que usa el enlace de retransmisión. **MetadataExchange** se admite en los siguientes enlaces de retransmisión: **NetTcpRelayBinding**, **NetOnewayRelayBinding**, **BasicHttpRelayBinding** y **WS2007HttpRelayBinding**. |1.8 |Bus de servicio de Microsoft Azure |
| [Enlaces de mensajería retransmitida de WCF: NetTcp directo](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161) |Muestra cómo configurar el enlace **NetTcpRelayBinding** para admitir el modo de conexión **híbrida**, que primero establece una conexión retransmitida y, si es posible, cambia automáticamente a una conexión directa entre un cliente y un servicio. |1.8 |Bus de servicio de Microsoft Azure |
| [Enlaces de mensajería retransmitida de WCF: nombre de usuario NetTcp MsgSec](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392) |Muestra cómo usar el enlace **NetTcpRelayBinding** con seguridad de mensajes. |1.8 |Bus de servicio de Microsoft Azure |
| [Enlaces de mensajería retransmitida de WCF: Net Oneway](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a) |Muestra cómo exponer y consumir un extremo del servicio mediante el enlace **NetOnewayRelayBinding** . |1.8 |Bus de servicio de Microsoft Azure |
| [Enlaces de mensajería retransmitida de WCF: WS2007Http simple](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a) |Muestra cómo usar el enlace **WS2007HttpRelayBinding** . Muestra un servicio simple que no usa las opciones de seguridad y no requiere que los clientes se autentiquen. |1.8 |Bus de servicio de Microsoft Azure |

## <a name="next-steps"></a>Pasos siguientes
Consulte los siguientes temas para obtener conceptos generales sobre Service Bus.

* [Información general sobre Service Bus Relay](service-bus-relay-overview.md)
* [Arquitectura del Bus de servicio](../service-bus-messaging/service-bus-architecture.md)
* [Elementos fundamentales de Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)




<!--HONumber=Nov16_HO3-->


