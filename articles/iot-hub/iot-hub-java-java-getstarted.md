<properties
	pageTitle="Introducción al Centro de IoT de Azure para Java | Microsoft Azure"
	description="Tutorial de introducción al Centro de IoT de Azure con Java Utilice Centro de IoT de Azure y Java con los SDK de IoT de Microsoft Azure para implementar una solución de Internet de las cosas."
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/22/2016"
     ms.author="dobett"/>

# Introducción al Centro de IoT de Azure para Java

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Introducción

El Centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional confiable y segura entre millones de dispositivos de Internet de las cosas (IoT) y un back-end de soluciones. Uno de los mayores desafíos que plantean los proyectos de IoT es cómo conectar dispositivos al back-end de la solución de manera segura y confiable. Para abordar este desafío, el Centro de IoT:

- Ofrece una mensajería confiable de gran escala de dispositivo a nube y de nube a dispositivo.
- Habilita las comunicaciones seguras con las credenciales de seguridad de cada dispositivo y el control de acceso.
- Incluye bibliotecas de dispositivos para las plataformas y los lenguajes más populares.

En este tutorial se muestra cómo realizar las siguientes acciones:

- Usar el Portal de Azure para crear un Centro de IoT.
- Crear una identidad de dispositivo en el Centro de IoT.
- Crear un dispositivo simulado que envía la telemetría al back-end en la nube.

Al final de este tutorial tendrá tres aplicaciones de consola de Java:

* **create-device-identity**, que crea una identidad de dispositivo y una clave de seguridad asociada para conectar el dispositivo simulado.
* **read-d2c-messages**, que muestra los datos de telemetría enviados por el dispositivo simulado.
* **simulated-device**, que se conecta con su Centro de IoT con la identidad del dispositivo creada anteriormente y envía un mensaje de telemetría cada segundo mediante el protocolo AMQPS.

> [AZURE.NOTE] El artículo [SDK de Centro de IoT][lnk-hub-sdks] proporciona información acerca de los SDK que puede usar para crear dos aplicaciones para ejecutarse en dispositivos y en el back-end de la solución.

Para completar este tutorial, necesitará lo siguiente:

+ Java SE 8. <br/> [Prepare your development environment][lnk-dev-setup] (Preparación del entorno de desarrollo) describe cómo instalar Java para este tutorial en Windows o Linux.

+ Maven 3. <br/> [Prepare your development environment][lnk-dev-setup] (Preparación del entorno de desarrollo) describe cómo instalar Maven para este tutorial en Windows o Linux.

+ Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][lnk-free-trial].

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Como paso final, haga clic en **Configuración** en la hoja Centro de IoT y luego en **Mensajes** en la hoja **Configuración**. En la hoja **Mensajes**, anote los valores de **Nombre compatible con Centro de eventos** y **Punto de conexión compatible de Centro de eventos**. Necesitará estos valores al crear la aplicación **read-d2c-messages**.

![][6]

Ahora que ha creado un Centro de IoT y que tiene el nombre de host del Centro de IoT, la cadena de conexión del Centro de IoT, el nombre compatible con el Centro de eventos y el punto de conexión compatible con el Centro de eventos, es preciso que complete el resto del tutorial.

## Creación de una identidad de dispositivo

En esta sección, creará una aplicación de consola de Java que crea una nueva identidad de dispositivo en el registro de identidades de su Centro de IoT. No se puede conectar un dispositivo al Centro de IoT a menos que tenga una entrada en el registro de identidades de dispositivo. Consulte la sección **Registro de identidad de dispositivos** de la [Guía para desarrolladores del Centro de IoT][lnk-devguide-identity] para obtener más información. Al ejecutar esta aplicación de consola, genera y una clave y un identificador de dispositivo únicos con el que el dispositivo puede identificarse cuando envía al Centro de IoT mensajes de dispositivo a la nube.

1. Cree una nueva carpeta vacía denominada iot-java-get-started. En la carpeta iot-java-get-started, cree un nuevo proyecto Maven denominado **create-device-identity** mediante el comando siguiente en el símbolo del sistema. Tenga en cuenta que es un comando único y largo.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. En el símbolo del sistema, navegue a la nueva carpeta create-device-identity.

3. Con un editor de texto, abra el archivo pom.xml en la carpeta create-device-identity y agregue la siguiente dependencia al nodo **dependencies**. Permite que se use el paquete iothub-service-sdk en su aplicación:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.2</version>
    </dependency>
    ```
    
4. Guarde y cierre el archivo pom.xml.

5. Mediante un editor de texto, abra el archivo create-device-identity\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Agregue las siguientes instrucciones **import** al archivo:

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Agregue las siguientes variables de nivel de clase a la clase **App**, reemplazando **{yourhubname}** y **{yourhubkey}** por los valores indicados anteriormente:

    ```
    private static final String connectionString = "HostName={yourhubname}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={yourhubkey}";
    private static final String deviceId = "javadevice";
    
    ```
    
8. Modifique la firma del método **main** para incluir las excepciones que se muestran a continuación:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. Agregue el siguiente código como cuerpo del método **main**. Este código crea un dispositivo denominado *javadevice* en el registro de identidades de Centro de IoT si no existe. Después muestra el identificador de dispositivo y la clave que necesitará más adelante:

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

13. Tome nota del **identificador de dispositivo** y de la **clave de dispositivo**. Los necesitará más adelante cuando cree una aplicación que se conecta a Centro de IoT como un dispositivo.

> [AZURE.NOTE] El registro de identidades del Centro de IoT solo almacena las identidades de dispositivo para permitir el acceso seguro al centro. Almacena las claves y los identificadores de dispositivo para usarlos como credenciales de seguridad, y un indicador de habilitado o deshabilitado que permite deshabilitar el acceso a un dispositivo individual. Si la aplicación necesita almacenar otros metadatos específicos del dispositivo, debe usar un almacén específico de la aplicación. Consulte [Guía para desarrolladores del Centro de IoT][lnk-devguide-identity] para más información.

## Recepción de mensajes de dispositivo a nube

En esta sección, creará una aplicación de consola de Java que lee los mensajes de dispositivo a nube desde el Centro de IoT. El Centro de IoT expone un punto de conexión compatible con [Centros de eventos ][lnk-event-hubs-overview] para poder leer los mensajes de dispositivo a nube. Para simplificar las cosas, este tutorial crea un lector básico que no es apto para una implementación de alta capacidad de procesamiento. El [Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT][lnk-process-d2c-tutorial] muestra cómo procesar mensajes de dispositivo a la nube a escala. En el tutorial [Introducción a los Centros de eventos][lnk-eventhubs-tutorial] se proporciona información adicional acerca de cómo procesar los mensajes desde los Centros de eventos. Dicha información se puede aplicar a los puntos de conexión compatibles con Centros de eventos de Centro de IoT.

> [AZURE.NOTE] El punto de conexión compatible con los Centros de eventos para leer mensajes de dispositivo a la nube siempre usa el protocolo AMQPS.

1. En la carpeta iot-java-get-started creada en la sección *Creación de una identidad de dispositivo*, cree un nuevo proyecto de Maven denominado **read-d2c-messages** mediante el comando siguiente en el símbolo del sistema. Tenga en cuenta que es un comando único y largo.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. En el símbolo del sistema, navegue a la nueva carpeta read-d2c-messages.

3. Con un editor de texto, abra el archivo pom.xml en la carpeta read-d2c-messages y agregue la siguiente dependencia al nodo **dependencies**. Esto permite usar el paquete eventhubs-client en la aplicación para leer desde el punto de conexión compatible con Centros de eventos:

    ```
    <dependency>
      <groupId>com.microsoft.eventhubs.client</groupId>
      <artifactId>eventhubs-client</artifactId>
      <version>1.0</version>
    </dependency>
    ```

4. Guarde y cierre el archivo pom.xml.

5. Con un editor de texto, abra el archivo read-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Agregue las siguientes instrucciones **import** al archivo:

    ```
    import java.io.IOException;
    import com.microsoft.eventhubs.client.Constants;
    import com.microsoft.eventhubs.client.EventHubClient;
    import com.microsoft.eventhubs.client.EventHubEnqueueTimeFilter;
    import com.microsoft.eventhubs.client.EventHubException;
    import com.microsoft.eventhubs.client.EventHubMessage;
    import com.microsoft.eventhubs.client.EventHubReceiver;
    import com.microsoft.eventhubs.client.ConnectionStringBuilder;
    ```

7. Agregue las siguientes variables de nivel de clase a la clase **App**:

    ```
    private static EventHubClient client;
    private static long now = System.currentTimeMillis();
    ```

8. Agregue la siguiente clase anidada dentro de la clase **App**. La aplicación crea dos subprocesos para ejecutar la clase **MessageReceiver** para leer mensajes de las dos particiones del Centro de eventos:

    ```
    private static class MessageReceiver implements Runnable
    {
        public volatile boolean stopThread = false;
        private String partitionId;
    }
    ```

9. Agregue el siguiente constructor a la clase **MessageReceiver**:

    ```
    public MessageReceiver(String partitionId) {
        this.partitionId = partitionId;
    }
    ```

10. Agregue el siguiente método **run** a la clase **MessageReceiver**. Este método crea una instancia de **EventHubReceiver** para leer desde una partición de Centro de eventos. Reproduce en bucle continuo e imprime los detalles del mensaje en la consola de imprime hasta que la clase **stopThread** sea true.

    ```
    public void run() {
      try {
        EventHubReceiver receiver = client.getConsumerGroup(null).createReceiver(partitionId, new EventHubEnqueueTimeFilter(now), Constants.DefaultAmqpCredits);
        System.out.println("** Created receiver on partition " + partitionId);
        while (!stopThread) {
          EventHubMessage message = EventHubMessage.parseAmqpMessage(receiver.receive(5000));
          if(message != null) {
            System.out.println("Received: (" + message.getOffset() + " | "
                + message.getSequence() + " | " + message.getEnqueuedTimestamp()
                + ") => " + message.getDataAsString());
          }
        }
        receiver.close();
      }
      catch(EventHubException e) {
        System.out.println("Exception: " + e.getMessage());
      }
    }
    ```

    > [AZURE.NOTE] Este método utiliza un filtro cuando crea el receptor para que este solo lea los mensajes enviados al Centro de IoT después de que el receptor comience a ejecutarse. Esto es útil en un entorno de prueba, porque puede ver el conjunto actual de mensajes, pero en un entorno de producción el código debe asegurarse de que se procesan todos los mensajes. Consulte el [Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT][lnk-process-d2c-tutorial] para más información.

11. Modifique la firma del método **main** para incluir las excepciones que se muestran a continuación:

    ```
    public static void main( String[] args ) throws IOException
    ```

12. Agregue el siguiente código al método **main** en la clase **App**. Este código crea una instancia de **EventHubClient** para conectarse al punto de conexión compatible del Centro eventos en Centro de IoT. Después, crea dos subprocesos para leer de las dos particiones. Reemplace **{youriothubkey}**, **{youreventhubcompatiblenamespace}** y **{youreventhubcompatiblename}** por los valores que ha anotado antes. El valor del marcador de posición **{youreventhubcompatiblenamespace}** proviene del **punto de conexión compatible con el Centro de eventos** y adopta la forma siguiente: **xxxxnamespace** (dicho de otro modo, quite el prefijo ****sb://** y el sufijo **.servicebus.windows.net** del valor de punto de conexión compatible con el Centro de eventos del portal).

    ```
    String policyName = "iothubowner";
    String policyKey = "{youriothubkey}";
    String namespace = "{youreventhubcompatiblenamespace}";
    String name = "{youreventhubcompatiblename}";
    try {
      ConnectionStringBuilder csb = new ConnectionStringBuilder(policyName, policyKey, namespace);
      client = EventHubClient.create(csb.getConnectionString(), name);
    }
    catch(EventHubException e) {
        System.out.println("Exception: " + e.getMessage());
    }
    
    MessageReceiver mr0 = new MessageReceiver("0");
    MessageReceiver mr1 = new MessageReceiver("1");
    Thread t0 = new Thread(mr0);
    Thread t1 = new Thread(mr1);
    t0.start(); t1.start();

    System.out.println("Press ENTER to exit.");
    System.in.read();
    mr0.stopThread = true;
    mr1.stopThread = true;
    client.close();
    ```

    > [AZURE.NOTE] Este código supone que usted creó el Centro de IoT en el nivel de F1 (gratis). Un Centro de IoT gratis tiene dos particiones denominadas "0" y "1". Si creó el Centro de IoT con otro plan de tarifa, debe ajustar el código para crear una clase **MessageReceiver** para cada partición.

13. Guarde y cierre el archivo App.java.

14. Para compilar la aplicación **read-d2c-messages** con Maven, ejecute el siguiente comando en el símbolo del sistema en la carpeta read-d2c-messages:

    ```
    mvn clean package -DskipTests
    ```

## Creación de una aplicación de dispositivo simulado

En esta sección, creará una aplicación de consola de Java que simula un dispositivo que envía mensajes de dispositivo a nube a un Centro de IoT.

1. En la carpeta iot-java-get-started creada en la sección *Creación de una identidad de dispositivo*, cree un nuevo proyecto de Maven denominado **simulated-device** mediante el comando siguiente en el símbolo del sistema. Tenga en cuenta que es un comando único y largo.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. En el símbolo del sistema, navegue a la nueva carpeta simulated-device.

3. Con un editor de texto, abra el archivo pom.xml en la carpeta simulated-device y agregue las siguientes dependencias al nodo **dependencies**. Esto le permite usar el paquete iothub-java-client en la aplicación para comunicarse con el Centro de IoT y serializar los objetos de Java a JSON:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.2</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. Guarde y cierre el archivo pom.xml.

5. Con un editor de texto, abra el archivo simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Agregue las siguientes instrucciones **import** al archivo:

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.Random;
    import javax.naming.SizeLimitExceededException;
    import com.google.gson.Gson;
    ```

7. Agregue las siguientes variables de nivel de clase a la clase **App**, reemplazando **{youriothubname}** por el nombre del Centro de IoT y **{yourdeviceid}** y **{yourdevicekey}** por los valores del dispositivo generados en la sección *Creación de una identidad de dispositivo*:

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static boolean stopThread = false;
    ```

    Esta aplicación de ejemplo usa la variable **protocol** cuando se crea una instancia de un objeto **DeviceClient**. Puede usar el protocolo HTTPS o AMQPS para comunicarse con el Centro de IoT.

8. Agregue la siguiente clase **TelemetryDataPoint** anidada dentro de la clase **App** para especificar los datos de telemetría que envía el dispositivo al Centro de IoT:

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

9. Agregue la siguiente clase **EventCallback** anidada dentro de la clase **App** para mostrar el estado de confirmación que devuelve el Centro de IoT cuando procesa un mensaje desde el dispositivo simulado. Este método también notifica el subproceso principal de la aplicación cuando se ha procesado el mensaje:

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status " + status.name());
      
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. Agregue la siguiente clase **MessageSender** anidada dentro de la clase **App**. El método **run** de esta clase genera datos de telemetría de ejemplo para enviar al Centro de IoT y espera una confirmación antes de enviar el mensaje siguiente:

    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;

      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          DeviceClient client;
          client = new DeviceClient(connString, protocol);
          client.open();
        
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = "myFirstDevice";
            telemetryDataPoint.windSpeed = currentWindSpeed;
      
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println(msgStr);
        
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
    
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
          client.close();
        } catch (Exception e) {
          e.printStackTrace();
        }
      }
    }
    ```

    Este método envía un nuevo mensaje de dispositivo a nube un segundo después de que Centro de IoT reconozca el mensaje anterior. El mensaje contiene un objeto JSON serializado con el valor de deviceId y un número generado aleatoriamente para simular un sensor de velocidad del viento.

11. Reemplace el método **main** con el siguiente código que crea un subproceso para enviar mensajes de dispositivo a nube a su Centro de IoT:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
    
      MessageSender ms0 = new MessageSender();
      Thread t0 = new Thread(ms0);
      t0.start(); 
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      ms0.stopThread = true;
    }
    ```

12. Guarde y cierre el archivo App.java.

13. Para compilar la aplicación **simulated-device** con Maven, ejecute el siguiente comando en el símbolo del sistema en la carpeta simulated-device:

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Control de errores transitorios][lnk-transient-faults].

## Ejecución de las aplicaciones

Ahora está preparado para ejecutar las aplicaciones.

1. En un símbolo del sistema, en la carpeta read-d2c, ejecute el siguiente comando para empezar la supervisión del Centro de IoT:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![][7]

2. En un símbolo del sistema, en la carpeta simulated-device, ejecute el siguiente comando para empezar el envío de datos de telemetría al Centro de IoT:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![][8]

3. El icono **Uso** del [Portal de Azure][lnk-portal] muestra el número de mensajes enviados al centro:

    ![][43]

## Pasos siguientes

En este tutorial, configuró un nuevo Centro de IoT en el portal y después creó una identidad de dispositivo en el registro de identidades del centro. Esta identidad de dispositivo se usó para habilitar la aplicación del dispositivo simulado para enviar al centro los mensajes de dispositivo a la nube y creó otra aplicación que muestra los mensajes recibidos por el centro. Puede seguir explorando las características del Centro de IoT y otros escenarios en los tutoriales siguientes:

- [Envío de mensajes de nube a dispositivo con el Centro de IoT][lnk-c2d-tutorial] muestra cómo enviar mensajes a dispositivos y procesar los comentarios de entrega generados por el Centro de IoT.
- [Procesamiento de mensajes de dispositivo a la nube][lnk-process-d2c-tutorial] muestra cómo procesar de forma confiable la telemetría y los mensajes interactivos procedentes de los dispositivos.
- [Cómo cargar archivos desde dispositivos a la nube ][lnk-upload-tutorial] describe un patrón que usa mensajes de nube a dispositivo para facilitar la carga de archivos desde los dispositivos.

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0511_2016-->