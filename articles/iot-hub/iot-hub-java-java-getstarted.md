---
title: Introducción al Centro de IoT de Azure para Java | Microsoft Docs
description: Tutorial de introducción al Centro de IoT de Azure con Java Utilice Centro de IoT de Azure y Java con los SDK de IoT de Microsoft Azure para implementar una solución de Internet de las cosas.
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2016
ms.author: dobett

---
# <a name="get-started-with-azure-iot-hub-for-java"></a>Introducción al Centro de IoT de Azure para Java
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Al final de este tutorial tendrá tres aplicaciones de consola de Java:

* **create-device-identity**, que crea una identidad de dispositivo y una clave de seguridad asociada para conectar el dispositivo simulado.
* **read-d2c-messages**, que muestra los datos de telemetría enviados por el dispositivo simulado.
* **simulated-device**, que se conecta con su Centro de IoT con la identidad del dispositivo creada anteriormente y envía un mensaje de telemetría cada segundo mediante el protocolo AMQPS.

> [!NOTE]
> El artículo [IoT Hub SDKs][lnk-hub-sdks] (SDK de IoT Hub) proporciona información acerca de los SDK que se pueden usar para crear tanto las aplicaciones que se ejecuten en los dispositivos como el back-end de la solución.
> 
> 

Para completar este tutorial, necesitará lo siguiente:

* Java SE 8. <br/> [Prepare your development environment][lnk-dev-setup] (Preparación de un entorno de desarrollo) describe cómo instalar Java para este tutorial en Windows o Linux.
* Maven 3.  <br/> [Prepare your development environment][lnk-dev-setup] (Preparación de un entorno de desarrollo) describe cómo instalar Maven para este tutorial en Windows o Linux.
* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte [Cree su cuenta gratuita de Azure hoy mismo][lnk-free-trial].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

El último paso es tomar nota del valor de **Clave principal** y, después, hacer clic en **Mensajes**. En la hoja **Mensajes**, anote los valores de **Nombre compatible con el Centro de eventos** y **Extremo compatible con Centro de eventos**. Necesita estos tres valores al crear la aplicación **read-d2c-messages** .

![Hoja de mensajería de IoT Hub de Azure Portal][6]

Ahora que ha creado un Centro de IoT y que tiene el nombre de host del Centro de IoT, la cadena de conexión del Centro de IoT, la clave principal del Centro de IoT, el nombre compatible con Centros de eventos y el punto de conexión compatible con Centros de eventos, es necesario que complete este tutorial.

## <a name="create-a-device-identity"></a>Creación de una identidad de dispositivo
En esta sección, creará una aplicación de consola de Java que crea una nueva identidad de dispositivo en el registro de identidades de su Centro de IoT. No se puede conectar un dispositivo al Centro de IoT a menos que tenga una entrada en el registro de identidades de dispositivo. Para más información, consulte la sección **Device identity registry operations** (Operaciones del Registro de identidad del dispositivo) de [Manage device identities in IoT Hub](Administración de identidades de dispositivos en IoT Hub.md)[lnk-devguide-identity]. Cuando ejecuta esta aplicación de consola, se genera una clave y un identificador de dispositivo únicos con el que el dispositivo puede identificarse cuando envía al Centro de IoT mensajes de dispositivo a la nube.

1. Cree una nueva carpeta vacía denominada iot-java-get-started. En la carpeta iot-java-get-started, cree un nuevo proyecto Maven denominado **create-device-identity** mediante el comando siguiente en el símbolo del sistema. Observe que este es un comando único y largo:
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. En el símbolo del sistema, navegue a la nueva carpeta create-device-identity.
3. Con un editor de texto, abra el archivo pom.xml en la carpeta create-device-identity y agregue la siguiente dependencia al nodo **dependencies** . Permite que se use el paquete iothub-service-sdk en su aplicación:
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.9</version>
    </dependency>
    ```
4. Guarde y cierre el archivo pom.xml.
5. Mediante un editor de texto, abra el archivo create-device-identity\src\main\java\com\mycompany\app\App.java.
6. Agregue las siguientes instrucciones **import** al archivo:
   
    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;
   
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```
7. Agregue las siguientes variables de nivel de clase a la clase **App** y sustituya **{yourhubconnectionstring}** por el valor anotado anteriormente:
   
    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
   
    ```
8. Modifique la firma del método **main** para incluir las excepciones de la siguiente manera:
   
    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
9. Agregue el siguiente código como cuerpo del método **main** . Este código crea un dispositivo denominado *javadevice* en el registro de identidades de Centro de IoT si no existe. Después muestra el identificador de dispositivo y la clave que necesitará más adelante:
   
    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);
   
    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```
10. Guarde y cierre el archivo App.java.
11. Para compilar la aplicación **create-device-identity** con Maven, ejecute el siguiente comando en el símbolo del sistema en la carpeta create-device-identity:
    
    ```
    mvn clean package -DskipTests
    ```
12. Para ejecutar la aplicación **create-device-identity** con Maven, ejecute el siguiente comando en el símbolo del sistema en la carpeta create-device-identity:
    
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
13. Anote el **identificador del dispositivo** y la **clave del dispositivo**. Los necesitará más adelante cuando cree una aplicación que se conecta a Centro de IoT como un dispositivo.

> [!NOTE]
> El registro de identidades del Centro de IoT solo almacena las identidades de dispositivo para permitir el acceso seguro al centro. Almacena las claves y los identificadores de dispositivo para usarlos como credenciales de seguridad, y un indicador de habilitado o deshabilitado que permite deshabilitar el acceso a un dispositivo individual. Si la aplicación necesita almacenar otros metadatos específicos del dispositivo, debe usar un almacén específico de la aplicación. Para más información, consulte [Manage device identities in IoT Hub][lnk-devguide-identity] (Administración de identidades de dispositivos en IoT Hub).
> 
> 

## <a name="receive-device-to-cloud-messages"></a>Recepción de mensajes de dispositivo a nube
En esta sección, creará una aplicación de consola de Java que lee los mensajes de dispositivo a nube desde un IoT Hub. El IoT Hub expone un punto de conexión compatible con [Centro de eventos][lnk-event-hubs-overview]que permite leer los mensajes del dispositivo a la nube. Para simplificar las cosas, este tutorial crea un lector básico que no es apto para una implementación de alta capacidad de procesamiento. En [Tutorial: procesamiento de mensajes de dispositivo a la nube de IoT Hub mediante .Net][lnk-process-d2c-tutorial], se muestra cómo procesar mensajes de dispositivo a la nube a escala. En el tutorial [Introducción a Event Hubs][lnk-eventhubs-tutorial] se proporciona más información acerca de cómo procesar los mensajes desde Event Hubs. Dicha información se puede aplicar a los puntos de conexión compatibles con Event Hubs de IoT Hub.

> [!NOTE]
> El punto de conexión compatible con los Centros de eventos para leer mensajes de dispositivo a la nube siempre usa el protocolo AMQPS.
> 
> 

1. En la carpeta iot-java-get-started creada en la sección *Creación de una identidad de dispositivo* , cree un nuevo proyecto de Maven denominado **read-d2c-messages** mediante el comando siguiente en el símbolo del sistema. Observe que este es un comando único y largo:
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. En el símbolo del sistema, navegue a la nueva carpeta read-d2c-messages.
3. Con un editor de texto, abra el archivo pom.xml en la carpeta read-d2c-messages y agregue la siguiente dependencia al nodo **dependencies** . Esto permite usar el paquete eventhubs-client en la aplicación para leer desde el punto de conexión compatible con Centros de eventos:
   
    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.8.2</version> 
    </dependency>
    ```
4. Guarde y cierre el archivo pom.xml.
5. Con un editor de texto, abra el archivo read-d2c-messages\src\main\java\com\mycompany\app\App.java.
6. Agregue las siguientes instrucciones **import** al archivo:
   
    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
   
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```
7. Agregue las siguientes variables de nivel de clase a la clase **App** . Reemplace **{youriothubkey}**, **{youreventhubcompatiblenamespace}** y **{youreventhubcompatiblename}** por los valores anotados anteriormente:
   
    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```
8. Agregue el siguiente método **receiveMessages** a la clase **App**. Dicho método crea una instancia de **EventHubClient** para conectarse al punto de conexión compatible con Event Hubs y, luego, crea de forma asincrónica una instancia de **PartitionReceiver** que se lee desde una partición del Centro de eventos. Se repite continuamente e imprime los detalles de los mensajes hasta que finaliza la aplicación.
   
    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive(100).get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```
   
   > [!NOTE]
   > Este método utiliza un filtro cuando crea el receptor para que este solo lea los mensajes enviados al Centro de IoT después de que el receptor comience a ejecutarse. Esto es útil en un entorno de prueba, porque puede ver el conjunto actual de mensajes. En un entorno de producción, el código debe asegurarse de que se procesan todos los mensajes (para más información, consulte [Tutorial: procesamiento de mensajes de dispositivo a la nube de IoT Hub][lnk-process-d2c-tutorial]).
   > 
   > 
9. Modifique la firma del método **main** para incluir la excepción de la siguiente manera:
   
    ```
    public static void main( String[] args ) throws IOException
    ```
10. Agregue el siguiente código al método **main** de la clase **App**. Este código crea dos instancias de **EventHubClient** y **PartitionReceiver**, lo que le permite cerrar la aplicación cuando haya terminado de procesar los mensajes:
    
    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```
    
    > [!NOTE]
    > Este código supone que usted creó el Centro de IoT en el nivel de F1 (gratis). Un Centro de IoT gratis tiene dos particiones denominadas "0" y "1".
    > 
    > 
11. Guarde y cierre el archivo App.java.
12. Para compilar la aplicación **read-d2c-messages** con Maven, ejecute el siguiente comando en el símbolo del sistema en la carpeta read-d2c-messages:
    
    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado
En esta sección, creará una aplicación de consola de Java que simula un dispositivo que envía mensajes de dispositivo a nube a un Centro de IoT.

1. En la carpeta iot-java-get-started que ha creado en la sección *Creación de una identidad de dispositivo* , cree un nuevo proyecto de Maven denominado **simulated-device** mediante el comando siguiente en el símbolo del sistema. Observe que este es un comando único y largo:
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. En el símbolo del sistema, navegue a la nueva carpeta simulated-device.
3. Con un editor de texto, abra el archivo pom.xml de la carpeta simulated-device y agregue las siguientes dependencias al nodo **dependencies** . Esto le permite usar el paquete iothub-java-client en la aplicación para comunicarse con el Centro de IoT y serializar los objetos de Java a JSON:
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.14</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```
4. Guarde y cierre el archivo pom.xml.
5. Con un editor de texto, abra el archivo simulated-device\src\main\java\com\mycompany\app\App.java.
6. Agregue las siguientes instrucciones **import** al archivo:
   
    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import com.google.gson.Gson;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```
7. Agregue las siguientes variables de nivel de clase a la clase **App** y reemplace **{youriothubname}** por el nombre del IoT Hub y **{yourdevicekey}** por el valor de la clave de dispositivo que generó en la sección *Creación de una identidad de dispositivo*:
   
    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```
   
    Esta aplicación de ejemplo usa la variable **protocol** al crear una instancia de un objeto **DeviceClient**. Puede usar el protocolo HTTPS o AMQPS para comunicarse con el Centro de IoT.
8. Agregue la siguiente clase **TelemetryDataPoint** anidada dentro de la clase **App** para especificar los datos de telemetría que el dispositivo envía a IoT Hub:
   
    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
   
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```
9. Agregue la siguiente clase **EventCallback** anidada dentro de la clase **App** para mostrar el estado de confirmación que devuelve IoT Hub cuando procesa un mensaje del dispositivo simulado. Este método también notifica el subproceso principal de la aplicación cuando se ha procesado el mensaje:
   
    ```
    private static class EventCallback implements IotHubEventCallback
    {
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
10. Agregue la siguiente clase **MessageSender** anidada dentro de la clase **App**. El método **run** de esta clase genera datos de telemetría de ejemplo que se envían al Centro de IoT y espera confirmación antes de enviar el siguiente mensaje:
    
    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;
    
      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
    
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.windSpeed = currentWindSpeed;
    
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
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
    
    Este método envía un nuevo mensaje de dispositivo a nube un segundo después de que Centro de IoT reconozca el mensaje anterior. El mensaje contiene un objeto JSON serializado con el valor de deviceId y un número generado aleatoriamente para simular un sensor de velocidad del viento.
11. Reemplace el método **main** por el siguiente código, que crea un subproceso para enviar mensajes del dispositivo a la nube al Centro de IoT:
    
    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();
    
      MessageSender sender = new MessageSender();
    
      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.close();
    }
    ```
12. Guarde y cierre el archivo App.java.
13. Para compilar la aplicación **simulated-device** con Maven, ejecute el siguiente comando en el símbolo del sistema en la carpeta simulated-device:
    
    ```
    mvn clean package -DskipTests
    ```

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, debe implementar directivas de reintentos (por ejemplo, retroceso exponencial), como se sugiere en el artículo de MSDN [Transient Fault Handling][lnk-transient-faults](Control de errores transitorios).
> 
> 

## <a name="run-the-applications"></a>Ejecución de las aplicaciones
Ahora está preparado para ejecutar las aplicaciones.

1. En un símbolo del sistema, en la carpeta read-d2c, ejecute el siguiente comando para empezar la supervisión de la primera partición del Centro de IoT:
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
   
    ![Aplicación cliente de servicio de IoT Hub de Java para supervisar mensajes de dispositivo a nube][7]
2. En un símbolo del sistema, en la carpeta simulated-device, ejecute el siguiente comando para empezar el envío de datos de telemetría al Centro de IoT:
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```
   
    ![Aplicación cliente de dispositivo de IoT Hub de Java para enviar mensajes de dispositivo a nube][8]
3. El icono **Uso** de [Azure Portal][lnk-portal] muestra el número de mensajes enviados al centro:
   
    ![Icono Uso de Azure Portal que muestra el número de mensajes enviados a IoT Hub][43]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, configuró un nuevo Centro de IoT en el portal y después creó una identidad de dispositivo en el registro de identidades del centro. Usó esta identidad de dispositivo para habilitar la aplicación del dispositivo simulado para enviar al centro los mensajes de dispositivo a la nube. También creó otra aplicación que muestra los mensajes recibidos por el centro. 

Para continuar la introducción al Centro de IoT y explorar otros escenarios IoT, consulte:

* [Conectar el dispositivo][lnk-connect-device]
* [Introducción a la administración de dispositivos de Centro de IoT de Azure con C# (versión preliminar)][lnk-device-management]
* [SDK de puerta de enlace de IoT (beta): introducción usando Linux][lnk-gateway-SDK]

Para aprender a ampliar una solución IoT y a procesar los mensajes de dispositivo a la nube a escala, consulte [Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT][lnk-process-d2c-tutorial].

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

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/


<!--HONumber=Oct16_HO2-->


