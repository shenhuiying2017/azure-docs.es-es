---
title: Azure Event Hubs para ecosistemas de Kafka | Microsoft Docs
description: Información general e introducción a Azure Event Hubs habilitados para Kafka
services: event-hubs
documentationcenter: .net
author: djrosanova
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 05/07/2018
ms.author: darosa
ms.openlocfilehash: 51e32737be4eaf3c61cdbe50b82a05c205800ac4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077613"
---
# <a name="event-hubs-for-kafka-ecosystems"></a>Event Hubs para ecosistemas de Kafka

Event Hubs para ecosistemas de Kafka proporciona un punto de conexión de Kafka que pueden usar sus aplicaciones basadas en Kafka existentes como alternativa a ejecutar su propio clúster de Kafka. Event Hubs para ecosistemas de Kafka admite [Apache Kafka 1.0](https://kafka.apache.org/10/documentation.html) y versiones del cliente más recientes, y funciona con sus aplicaciones existentes de Kafka, incluida MirrorMaker. Cambie la cadena de conexión y empiece a transmitir eventos desde sus aplicaciones que usan el protocolo de Kafka en Event Hubs.

## <a name="what-does-event-hubs-for-kafka-ecosystems-provide"></a>¿Qué proporciona Event Hubs para ecosistemas de Kafka?

Event Hubs para ecosistemas de Kafka proporciona un protocolo principal basado en Azure Event Hubs que es binario y compatible con la versión 1.0 de Kafka y posteriores, tanto para leer desde temas de Kafka como para escribir en ellos. Desde un punto de vista conceptual, Kafka y Event Hubs son casi idénticos: ambos son registros con particiones creados para transmitir datos. En la tabla siguiente, se asignan conceptos entre Kafka y Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Asignación conceptual de Kafka y Event Hubs

| Concepto de Kafka | Concepto de Event Hubs|
| --- | --- |
| Clúster | Espacio de nombres |
| Tema. | Event Hubs |
| Partition | Partition|
| Grupo de consumidores | Grupo de consumidores |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Diferencias principales entre Kafka y Event Hubs

Mientras que [Kafka Apache](https://kafka.apache.org/) es un software que se puede ejecutar siempre que elija, Event Hubs es un servicio en la nube similar a Azure Blob Storage. No hay ningún servidor o red que administrar ni ningún agente que configurar. Se crea un espacio de nombres, que es un FQDN en el que están los temas, y después se crean Event Hubs o temas dentro de ese espacio de nombres. Para obtener más información sobre Event Hubs y espacios de nombres, consulte [¿Qué es Event Hubs?](event-hubs-what-is-event-hubs.md) Como un servicio en la nube, Event Hubs usa una única dirección IP virtual estable como punto de conexión, de forma que los clientes no necesitan conocer los agentes o equipos de un clúster. 

La escala en Event Hubs se controla por el número de unidades de rendimiento que adquiere; cada unidad de rendimiento le otorga 1 MB por segundo o 1000 eventos por segundo de entrada. De forma predeterminada, Event Hubs escala verticalmente las unidades de rendimiento cuando alcanza el límite con la característica [Inflado automático](event-hubs-auto-inflate.md); esta característica también funciona con Event Hubs para ecosistemas de Kafka. 

### <a name="security-and-authentication"></a>Seguridad y autenticación

Azure Event Hubs requiere SSL o TLS para todas las comunicaciones y usa firmas de acceso compartido (SAS) para la autenticación. Este requisito también es válido para un punto de conexión de Kafka de Event Hubs. Por compatibilidad con Kafka, Event Hubs usa SASL PLAIN para la autenticación y SASL SSL para la seguridad de transporte. Para obtener más información sobre la seguridad en Event Hubs, vea [Autenticación y seguridad de Event Hubs](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Otras características de Event Hubs disponibles para Kafka

Event Hubs para el ecosistema de Kafka le permite escribir con un protocolo y leer con otro, para que los productores de Kafka actuales puedan seguir publicando a través de Kafka y usted pueda agregar lectores con Event Hubs, como Azure Stream Analytics o Azure Functions. Además, las características de Event Hubs como [Capture](event-hubs-capture-overview.md) y [Recuperación ante desastres geográfica](event-hubs-geo-dr.md) también funcionan con Event Hubs para ecosistemas de Kafka.

## <a name="features-that-are-not-supported-in-the-preview"></a>Características que no se admiten en la versión preliminar

Para la versión preliminar pública de la integración de Event Hubs para ecosistemas de Kafka, no se admiten las siguientes características de Kafka:

*   Productor idempotente
*   Transacción
*   Compresión
*   Retención basada en el tamaño
*   Compactación del registro
*   Adición de particiones a un tema existente
*   Compatibilidad con HTTP Kafka API
*   Kafka Connect
*   Kafka Streams

## <a name="next-steps"></a>Pasos siguientes

En este artículo, se proporciona una introducción a Event Hubs para ecosistemas de Kafka. Para obtener más información, consulte los siguientes vínculos:

* [How to create Kafka enabled Event Hubs](event-hubs-create-kafka-enabled.md) (Cómo crear Event Hubs habilitados para Kafka)
* [Stream into Event Hubs from your Kafka applications](event-hubs-quickstart-kafka-enabled-event-hubs.md) (Transmitir en Event Hubs desde sus aplicaciones de Kafka)
* Empiece por un [tutorial de Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Preguntas más frecuentes sobre Event Hubs](event-hubs-faq.md)

 
 

