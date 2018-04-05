---
title: Envío de eventos a Azure Event Hubs mediante Java | Microsoft Docs
description: Introducción al envío de eventos a Event Hubs mediante Java
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: sethm
ms.openlocfilehash: 5dd0c88dab9ff4b7073a9acf6872b4c3ff085586
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Envío de eventos a Azure Event Hubs mediante Java

Event Hubs es un sistema de recopilación de alta escalabilidad que puede recibir millones de eventos por segundo, habilitando una aplicación para procesar y analizar las grandes cantidades de datos generados por las aplicaciones y los dispositivos conectados. Una vez recopilados en un centro de eventos, puede transformar y almacenar los datos usando cualquier proveedor de análisis en tiempo real o clúster de almacenamiento.

Para más información, consulte [Información general de Event Hubs][Event Hubs overview].

Este tutorial muestra cómo enviar eventos a un centro de eventos mediante una aplicación de consola en Java. Para recibir eventos mediante la biblioteca Event Processor Host de Java, consulte [este artículo](event-hubs-java-get-started-receive-eph.md) o haga clic en el idioma de recepción adecuado en la tabla de contenido de la izquierda.

Para completar este tutorial necesitará lo siguiente:

* Un entorno de desarrollo de Java. En este tutorial, se da por hecho que se va a trabajar con [Eclipse](https://www.eclipse.org/).
* Una cuenta de Azure activa. Si no tiene una suscripción a Azure, cree una [cuenta gratuita][] antes de empezar.

El código de este tutorial se basa en el [ejemplo Enviar GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/Send), que se puede examinar para ver toda la aplicación en funcionamiento.

## <a name="send-events-to-event-hubs"></a>Envío de eventos a Event Hubs

La biblioteca de cliente de Java para Event Hubs está disponible para su uso en proyectos de Maven desde el [repositorio central de Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Puede hacer referencia a esta biblioteca con la siguiente declaración de dependencia en el archivo de proyecto de Maven. La versión actual es 1.0.0:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.0</version>
</dependency>
```

Para distintos tipos de entornos de compilación, puede obtener explícitamente los últimos archivos JAR publicados del [repositorio central de Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Para un editor de eventos simples, importe el paquete *com.microsoft.azure.eventhubs* para las clases de cliente de Event Hubs y el paquete *com.microsoft.azure.servicebus* para las clases de utilidad, como las excepciones comunes que se comparten con el cliente de mensajería de Azure Service Bus. 

### <a name="declare-the-send-class"></a>Declare la clase de envío

Para el ejemplo siguiente, primero cree un nuevo proyecto de Maven para una aplicación de consola o shell en su entorno de desarrollo de Java favorito. Asigne `Send` como nombre de la clase:     

```java
package com.microsoft.azure.eventhubs.samples.send;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.PartitionSender;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.Random;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ExecutorService;

public class Send {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
```

### <a name="construct-connection-string"></a>Crear cadena de conexión

Use la clase ConnectionStringBuilder para construir un valor de cadena de conexión y pasarlo a la instancia de cliente de Event Hubs. Reemplace los marcadores de posición por los valores que obtuvo al crear el espacio de nombres y el centro de eventos:

```java
   final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
      .setNamespaceName("----NamespaceName-----")
      .setEventHubName("----EventHubName-----")
      .setSasKeyName("-----SharedAccessSignatureKeyName-----")
      .setSasKey("---SharedAccessSignatureKey----");
```

### <a name="send-events"></a>Envío de eventos

A continuación, cree un evento singular transformando una cadena en su codificación de bytes UTF-8. Después creamos una nueva instancia de cliente de Event Hubs a partir de la cadena de conexión y enviamos el mensaje.   

```java 
byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
EventData sendEvent = new EventData(payloadBytes);

final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);
ehClient.sendSync(sendEvent);
    
// close the client at the end of your program
ehClient.closeSync();

``` 

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de Event Hubs, visite los vínculos siguientes:

* [Recepción de eventos mediante EventProcessorHost](event-hubs-java-get-started-receive-eph.md)
* [Información general de Event Hubs][Event Hubs overview]
* [Creación de un centro de eventos](event-hubs-create.md)
* [Preguntas más frecuentes sobre Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[cuenta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

