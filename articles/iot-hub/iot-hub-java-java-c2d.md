<properties
	pageTitle="Envío de mensajes de nube a dispositivo con el Centro de IoT | Microsoft Azure"
	description="Siga este tutorial para aprender a enviar mensajes de nube a dispositivo mediante el Centro de IoT de Azure con Java."
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/23/2016"
     ms.author="dobett"/>

# Tutorial: cómo enviar mensajes de la nube a un dispositivo con el Centro de IoT y Java

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## Introducción

Centro de IoT de Azure es un servicio totalmente administrado que ayuda a habilitar la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y un back-end de aplicación. El tutorial [Introducción al Centro de IoT] muestra cómo crear un Centro de IoT, aprovisionar la identidad de un dispositivo en él y codificar un dispositivo simulado que envía mensajes de dispositivo a nube.

Este tutorial se basa en la [introducción al Centro de IoT]. En él se muestra cómo realizar las siguientes acciones:

- Desde el back-end de la nube de la aplicación, envíe mensajes de la nube al dispositivo en un único dispositivo a través de Centro de IoT.
- Reciba mensajes de nube a dispositivo en un dispositivo.
- Desde el back-end de la nube de la aplicación, solicite confirmación de entrega (*comentarios*) para los mensajes enviados a un dispositivo desde Centro de IoT.

Encontrará más información sobre los mensajes de nube a dispositivo en la [Guía para desarrolladores del Centro de IoT][IoT Hub Developer Guide - C2D].

Al final de este tutorial, ejecutará dos aplicaciones de consola de Java:

* **simulated-device**, versión modificada de la aplicación creada en la [introducción al Centro de IoT], que se conecta al Centro de IoT y recibe mensajes de nube a dispositivo.
* **send-c2d-messages**, que envía un mensaje de nube a dispositivo al dispositivo simulado mediante el Centro de IoT y luego recibe su confirmación de entrega.

> [AZURE.NOTE] El Centro de IoT ofrece compatibilidad con el SDK en muchas plataformas de dispositivos y lenguajes (incluido C, Java y Javascript), mediante los SDK de dispositivos IoT de Azure. Visite el [Centro para desarrolladores de IoT de Azure] para obtener instrucciones paso a paso sobre cómo conectar el dispositivo al código de este tutorial y, en general, al Centro de IoT de Azure.

Para completar este tutorial, necesitará lo siguiente:

+ Java SE 8. <br/> [Prepare your development environment][lnk-dev-setup] (Preparación del entorno de desarrollo) describe cómo instalar Java para este tutorial en Windows o Linux.

+ Maven 3. <br/> [Prepare your development environment][lnk-dev-setup] (Preparación del entorno de desarrollo) describe cómo instalar Maven para este tutorial en Windows o Linux.

+ Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte [Evaluación gratuita de Azure][lnk-free-trial].

## Recepción de mensajes en el dispositivo simulado

En esta sección, modificará la aplicación de dispositivo simulado que creó en [Introducción al Centro de IoT de Azure para .NET] para recibir mensajes de la nube a un dispositivo desde el Centro de IoT.

1. Con un editor de texto, abra el archivo simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java.

2. Agregue la siguiente clase **MessageCallback** como una clase anidada dentro de la clase **aplicación**. El método **execute** se invoca cuando el dispositivo recibe un mensaje del Centro de IoT. En este ejemplo, el dispositivo siempre notifica el centro que ha completado el mensaje:

    ```
    private static class MessageCallback implements
    com.microsoft.azure.iothub.MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Modifique el método **main** para crear una instancia de **MessageCallback** y llame al método **setMessageCallback** antes de que se abra el cliente, de la manera siguiente:

    ```
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new MessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [AZURE.NOTE] Si usa HTTP/1 en lugar de AMQP como transporte, la instancia de **DeviceClient** busca mensajes del Centro de IoT con menos frecuencia (menos de 25 minutos). Consulte la [Guía del desarrollador del Centro de IoT][IoT Hub Developer Guide - C2D] para más información sobre las diferencias entre la compatibilidad con AMQP y HTTP/1, y la limitación del Centro de IoT.

## Envío de un mensaje de nube a dispositivo desde el back-end de la aplicación

En esta sección, escribirá una aplicación de consola de Java que envía mensajes de nube a dispositivo a la aplicación del dispositivo simulado. Necesitará el identificador del dispositivo que agregó en el tutorial de [introducción al Centro de IoT] y la cadena de conexión de su centro de IoT que encontrará en el [Portal de Azure].

1. Cree un nuevo proyecto de Maven denominado **send-c2d-messages** mediante el siguiente comando en el símbolo del sistema. Tenga en cuenta que es un comando único y largo.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. En el símbolo del sistema, vaya a la nueva carpeta send-c2d-messages.

3. Con un editor de texto, abra el archivo pom.xml de la carpeta send-c2d-messages y agregue la siguiente dependencia al nodo **dependencies**. Esto le permite usar el paquete **iothub-java-service-client** en la aplicación para comunicarse con el servicio Centro de IoT:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.7</version>
    </dependency>
    ```

4. Guarde y cierre el archivo pom.xml.

5. Con un editor de texto, abra el archivo send-c2d-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Agregue las siguientes instrucciones **import** al archivo:

    ```
    import com.microsoft.azure.iot.service.sdk.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Agregue las siguientes variables de nivel de clase a la clase **App** y sustituya **{yourhubconnectionstring}** y **{yourdeviceid}** por los valores anotados anteriormente:

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    ```
    
8. Reemplace el método **main** por el siguiente código que conecta con el centro de IoT, envía un mensaje al dispositivo y luego espera una confirmación de que el dispositivo ha recibido y procesado el mensaje:

    ```
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
      
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver(deviceId);
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

    > [AZURE.NOTE] Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling] (Control de errores transitorios).

## Ejecución de las aplicaciones

Ahora está preparado para ejecutar las aplicaciones.

1. En un símbolo del sistema, en la carpeta simulated-device, ejecute el siguiente comando para empezar el envío de datos de telemetría al Centro de IoT:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![][img-simulated-device]

2. En un símbolo del sistema de la carpeta send-c2d-messages, ejecute el siguiente comando para enviar un mensaje de la nube a dispositivo y esperar una confirmación de comentarios:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![][img-send-command]

## Pasos siguientes

En este tutorial, aprendió a enviar y recibir mensajes de nube a dispositivo. Puede seguir explorando las características del Centro de IoT y los distintos escenarios con los tutoriales siguientes:

- [Procesamiento de mensajes de dispositivo a la nube] muestra cómo procesar de forma confiable la telemetría y los mensajes interactivos procedentes de los dispositivos.
- [Carga de archivos desde dispositivos], describe un patrón que usa los mensajes de nube a dispositivo para facilitar la carga de archivos desde los dispositivos.

Información adicional sobre el centro de IoT:

* [Información general sobre el centro de IoT]
* [Guía del desarrollador del centro de IoT]
* [Directrices sobre el centro de IoT]
* [Lenguajes y plataformas de dispositivos compatibles]
* [Centro para desarrolladores de Azure]


<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]: media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[Introducción al Centro de IoT]: iot-hub-java-java-getstarted.md
[Introducción al Centro de IoT de Azure para .NET]: iot-hub-java-java-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Procesamiento de mensajes de dispositivo a la nube]: iot-hub-csharp-csharp-process-d2c.md
[Carga de archivos desde dispositivos]: iot-hub-csharp-csharp-file-upload.md
[Información general sobre el centro de IoT]: iot-hub-what-is-iot-hub.md
[Directrices sobre el centro de IoT]: iot-hub-guidance.md
[Guía del desarrollador del centro de IoT]: iot-hub-devguide.md
[Lenguajes y plataformas de dispositivos compatibles]: iot-hub-supported-devices.md
[Centro para desarrolladores de Azure]: http://www.azure.com/develop/iot
[Centro para desarrolladores de IoT de Azure]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Portal de Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0706_2016-->