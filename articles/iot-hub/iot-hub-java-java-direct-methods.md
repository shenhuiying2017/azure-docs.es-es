---
title: "Uso de métodos directos de Azure IoT Hub (Java) | Microsoft Docs"
description: "Describe cómo usar los métodos directos de IoT Hub de Azure. Usará el SDK de dispositivo IoT de Azure para Java con el fin de implementar una aplicación de dispositivo simulado que incluye un método directo, además del SDK de servicio IoT de Azure para Java con el objetivo de implementar una aplicación de servicio que invoque el método directo."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 4fb759ecd7767c126bc22165494652039ba1caa4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-direct-methods-java"></a>Uso de métodos directos (Java)

[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

En este tutorial, creará dos aplicaciones de consola de Java:

* **invoke-direct-method**, una aplicación de back-end de Java que llama a un método de la aplicación de dispositivo simulado y muestra la respuesta.
* **simulated-device**, una aplicación de Java que simula un dispositivo que se conecta a la instancia de IoT Hub con la identidad de dispositivo que crea. Esta aplicación responde a la invocación directa desde el back-end.

> [!NOTE]
> Para más información acerca de los diversos SDK que puede usar para crear aplicaciones para que se ejecuten en dispositivos y en el back-end de la solución, consulte [SDK de IoT de Azure][lnk-hub-sdks].

Para completar este tutorial, necesita:

* Java SE 8. <br/> [Prepare your development environment][lnk-dev-setup] (Preparación del entorno de desarrollo) describe cómo instalar Java para este tutorial en Windows o Linux.
* Maven 3.  <br/> [Prepare your development environment][lnk-dev-setup] (Preparación del entorno de desarrollo) describe cómo instalar [Maven][lnk-maven] para este tutorial en Windows o Linux.
* [Versión de Node.js 0.10.0 o posterior](http://nodejs.org).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado

En esta sección, creará una aplicación de consola de Java que responda a un método llamado por el back-end de solución.

1. Cree una carpeta vacía llamada iot-java-direct-method.

1. En la carpeta iot-java-direct-method, cree un proyecto de Maven denominado **simulated-device** mediante el comando siguiente en el símbolo del sistema. El siguiente es un comando único y largo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. En el símbolo del sistema, vaya a la nueva carpeta simulated-device.

1. Con un editor de texto, abra el archivo pom.xml de la carpeta simulated-device y agregue las siguientes dependencias al nodo **dependencies** . Esta dependencia le permite usar el paquete iot-device-client en la aplicación para comunicarse con la instancia de IoT Hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Puede comprobar la versión más reciente de **iot-device-client** mediante la [funcionalidad de búsqueda de Maven][lnk-maven-device-search].

1. Agregue el nodo **build** después del nodo **dependencies**. Esta configuración indica a Maven que use Java 1.8 para compilar la aplicación:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. Guarde y cierre el archivo pom.xml.

1. Con un editor de texto, abra el archivo simulated-device\src\main\java\com\mycompany\app\App.java.

1. Agregue las siguientes instrucciones **import** al archivo:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. Agregue las siguientes variables de nivel de clase a la clase **App** . Reemplace `{youriothubname}` por el nombre de la instancia de IoT Hub y `{yourdevicekey}` por el valor de la clave de dispositivo que generó en la sección *Creación de una identidad de dispositivo*:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Esta aplicación de ejemplo usa la variable **protocol** al crear una instancia de un objeto **DeviceClient**. 

1. Agregue la clase anidada siguiente a la clase **App** para devolver un código de estado a la instancia de IoT Hub:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. Agregue la clase anidada siguiente a la clase **App** para controlar las invocaciones de método directo desde el back-end de la solución:

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "writeLine" :
          {
            int status = METHOD_SUCCESS;
            System.out.println(new String((byte[])methodData));
            deviceMethodData = new DeviceMethodData(status, "Executed direct method " + methodName);
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

1. Agregue un método **main** a la clase **App** para crear un **DeviceClient** y escuchar las invocaciones de método directo:

    ```java
    public static void main(String[] args)
      throws IOException, URISyntaxException
    {
      System.out.println("Starting device sample...");

      DeviceClient client = new DeviceClient(connString, protocol);

      try
      {
        client.open();
        client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
        System.out.println("Subscribed to direct methods. Waiting...");
      }
      catch (Exception e)
      {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
        client.close();
        System.out.println("Shutting down...");
      }

      System.out.println("Press any key to exit...");
      Scanner scanner = new Scanner(System.in);
      scanner.nextLine();
      scanner.close();
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Guarde y cierre el archivo simulated-device\src\main\java\com\mycompany\app\App.java

1. Compile la aplicación **simulated-device** y corrija los errores. En el símbolo del sistema, vaya a la carpeta simulated-device y ejecute el comando siguiente:

    `mvn clean package -DskipTests`

## <a name="call-a-direct-method-on-a-device"></a>Llamada a un método directo en un dispositivo

En esta sección, creará una aplicación de consola de Java que invoca a un método directo y luego muestra la respuesta. Esta aplicación de consola se conecta a la instancia de IoT Hub para invocar al método directo.

1. En la carpeta iot-java-direct-method, cree un proyecto de Maven denominado **invoke-direct-method** mediante el comando siguiente en el símbolo del sistema. El siguiente es un comando único y largo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=invoke-direct-method -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. En el símbolo del sistema, vaya a la carpeta invoke-direct-method.

1. Con un editor de texto, abra el archivo pom.xml en la carpeta invoke-direct-method y agregue la dependencia siguiente al nodo **dependencies**. Esta dependencia le permite usar el paquete iot-service-client en la aplicación para comunicarse con la instancia de IoT Hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Puede comprobar la versión más reciente de **iot-service-client** mediante la [funcionalidad de búsqueda de Maven][lnk-maven-service-search].

1. Agregue el nodo **build** después del nodo **dependencies**. Esta configuración indica a Maven que use Java 1.8 para compilar la aplicación:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. Guarde y cierre el archivo pom.xml.

1. Con un editor de texto, abra el archivo invoke-direct-method\src\main\java\com\mycompany\app\App.java.

1. Agregue las siguientes instrucciones **import** al archivo:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    ```

1. Agregue las siguientes variables de nivel de clase a la clase **App** . Reemplace `{youriothubconnectionstring}` por la cadena de conexión de IoT Hub que anotó en la sección *Creación de un IoT Hub*:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String methodName = "writeLine";
    public static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    public static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    public static final String payload = "a line to be written";
    ```

1. Agregue el código siguiente al método **main** para invocar el método en el dispositivo simulado:

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
        System.out.println("Invoke direct method");
        MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, payload);

        if(result == null)
        {
            throw new IOException("Direct method invoke returns null");
        }
        System.out.println("Status=" + result.getStatus());
        System.out.println("Payload=" + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }

    System.out.println("Shutting down sample...");
    ```

1. Guarde y cierre el archivo invoke-direct-method\src\main\java\com\mycompany\app\App.java.

1. Compile la aplicación **invoke-direct-method** y corrija los errores. En el símbolo del sistema, vaya a la carpeta invoke-direct-method y ejecute el comando siguiente:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Ejecución de las aplicaciones

Ya está preparado para ejecutar las aplicaciones de consola.

1. En un símbolo del sistema, en la carpeta simulated-device, ejecute el comando siguiente para empezar a escuchar las llamadas de método desde la instancia de IoT Hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Aplicación de dispositivo simulado IoT Hub de Java para escuchar las llamadas al método directo][8]

1. En un símbolo del sistema en la carpeta invoke-direct-method, ejecute el comando siguiente para llamar a un método en el dispositivo simulado desde la instancia de IoT Hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Aplicación de servicio IoT Hub de Java para llamar a un método directo][7]

1. El dispositivo simulado responde a la llamada al método directo:

    ![La aplicación de dispositivo simulado IoT Hub de Java responde a la llamada al método directo][9]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, configuró un centro de IoT nuevo en Azure Portal y, después, creó una identidad de dispositivo en el registro de identidades del centro de IoT. Usó esta identidad de dispositivo para que la aplicación del dispositivo simulado reaccionara a los métodos que se invoquen desde la nube. También creó una aplicación que invoca métodos en el dispositivo y muestra la respuesta del dispositivo.

Para explorar otros escenarios de IoT, consulte [Programación de trabajos en varios dispositivos][lnk-devguide-jobs].

Para información sobre cómo ampliar la solución de IoT y programar llamadas de método en varios dispositivos, consulte el tutorial [Programación de trabajos en varios dispositivos][lnk-tutorial-jobs].

<!-- Images. -->
[7]: ./media/iot-hub-java-java-direct-methods/invoke-method.png
[8]: ./media/iot-hub-java-java-direct-methods/device-listen.png
[9]: ./media/iot-hub-java-java-direct-methods/device-respond.png

<!-- Links -->
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md
[lnk-maven]: https://maven.apache.org/what-is-maven.html
[lnk-hub-sdks]: iot-hub-devguide-sdks.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
