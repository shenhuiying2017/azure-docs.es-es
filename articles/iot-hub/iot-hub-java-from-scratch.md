<properties
	pageTitle="Crear un cliente de Java del Centro de IoT | Microsoft Azure"
	description="Siga este tutorial para que se compile un cliente de Java del Centro de IoT que usa el SDK del dispositivo de IoT de Microsoft Azure para Java* para comunicarse con el Centro de IoT de Azure."
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
     ms.date="11/26/2015"
     ms.author="dobett"/>
     
# Crear una aplicación de cliente del centro de IoT de Azure con Java

En este artículo se describe cómo crear una aplicación cliente que usa el [SDK del dispositivo de IoT de Microsoft Azure para Java][lnk-java-sdk] para comunicarse con el Centro de IoT de Azure. En el tutorial se muestra cómo crear y compilar el proyecto mediante la herramienta [Maven][apache-maven]. Puede seguir estas instrucciones en una máquina Linux o Windows.

Puede ver los [documentos de la API de Java][lnk-java-api-docs] para referencia.

## Instalación

Vea [Preparar el entorno de desarrollo][devbox-setup] para información sobre los requisitos previos y configurar su entorno de desarrollo en Windows o en Linux.

> [AZURE.NOTE]Es importante que complete los pasos de [Preparar el entorno de desarrollo][devbox-setup] antes de empezar este tutorial para instalar los requisitos previos y agregar los archivos JAR necesarios en el repositorio de Maven local.

## Creación del proyecto

1. En la herramienta de línea de comandos, ejecute el siguiente comando para crear un proyecto de Maven nuevo y vacío en una ubicación adecuada de la máquina de desarrollo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=iot-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    > [AZURE.NOTE]Es un comando único y largo. Asegúrese de copiar todo el comando si quiere pegar en la herramienta de línea de comandos.

    Este comando crea una carpeta de proyecto denominada *iot-device* que tiene la estructura de proyecto de Maven estándar. Para más información, vea [Maven en 5 minutos][maven-five-minutes] en el sitio web de Apache.

2. Abra el archivo **pom.xml** en la carpeta iot-device en un editor de texto.

3. Agregue la siguiente sección **dependency** nueva después de la existente para incluir las bibliotecas de cliente necesarias:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-client</artifactId>
      <version>1.0.0-preview.4</version>
    </dependency>
    ```

4. Agregue la siguiente sección **build** después de la sección **dependencies** para que el archivo JAR final incluya las dependencias y que el manifiesto identifique la clase **main**.

    ```
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-shade-plugin</artifactId>
          <executions>
            <execution>
              <phase>package</phase>
              <goals>
                <goal>shade</goal>
              </goals>
            </execution>
          </executions>
          <configuration>
            <finalName>${artifactId}-${version}-with-deps</finalName>
          </configuration>
        </plugin>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-jar-plugin</artifactId>
          <version>2.6</version>
          <configuration>
            <archive>
              <manifest>
                <addClasspath>true</addClasspath>
                <mainClass>com.mycompany.app.App</mainClass>
              </manifest>
            </archive>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

5. Guarde el archivo **pom.xml**.

## Creación de la aplicación

1. Abra el archivo **App.java** de la carpeta iot-device/src/main/java/com/mycompany/app en un editor de texto.

2. Agregue las siguientes instrucciones **import**, que incluyen las bibliotecas de dispositivo IoT, después de la instrucción **package**:

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
    import java.util.Scanner;
    
    import javax.naming.SizeLimitExceededException;
    ```

3. Agregue la siguiente clase **EventCallback** como una clase anidada dentro de la clase **aplicación**. El método **execute** de la clase **EventCallback** se invoca cuando el dispositivo recibe una confirmación del centro de IoT en respuesta a un mensaje de dispositivo a la nube.

    ```
    protected static class EventCallback
        implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        Integer i = (Integer) context;
        System.out.println("IoT Hub responded to message " + i.toString()
            + " with status " + status.name());
      }
    }
    ```

4. Agregue la siguiente clase **MessageCallback** como una clase anidada dentro de la clase **aplicación**. El método **execute** de la clase **MessageCallback** le permite enviar un mensaje con comentarios a su centro de IoT en respuesta a un mensaje de nube a dispositivo recibido por el dispositivo.

    ```
    protected static class MessageCallback
        implements com.microsoft.azure.iothub.MessageCallback
    {
      public IotHubMessageResult execute(Message msg,
          Object context)
      {
        System.out.println(
            "Received message with content: " + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
    
          return IotHubMessageResult.COMPLETE;
      }
    }
    ```

5. Reemplace el método **main** existente por el código siguiente que:

  - Crea una instancia **DeviceClient**.
  - Inicializa la devolución de llamada de mensaje para mensajes de la nube al dispositivo.
  - Abre el **DeviceClient** para habilitarlo para enviar mensajes del dispositivo a la nube y recibir mensajes de la nube al dispositivo.
  - Envía diez mensajes de ejemplo a su centro de IoT.

    Reemplace `<your device connection string>` por una cadena de conexión de dispositivo válida. Puede aprovisionar un dispositivo y recuperar su cadena de conexión mediante la herramienta [DeviceExplorer][lnk-device-explorer] o la herramienta [Explorer del Centro de IoT][lnk-iothub-explorer].

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException
    {
      String connString = "<your device connection string>";
      IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    
      DeviceClient client = new DeviceClient(connString, protocol);
    
      MessageCallback messageCallback = new MessageCallback();
      client.setMessageCallback(messageCallback, null);
    
      client.open();
    
      for (int i = 0; i < 10; ++i)
      {
        String msgStr = "Event Message " + Integer.toString(i);
        try
        {
          Message msg = new Message(msgStr);
          msg.setProperty("messageCount", Integer.toString(i));
          System.out.println(msgStr);
    
          EventCallback eventCallback = new EventCallback();
          client.sendEventAsync(msg, eventCallback, i);
        }
        catch (Exception e)
        {
        }
      }
    
      System.out.println("Press any key to exit...");
      Scanner scanner = new Scanner(System.in);
      scanner.nextLine();
    
      client.close();
    }
    ```

6. Para compilar el código y empaquetarlo en un archivo JAR, ejecute el siguiente comando en un símbolo del sistema en la carpeta del proyecto **iot-device**:

    ```
    mvn package
    ```

7. Para ejecutar la aplicación, ejecute el siguiente comando en un símbolo del sistema en la carpeta del proyecto **iot-device**:

    ```
    java -jar target/iot-device-1.0-SNAPSHOT-with-deps.jar
    ```

8. Puede usar la herramienta [DeviceExplorer][lnk-device-explorer] para supervisar los mensajes del dispositivo a la nube que recibe su centro de IoT y para enviar mensajes de la nube al dispositivo para el dispositivo desde el centro de IoT.

## Cambiar la granularidad del registro

Para cambiar la granularidad del registro, incluya la siguiente línea en el archivo `config.properties`:

```
.level = {LOGGING_LEVEL}
```

> [AZURE.NOTE]Puede leer una explicación de los diferentes [niveles de registro](http://docs.oracle.com/javase/7/docs/api/java/util/logging/Level.html).

Luego, establezca la propiedad de JVM `java.util.logging.config.file={Path to your config.properties file}`.

Para registrar los marcos AMQP, establezca la variable de entorno `PN_TRACE_FRM=1` en su entorno de comandos.


[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-java-api-docs]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[apache-maven]: https://maven.apache.org/index.html
[maven-five-minutes]: https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html
[devbox-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md
[lnk-iothub-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/iothub-explorer/doc/provision_device.md

<!---HONumber=AcomDC_1217_2015-->