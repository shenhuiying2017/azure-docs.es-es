---
title: "Envío de eventos a Azure Event Hubs mediante Java | Microsoft Docs"
description: "Introducción al envío de eventos a Event Hubs mediante Java"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: aa7244849f6286e8ef9f9785c133b4c326193c12
ms.openlocfilehash: feb466064f2e26a329977240eeafb28148bdf212


---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Envío de eventos a Azure Event Hubs mediante Java

## <a name="introduction"></a>Introducción
Centros de eventos es un sistema de recopilación de alta escalabilidad que puede recibir millones de eventos por segundo, habilitando una aplicación para procesar y analizar las grandes cantidades de datos generados por las aplicaciones y los dispositivos conectados. Una vez recopilados en los Centros de eventos, puede transformar y almacenar los datos usando cualquier proveedor de análisis en tiempo real o clúster de almacenamiento.

Para más información, consulte [Información general de Event Hubs][Event Hubs overview].

Este tutorial muestra cómo enviar eventos a un centro de eventos mediante una aplicación de consola en Java. Para recibir eventos mediante la biblioteca Event Processor Host de Java, consulte [este artículo](event-hubs-java-get-started-receive-eph.md) o haga clic en el idioma de recepción adecuado en la tabla de contenido de la izquierda.

Para completar este tutorial necesitará lo siguiente:

* Un entorno de desarrollo de Java. Para este tutorial, asumimos que vamos a trabajar con [Eclipse](https://www.eclipse.org/).
* Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una cuenta gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluación gratuita de Azure</a>.

## <a name="send-messages-to-event-hubs"></a>Envío de mensajes a Centros de eventos
La biblioteca de cliente de Java para Centros de eventos está disponible para su uso en proyectos de Maven en el [repositorio central de Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22), y se puede hacer referencia a ella mediante la siguiente declaración de dependencia en el archivo de proyecto de Maven:    

``` XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
```

Para distintos tipos de entornos de compilación, puede obtener explícitamente los últimos archivos JAR publicados en el [repositorio central de Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) o en el [punto de distribución de versiones en GitHub](https://github.com/Azure/azure-event-hubs/releases).  

Para un editor de eventos simples, importe el paquete *com.microsoft.azure.eventhubs* para las clases de cliente de Event Hubs y el paquete *com.microsoft.azure.servicebus* para las clases de utilidad, como las excepciones comunes que se comparten con el cliente de mensajería de Azure Service Bus. 

Para el ejemplo siguiente, primero cree un nuevo proyecto de Maven para una aplicación de consola o shell en su entorno de desarrollo de Java favorito. La clase se llamará ```Send```.     

``` Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
    public static void main(String[] args) 
            throws ServiceBusException, ExecutionException, InterruptedException, IOException
    {
```

Reemplace el nombre del espacio de nombres y del Centro de eventos por los valores que usó al crear el Centro de eventos.

``` Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
    ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

A continuación, cree un evento singular convirtiendo una cadena en su codificación de bytes UTF-8. Después creamos una nueva instancia de cliente de Centros de eventos a partir de la cadena de conexión y enviamos el mensaje.   

``` Java 

    byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
    EventData sendEvent = new EventData(payloadBytes);

    EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
    ehClient.sendSync(sendEvent);
    }
}

``` 

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de Event Hubs, visite los vínculos siguientes:

* [Recepción de eventos mediante EventProcessorHost](event-hubs-java-get-started-receive-eph.md)
* [Información general de Event Hubs](event-hubs-what-is-event-hubs.md)
* [Creación de un Centro de eventos](event-hubs-create.md)
* [Preguntas más frecuentes sobre Event Hubs](event-hubs-faq.md)


<!--HONumber=Feb17_HO1-->


