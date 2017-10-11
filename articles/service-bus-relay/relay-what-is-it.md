---
title: "¿Qué es Azure Relay y por qué se usa? | Microsoft Docs"
description: "Información general sobre Relay de Azure"
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 1e3e971d-2a24-4f96-a88a-ce3ea2b1a1cd
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: sethm
ms.openlocfilehash: 77ee85db0bcc701514a1a98da9405a79d658d49d
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="what-is-azure-relay"></a>¿Qué es Relay de Azure?

El servicio Relay de Azure facilita las aplicaciones híbridas, ya que permite exponer de forma segura los servicios que se encuentran en una red corporativa en la nube pública sin tener que abrir una conexión de firewall y sin que sea necesario realizar cambios molestos en una infraestructura de red corporativa. Relay admite diversos protocolos de transporte y estándares de servicios web.

El servicio de retransmisión admite mensajería unidireccional tradicional, de solicitud/respuesta y de punto a punto. También admite la distribución de eventos en el ámbito de internet para habilitar escenarios de publicación/suscripción y la comunicación de socket bidireccional para aumentar la eficacia punto a punto. 

En el patrón de transferencia de datos, un servicio local se conecta al servicio de relé mediante un puerto de salida y crea un socket bidireccional para la comunicación enlazada a una dirección de encuentro concreta. Después el cliente puede comunicarse con el servicio local enviando tráfico al servicio de retransmisión destinados a la dirección de encuentro. El servicio de retransmisión "retransmite" entonces los datos al servicio local a través de un socket bidireccional dedicado a cada cliente. El cliente no necesita una conexión directa al servicio local, no es necesario saber dónde reside el servicio, y el servicio local no necesita ningún puerto de entrada abierto en el firewall.

Los elementos de las funcionalidades claves que proporciona Relay son mensajes bidireccionales y no almacenados en búfer en los límites de red con limitación de tipo TCP, detección de puntos de conexión, estado de conectividad y seguridad de puntos de conexión superpuesta. Las funcionalidades de Relay se diferencian de las tecnologías de integración en el nivel de red, como VPN, en que pueden alcanzar un punto de conexión de aplicaciones de un solo equipo, mientras que la tecnología VPN es mucho más intrusiva, ya que se basa en modificar el entorno de red.

Relay de Azure tiene dos características:

1. [Conexiones híbridas](#hybrid-connections): usa los sockets web de estándar abierto, con lo que se admiten escenarios multiplataforma.
2. [Retransmisiones de WCF](#wcf-relays): usa Windows Communication Foundation (WCF) para habilitar las llamadas a procedimientos remotos. WCF Relay es la oferta de Relay heredada que muchos clientes ya pueden utilizar con sus modelos de programación de WCF.

Conexiones híbridas y Retransmisiones de WCF habilitan una conexión segura a los recursos de dentro de una red corporativa. El uso de una u otra depende de sus necesidades particulares que se detallan en la siguiente tabla:

|  | Retransmisión de WCF | conexiones híbridas |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |x |
| **JavaScript/NodeJS** | |x |
| **Protocolo abierto basado en estándares** | |x |
| **Varios modelos de programación de RPC** | |x |

## <a name="hybrid-connections"></a>conexiones híbridas

La funcionalidad [Conexiones híbridas de Azure Relay](relay-hybrid-connections-protocol.md) es una evolución segura y de protocolo abierto de las características de retransmisión existentes que se pueden implementar en cualquier plataforma y en cualquier lenguaje que tenga una funcionalidad de WebSocket básica, que incluya explícitamente la API de WebSocket en los exploradores web habituales. Conexiones híbridas se basa en HTTP y WebSockets.

### <a name="service-history"></a>Historial de servicios

Conexiones híbridas sustituye a la anterior característica, denominada igualmente "BizTalk Services", que se basa en Retransmisión de WCF de Azure Service Bus. La nueva funcionalidad Conexiones híbridas complementa a la característica Retransmisión de WCF existente y estas dos funcionalidades de servicio se ejecutarán en paralelo en el servicio Azure Relay. Aunque comparten una puerta de enlace común, se trata de implementaciones diferentes.

## <a name="wcf-relays"></a>Relés de WCF

Retransmisión de WCF es totalmente compatible con NET Framework (NETFX) y WCF. La conexión entre el servicio local y el servicio de retransmisión se inicia mediante un conjunto de enlaces de “retransmisión” WCF. En segundo plano, los enlaces de retransmisión se asignan a nuevos elementos de enlace de transporte diseñados para crear componentes de canal WCF que se integran con el Bus de servicio en la nube.

## <a name="architecture-processing-of-incoming-relay-requests"></a>Arquitectura: Procesamiento de solicitudes entrantes de retransmisión
Cuando un cliente envía una solicitud a [Azure Relay](/azure/service-bus-relay/), Azure Load Balancer lo enruta a cualquiera de los nodos de puerta de enlace. Si la solicitud es una solicitud de escucha, el nodo de puerta de enlace crea una nueva retransmisión. Si la solicitud es una solicitud de conexión a una retransmisión específica, el nodo de puerta de enlace reenvía la solicitud de conexión al nodo de puerta de enlace que posee la retransmisión. El nodo de puerta de enlace que posee la retransmisión envía una solicitud de encuentro al cliente de escucha, solicitando al agente de escucha crear un canal temporal para el nodo de puerta de enlace que recibió la solicitud de conexión.

Cuando se establece la conexión de retransmisión, los clientes pueden intercambiar mensajes a través del nodo de puerta de enlace que se usa para el encuentro.

![Procesamiento de solicitudes entrantes de retransmisión WCF](./media/relay-what-is-it/ic690645.png)

## <a name="next-steps"></a>Pasos siguientes

* [Preguntas más frecuentes acerca de Relay](relay-faq.md)
* [Creación de un espacio de nombres](relay-create-namespace-portal.md)
* [Introducción a .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introducción a Node](relay-hybrid-connections-node-get-started.md)

