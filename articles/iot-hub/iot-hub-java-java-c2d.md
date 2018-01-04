---
title: Mensajes de nube a dispositivo con IoT Hub de Azure (Java) | Microsoft Docs
description: "Cómo enviar mensajes de nube a un dispositivo de una instancia de IoT Hub de Azure mediante los SDK de IoT de Azure para Java. Modifique una aplicación de dispositivo simulado para recibir mensajes de nube a dispositivo y cambie una aplicación de back-end para enviar los mensajes de nube a dispositivo."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 7f785ea8-e7c2-40c5-87ef-96525e9b9e1e
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: 0c94b627ad80a1cc32bc0084d3792a917225d9c7
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Envío mensajes de nube a dispositivo con IoT Hub (Java)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

IoT Hub de Azure es un servicio totalmente administrado que permite la comunicación bidireccional confiable y segura entre millones de dispositivos y una solución de back-end. El tutorial [Introducción a Iot Hub] muestra cómo crear un centro de IoT, aprovisionar la identidad de un dispositivo en él y codificar una aplicación de dispositivo simulado que envía mensajes de dispositivo a nube.

Este tutorial se basa en la [Introducción a Iot Hub]. En él se muestra cómo realizar las siguientes acciones:

* Desde el back-end de la nube de la aplicación, envíe mensajes de nube a dispositivo en un único dispositivo a través de IoT Hub.
* Reciba mensajes de nube a dispositivo en un dispositivo.
* Desde el back-end de la nube de la aplicación, solicite confirmación de entrega (*comentarios*) para los mensajes enviados a un dispositivo desde IoT Hub.

Encontrará más información sobre los mensajes de nube a dispositivo en la [Guía para desarrolladores de IoT Hub][IoT Hub developer guide - C2D].

Al final de este tutorial, ejecutará dos aplicaciones de consola de Java:

* **simulated-device**, versión modificada de la aplicación creada en la [Introducción a Iot Hub], que se conecta al Centro de IoT y recibe mensajes de nube a dispositivo.
* **send-c2d-messages**, que envía un mensaje de nube a dispositivo a la aplicación de dispositivo simulado a través de IoT Hub y recibe la confirmación de entrega.

> [!NOTE]
> El Centro de IoT ofrece compatibilidad con el SDK en muchas plataformas de dispositivos y lenguajes (incluido C, Java y Javascript), mediante los SDK de dispositivos IoT de Azure. Visite el [Centro para desarrolladores de IoT de Azure]para obtener instrucciones paso a paso sobre cómo conectar el dispositivo al código de este tutorial y, en general, al Centro de IoT de Azure.

Para completar este tutorial, necesitará lo siguiente:

* Una versión funcional y completa del tutorial [Introducción a IoT Hub](iot-hub-java-java-getstarted.md) o [Procesamiento de mensajes de dispositivo a nube de IoT Hub](iot-hub-java-java-process-d2c.md).
* La versión más reciente de [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

## <a name="receive-messages-in-the-simulated-device-app"></a>Recepción de mensajes en la aplicación de dispositivo simulado

En esta sección, modificará la aplicación de dispositivo simulado que creó en el tutorial [Introducción a Iot Hub] para recibir mensajes de nube a dispositivo desde el centro de IoT.

1. Con un editor de texto, abra el archivo simulated-device\src\main\java\com\mycompany\app\App.java.

2. Agregue la siguiente clase **MessageCallback** como una clase anidada dentro de la clase **aplicación**. El método **execute** se invoca cuando el dispositivo recibe un mensaje del Centro de IoT. En este ejemplo, el dispositivo siempre notifica al centro de IoT que ha completado el mensaje:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
    
        return IotHubMessageResult.COMPLETE;
      }
    }
    ```
3. Modifique el método **main** para crear una instancia de **AppMessageCallback** y llame al método **setMessageCallback** antes de que se abra el cliente, de la manera siguiente:

    ```java
    client = new DeviceClient(connString, protocol);
   
    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Si usa HTTPS en lugar de MQTT o AMQP como transporte, la instancia de **DeviceClient** busca mensajes de IoT Hub con menos frecuencia (menos de 25 minutos). Para más información sobre las diferencias entre la compatibilidad con MQTT, AMQP y HTTPS, y la limitación de IoT Hub, consulte la [Guía del desarrollador de IoT Hub][IoT Hub developer guide - C2D].

4. Para compilar la aplicación **simulated-device** con Maven, ejecute el siguiente comando en el símbolo del sistema en la carpeta simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="send-a-cloud-to-device-message"></a>Envío de mensajes de nube a dispositivo

En esta sección, escribirá una aplicación de consola de Java que envía mensajes de nube a dispositivo a la aplicación del dispositivo simulado. Necesita el identificador de dispositivo que agregó en el tutorial de [Introducción a Iot Hub]. También necesita la cadena de conexión para IoT Hub que encontrará en [Azure Portal].

1. Cree un proyecto de Maven denominado **send-c2d-messages** mediante el siguiente comando en el símbolo del sistema. Observe que este es un comando único y largo:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. En el símbolo del sistema, vaya a la nueva carpeta send-c2d-messages.

3. Con un editor de texto, abra el archivo pom.xml de la carpeta send-c2d-messages y agregue la siguiente dependencia al nodo **dependencies** . Esto le permite usar el paquete **iothub-java-service-client** en la aplicación para comunicarse con el servicio IoT Hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Puede comprobar la versión más reciente de **iot-service-client** mediante la [funcionalidad de búsqueda de Maven][lnk-maven-service-search].

4. Guarde y cierre el archivo pom.xml.

5. Con un editor de texto, abra el archivo send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Agregue las siguientes instrucciones **import** al archivo:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Agregue las siguientes variables de nivel de clase a la clase **App** y sustituya **{yourhubconnectionstring}** y **{yourdeviceid}** por los valores anotados anteriormente:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    ```

8. Reemplace el método **main** por el código siguiente: Este código conecta con el centro de IoT, envía un mensaje al dispositivo y luego espera una confirmación de que el dispositivo ha recibido y procesado el mensaje:
   
    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
   
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();
   
        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);
   
        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");
   
        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }
   
        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Control de errores transitorios].


9. Para compilar la aplicación **simulated-device** con Maven, ejecute el siguiente comando en el símbolo del sistema en la carpeta simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Ejecución de las aplicaciones

Ahora está preparado para ejecutar las aplicaciones.

1. En un símbolo del sistema, en la carpeta simulated-device, ejecute el siguiente comando para empezar el envío de datos de telemetría a IoT Hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Ejecución de una aplicación de dispositivo simulada][img-simulated-device]

2. En un símbolo del sistema de la carpeta send-c2d-messages, ejecute el siguiente comando para enviar un mensaje de la nube a dispositivo y esperar una confirmación de comentarios:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Ejecución del comando para enviar el mensaje C2D][img-send-command]

## <a name="next-steps"></a>pasos siguientes

En este tutorial, aprendió a enviar y recibir mensajes de nube a dispositivo. 

Para ver ejemplos de soluciones completas de un extremo a otro que usen el Centro de IoT, consulte [Documentación del Conjunto de aplicaciones de IoT].

Para obtener más información sobre cómo desarrollar soluciones con IoT Hub, consulte la [Guía del desarrollador de IoTHub de Azure].

<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[Introducción a Iot Hub]: iot-hub-java-java-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Guía del desarrollador de IoTHub de Azure]: iot-hub-devguide.md
[Centro para desarrolladores de IoT de Azure]: http://azure.microsoft.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[Control de errores transitorios]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Portal]: https://portal.azure.com
[Documentación del Conjunto de aplicaciones de IoT]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22