---
title: "Introducción a Azure IoT Hub (Java) | Microsoft Docs"
description: "Obtenga información sobre cómo enviar mensajes del dispositivo a la nube a una instancia de Azure IoT Hub mediante los SDK de IoT para Java. Cree aplicaciones de servicio y de dispositivo simuladas para registrar el dispositivo, enviar mensajes y leerlos en IoT Hub."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 70dae4a8-0e98-4c53-b5a5-9d6963abb245
ms.service: iot-hub
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f41dd62d3f074eebe0c996a9c14bc7811d5b365
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="connect-your-device-to-your-iot-hub-using-java"></a>Conexión del dispositivo en IoT Hub con Java
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Al final de este tutorial tendrá tres aplicaciones de consola de Java:

* **create-device-identity**, que crea una identidad de dispositivo y una clave de seguridad asociada para conectar la aplicación de dispositivo.
* **read-d2c-messages**, que muestra los datos de telemetría enviados por la aplicación de dispositivo.
* **simulated-device**, que se conecta con su instancia de IoT Hub con la identidad del dispositivo creada anteriormente y envía un mensaje de telemetría cada segundo mediante el protocolo MQTT.

> [!NOTE]
> En el artículo [Azure Iot SDKs][lnk-hub-sdks] (SDK de IoT de Azure) se proporciona información acerca de los diversos SDK que puede usar para crear ambas aplicaciones para que se ejecuten en dispositivos y en el back-end de la solución.

Para completar este tutorial, necesitará lo siguiente:

* La versión más reciente de [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

El último paso es tomar nota del valor de **Primary key** (Clave principal). A continuación, haga clic en **Endpoints** (Puntos de conexión) y en el punto de conexión integrado en **Events** (Eventos). En la hoja **Properties** (Propiedades), anote los valores del **nombre compatible con el centro de eventos** y con la dirección del **punto de conexión compatible con centro de eventos**. Necesita estos tres valores al crear la aplicación **read-d2c-messages**.

![Hoja de mensajería de IoT Hub de Azure Portal][6]

Ahora ha creado su instancia de IoT Hub. Ahora tiene el nombre de host de IoT Hub, la cadena de conexión de IoT Hub, la clave principal de IoT Hub, el nombre compatible con Event Hubs y el punto de conexión compatible con Event Hubs que necesita para completar este tutorial.

## <a name="create-a-device-identity"></a>Creación de una identidad de dispositivo
En esta sección, creará una aplicación de consola Java que crea una identidad de dispositivo en el registro de identidades de su instancia de IoT Hub. No se puede conectar un dispositivo a IoT Hub a menos que tenga una entrada en el registro de identidades. Para más información, consulte la sección sobre el **registro de la identidad** de la [guía para desarrolladores de IoT Hub][lnk-devguide-identity]. Cuando ejecuta esta aplicación de consola, se genera una clave y un identificador de dispositivo únicos con el que el dispositivo puede identificarse cuando envía mensajes de dispositivo a la nube a IoT Hub.

1. Cree una carpeta vacía denominada iot-java-get-started. En la carpeta iot-java-get-started, cree un proyecto de Maven denominado **create-device-identity** mediante el comando siguiente en el símbolo del sistema. Observe que este es un comando único y largo:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. En el símbolo del sistema, vaya a la nueva carpeta create-device-identity.

3. Con un editor de texto, abra el archivo pom.xml en la carpeta create-device-identity y agregue la siguiente dependencia al nodo **dependencies** . Esta dependencia le permite usar el paquete iot-service-client en su aplicación:

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

5. Mediante un editor de texto, abra el archivo create-device-identity\src\main\java\com\mycompany\app\App.java.

6. Agregue las siguientes instrucciones **import** al archivo:

    ```java
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.Device;
    import com.microsoft.azure.sdk.iot.service.RegistryManager;
   
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Agregue las siguientes variables de nivel de clase a la clase **App** y sustituya **{yourhubconnectionstring}** por el valor anotado anteriormente:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
    ```
[!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

8. Modifique la firma del método **main** para incluir las excepciones de la siguiente manera:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```

9. Agregue el siguiente código como cuerpo del método **main** . Este código crea un dispositivo denominado *javadevice* en el registro de identidades de IoT Hub, si no existe. Después muestra el identificador de dispositivo y la clave que necesitará más adelante:

    ```java
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    // Create a device that's enabled by default, 
    // with an autogenerated key.
    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      // If the device already exists.
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }

    // Display information about the
    // device you created.
    System.out.println("Device Id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. Guarde y cierre el archivo App.java.

11. Para compilar la aplicación **create-device-identity** con Maven, ejecute el siguiente comando en el símbolo del sistema en la carpeta create-device-identity:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

12. Para ejecutar la aplicación **create-device-identity** con Maven, ejecute el siguiente comando en el símbolo del sistema en la carpeta create-device-identity:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Anote el **identificador del dispositivo** y la **clave del dispositivo**. Los necesitará más adelante cuando cree una aplicación que se conecta a IoT Hub como dispositivo.

> [!NOTE]
> El registro de identidades de IoT Hub solo almacena identidades de dispositivos para permitir el acceso seguro a IoT Hub. Almacena las claves y los identificadores de dispositivo para usarlos como credenciales de seguridad, y un indicador de habilitado o deshabilitado que permite deshabilitar el acceso a un dispositivo individual. Si la aplicación necesita almacenar otros metadatos específicos del dispositivo, debe usar un almacén específico de la aplicación. Consulte la [guía para desarrolladores de IoT Hub][lnk-devguide-identity] para más información.

## <a name="receive-device-to-cloud-messages"></a>Recepción de mensajes de dispositivo a nube

En esta sección, creará una aplicación de consola de Java que lee los mensajes de dispositivo a nube desde IoT Hub. IoT Hub expone un punto de conexión compatible con [Event Hubs][lnk-event-hubs-overview] para poder leer los mensajes de dispositivo a la nube. Para simplificar las cosas, este tutorial crea un lector básico que no es apto para una implementación de alta capacidad de procesamiento. El [tutorial: procesamiento de mensajes de dispositivo a la nube][lnk-process-d2c-tutorial] muestra cómo procesar mensajes de dispositivo a la nube a escala. En el tutorial [Introducción a Event Hubs][lnk-eventhubs-tutorial] se proporciona información adicional acerca de cómo procesar los mensajes desde Event Hubs. Dicha información se puede aplicar a los puntos de conexión de IoT Hub compatibles con Event Hubs.

> [!NOTE]
> El punto de conexión compatible con Event Hubs para leer mensajes de dispositivo a la nube siempre usa el protocolo AMQP.

1. En la carpeta iot-java-get-started creada en la sección *Creación de una identidad de dispositivo*, cree un proyecto de Maven denominado **read-d2c-messages** mediante el comando siguiente en el símbolo del sistema. Observe que este es un comando único y largo:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. En el símbolo del sistema, vaya a la nueva carpeta read-d2c-messages.

3. Con un editor de texto, abra el archivo pom.xml en la carpeta read-d2c-messages y agregue la siguiente dependencia al nodo **dependencies** . Esta dependencia permite usar el paquete eventhubs-client en la aplicación para leer desde el punto de conexión compatible con Event Hubs:

    ```xml
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.15.0</version> 
    </dependency>
    ```

4. Guarde y cierre el archivo pom.xml.

5. Con un editor de texto, abra el archivo read-d2c-messages\src\main\java\com\mycompany\app\App.java.

6. Agregue las siguientes instrucciones **import** al archivo:

    ```java
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;

    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.function.*;
    ```

7. Agregue la siguiente variable de nivel de clase a la clase **App** . Reemplace **{youriothubkey}**, **{youreventhubcompatiblenamespace}** y **{youreventhubcompatiblename}** por los valores anotados anteriormente:

    ```java
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```

8. Agregue el siguiente método **receiveMessages** a la clase **App**. Dicho método crea una instancia de **EventHubClient** para conectarse al punto de conexión compatible con Event Hubs y, luego, crea una instancia de **PartitionReceiver** de forma asincrónica para leer desde una partición de Event Hubs. Se repite continuamente e imprime los detalles de los mensajes hasta que finaliza la aplicación.

    ```java
    // Create a receiver on a partition.
    private static EventHubClient receiveMessages(final String partitionId) {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      } catch (Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        // Create a receiver using the
        // default Event Hubs consumer group
        // that listens for messages from now on.
        client.createReceiver(EventHubClient.DEFAULT_CONSUMER_GROUP_NAME, partitionId, Instant.now())
          .thenAccept(new Consumer<PartitionReceiver>() {
            public void accept(PartitionReceiver receiver) {
              System.out.println("** Created receiver on partition " + partitionId);
              try {
                while (true) {
                  Iterable<EventData> receivedEvents = receiver.receive(100).get();
                  int batchSize = 0;
                  if (receivedEvents != null) {
                    System.out.println("Got some events");
                    for (EventData receivedEvent : receivedEvents) {
                      System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s",
                        receivedEvent.getSystemProperties().getOffset(),
                        receivedEvent.getSystemProperties().getSequenceNumber(),
                        receivedEvent.getSystemProperties().getEnqueuedTime()));
                      System.out.println(String.format("| Device ID: %s",
                        receivedEvent.getSystemProperties().get("iothub-connection-device-id")));
                      System.out.println(String.format("| Message Payload: %s",
                        new String(receivedEvent.getBytes(), Charset.defaultCharset())));
                      batchSize++;
                    }
                  }
                  System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId, batchSize));
                }
              } catch (Exception e) {
                System.out.println("Failed to receive messages: " + e.getMessage());
              }
            }
          });
        } catch (Exception e) {
          System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```

   > [!NOTE]
   > Este método utiliza un filtro cuando crea el receptor para que este solo lea los mensajes enviados a IoT Hub después de que el receptor comience a ejecutarse. Esta técnica es útil en un entorno de prueba, porque puede ver el conjunto actual de mensajes. En un entorno de producción, el código debe garantizar que se procesan todos los mensajes. Consulte el tutorial[procesamiento de mensajes de dispositivo a la nube de IoT Hub][lnk-process-d2c-tutorial] para más información.

9. Modifique la firma del método **main** para incluir la excepción de la siguiente manera:

    ```java
    public static void main( String[] args ) throws IOException
    ```

10. Agregue el siguiente código al método **main** de la clase **App**. Este código crea las dos instancias de **EventHubClient** y **PartitionReceiver**, lo que le permite cerrar la aplicación cuando haya terminado de procesar los mensajes:

    ```java
    // Create receivers for partitions 0 and 1.
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    } catch (Exception e) {
      System.exit(1);
    }
    ```

    > [!NOTE]
    > Este código supone que usted creó la instancia de IoT Hub en el nivel F1 (gratis). Una instancia gratuita de IoT Hub tiene dos particiones denominadas "0" y "1".

11. Guarde y cierre el archivo App.java.

12. Para compilar la aplicación **read-d2c-messages** con Maven, ejecute el siguiente comando en el símbolo del sistema en la carpeta read-d2c-messages:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Creación de una aplicación de dispositivo
En esta sección, creará una aplicación de consola de Java que simula un dispositivo que envía mensajes de dispositivo a nube a IoT Hub.

1. En la carpeta iot-java-get-started que ha creado en la sección *Creación de una identidad de dispositivo*, cree un proyecto de Maven denominado **simulated-device** mediante el comando siguiente en el símbolo del sistema. Observe que este es un comando único y largo:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. En el símbolo del sistema, vaya a la nueva carpeta simulated-device.

3. Con un editor de texto, abra el archivo pom.xml de la carpeta simulated-device y agregue las siguientes dependencias al nodo **dependencies** . Esta dependencia le permite usar el paquete iothub-java-client en la aplicación para comunicarse con la instancia de IoT Hub y serializar los objetos de Java a JSON:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

    > [!NOTE]
    > Puede comprobar la versión más reciente de **iot-device-client** mediante la [funcionalidad de búsqueda de Maven][lnk-maven-device-search].

4. Guarde y cierre el archivo pom.xml.

5. Con un editor de texto, abra el archivo simulated-device\src\main\java\com\mycompany\app\App.java.

6. Agregue las siguientes instrucciones **import** al archivo:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.google.gson.Gson;

    import java.io.*;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

7. Agregue las siguientes variables de nivel de clase a la clase **App** . Reemplace **{youriothubname}** por el nombre de la instancia de IoT Hub y **{yourdevicekey}** por el valor de la clave de dispositivo que ha generado en la sección *Creación de una identidad de dispositivo*:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```
   
    Esta aplicación de ejemplo usa la variable **protocol** al crear una instancia de un objeto **DeviceClient**. Puede usar los protocolos MQTT, AMQP o HTTP para comunicarse con IoT Hub.

8. Agregue la siguiente clase **TelemetryDataPoint** anidada dentro de la clase **App** para especificar los datos de telemetría que el dispositivo envía a IoT Hub:

    ```java
    private static class TelemetryDataPoint {
      public String deviceId;
      public double temperature;
      public double humidity;
   
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```
9. Agregue la siguiente clase **EventCallback** anidada dentro de la clase **App** para mostrar el estado de confirmación que devuelve IoT Hub cuando procesa un mensaje de la aplicación de dispositivo. Este método también notifica el subproceso principal de la aplicación cuando se ha procesado el mensaje:
   
    ```java
    private static class EventCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
   
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. Agregue la siguiente clase **MessageSender** anidada dentro de la clase **App**. El método **run** de esta clase genera datos de telemetría de ejemplo que se envían a IoT Hub y espera confirmación antes de enviar el siguiente mensaje:

    ```java
    private static class MessageSender implements Runnable {
      public void run()  {
        try {
          double minTemperature = 20;
          double minHumidity = 60;
          Random rand = new Random();
    
          while (true) {
            double currentTemperature = minTemperature + rand.nextDouble() * 15;
            double currentHumidity = minHumidity + rand.nextDouble() * 20;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.temperature = currentTemperature;
            telemetryDataPoint.humidity = currentHumidity;
    
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            msg.setProperty("temperatureAlert", (currentTemperature > 30) ? "true" : "false");
            msg.setMessageId(java.util.UUID.randomUUID().toString()); 
            System.out.println("Sending: " + msgStr);
    
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
    
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        }
      }
    }
    ```

    Este método envía un nuevo mensaje de dispositivo a nube un segundo después de que IoT Hub reconozca el mensaje anterior. El mensaje contiene un objeto JSON serializado con el valor de deviceId y un número generado aleatoriamente para simular un sensor de temperatura y otro de humedad.

11. Reemplace el método **main** por el siguiente código, que crea un subproceso para enviar mensajes del dispositivo a la nube a IoT Hub:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();
    
      MessageSender sender = new MessageSender();
    
      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.closeNow();
    }
    ```

12. Guarde y cierre el archivo App.java.

13. Para compilar la aplicación **simulated-device** con Maven, ejecute el siguiente comando en el símbolo del sistema en la carpeta simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling][lnk-transient-faults] (Tratamiento de errores temporales).

## <a name="run-the-apps"></a>Ejecución de las aplicaciones

Ya está preparado para ejecutar las aplicaciones.

1. En un símbolo del sistema, en la carpeta read-d2c, ejecute el siguiente comando para empezar la supervisión de la primera partición de IoT Hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplicación cliente de servicio de IoT Hub de Java para supervisar mensajes del dispositivo a la nube][7]

2. En un símbolo del sistema, en la carpeta simulated-device, ejecute el siguiente comando para empezar el envío de datos de telemetría a IoT Hub:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Aplicación de dispositivo de IoT Hub de Java para enviar mensajes del dispositivo a la nube][8]

3. El icono **Uso** de [Azure Portal][lnk-portal] muestra el número de mensajes enviados a IoT Hub:

    ![Icono Uso de Azure Portal que muestra el número de mensajes enviados a IoT Hub][43]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, configuró un centro de IoT nuevo en Azure Portal y, después, creó una identidad de dispositivo en el registro de identidades del centro de IoT. Usó esta identidad de dispositivo para habilitar la aplicación del dispositivo para enviar a IoT Hub los mensajes del dispositivo a la nube. También creó otra aplicación que muestra los mensajes recibidos por el centro de IoT.

Para continuar la introducción a IoT Hub y explorar otros escenarios de IoT, consulte:

* [Conexión del dispositivo][lnk-connect-device]
* [Introducción a la administración de dispositivos][lnk-device-management]
* [Introducción a Azure IoT Edge][lnk-iot-edge]

Para aprender a ampliar su solución IoT y cómo procesar mensajes de dispositivo a la nube a escala, consulte [Tutorial: procesamiento de mensajes de dispositivo a la nube][lnk-process-d2c-tutorial].
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-eventhubs-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22
