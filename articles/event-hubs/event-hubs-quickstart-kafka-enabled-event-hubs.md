---
title: Streaming en Azure Event Hubs para Kafka Ecosystem | Microsoft Docs
description: Streaming en Event Hubs mediante el protocolo y las API de Kafka.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2018
ms.author: bahariri
ms.openlocfilehash: cabbb7ed6157a6c68530ab6b5f405aa67b31a1b2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="stream-into-event-hubs-for-kafka-ecosystem"></a>Streaming en Event Hubs para Kafka Ecosystem

> [!NOTE]
> Este ejemplo está disponible en [GitHub](https://github.com/Azure/azure-event-hubs).

En esta guía de inicio rápido se muestra cómo transmitir a instancias de Event Hubs habilitadas para Kafka sin cambiar los clientes del protocolo o ejecutar sus propios clústeres. Aprenderá a usar sus productores y consumidores para hablar con instancias de Event Hubs habilitadas para Kafka con un simple cambio de configuración en sus aplicaciones. Azure Event Hubs para Kafka Ecosystem admite [Apache Kafka versión 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>requisitos previos

Para completar esta guía de inicio rápido, asegúrese de que dispone de lo siguiente:

* Una suscripción de Azure. Si no tiene una, cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.
* [Kit de desarrollo de Java (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Descargue](http://maven.apache.org/download.cgi) e [instale](http://maven.apache.org/install.html) un archivo binario de Maven.
* [Git](https://www.git-scm.com/)
* [Un espacio de nombres de Event Hubs habilitado para Kafka](event-hubs-create.md)

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Envío y recepción de mensajes con Kafka en Event Hubs

1. Clone el [repositorio de Azure Event Hubs](https://github.com/Azure/azure-event-hubs).

2. Vaya a `azure-event-hubs/samples/kafka/quickstart/producer`.

3. Actualice los detalles de configuración del productor en src/main/resources/producer.config, como se muestra aquí.

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
4. Ejecute el código de productor y transmítalo a Event Hubs habilitado para Kafka.
   
    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
5. Vaya a azure-event-hubs/samples/kafka/quickstart/consumer.

6. Actualice los detalles de configuración del consumidor en src/main/resources/consumer.config, como se muestra aquí.
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Ejecute el código de consumidor y procéselo desde Event Hubs habilitado para Kafka mediante los clientes de Kafka.

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Si el clúster de Kafka de Event Hubs tiene eventos en cola del productor, debería empezar a recibirlos ahora desde el consumidor.

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre Event Hubs para Kafka Ecosystem](event-hubs-for-kafka-ecosystem-overview.md)
* [Más información sobre Events Hubs](event-hubs-what-is-event-hubs.md)
* Use [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para transmitir eventos desde el entorno local de Kafka a Event Hubs habilitados para Kafka cloud.](event-hubs-kafka-mirror-maker-tutorial.md)
