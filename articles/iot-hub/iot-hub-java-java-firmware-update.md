---
title: "Actualización de firmware de dispositivos con Azure IoT Hub (Java/Java) | Microsoft Docs"
description: "Describe cómo usar la administración de dispositivos en IoT Hub de Azure para iniciar una actualización de firmware del dispositivo. El SDK de dispositivo IoT de Azure para Java se usa para implementar un aplicación de dispositivo simulado y una aplicación de servicio que desencadene la actualización del firmware."
services: iot-hub
documentationcenter: java
author: msebolt
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: v-masebo
ms.openlocfilehash: 024c2e9bf580f97b412a85913ca29d757872556a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-javajava"></a>Use la administración de dispositivos para iniciar una actualización de firmware de los dispositivos (Java/Java)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

En el tutorial de [Introducción a la administración de dispositivos][lnk-dm-getstarted], vimos cómo usar los primitivos de [dispositivo gemelo][lnk-devtwin] y [métodos directos][lnk-c2dmethod] para reiniciar de forma remota un dispositivo. Este tutorial emplea los mismos tipos primitivos de IoT Hub. Además, proporciona orientación y muestra cómo realizar una actualización de firmware simulada de extremo a extremo.  Este patrón se usa en la implementación de actualizaciones de firmware del [ejemplo de implementación de dispositivos Raspberry Pi][lnk-rpi-implementation].

En este tutorial se muestra cómo realizar las siguientes acciones:

* Crear una aplicación de consola de Java que llame al método directo **firmwareUpdate** en la aplicación de dispositivo simulado a través de su instancia de IoT Hub.
* Crear una aplicación de consola de Java que simule el dispositivo e implemente el método directo **firmwareUpdate**. Este método inicia un proceso de varias fases que espera para descargar la imagen de firmware, la descarga y, por último, la aplica. Durante cada fase de la actualización, el dispositivo usa las propiedades notificadas para informar sobre el progreso.

Al final de este tutorial, tendrá dos aplicaciones de consola de Java:

**firmware-update**, llama a un método directo en el dispositivo simulado, muestra la respuesta y muestra periódicamente las actualizaciones de las propiedades notificadas

**simulated-device**, se conecta a la instancia de IoT Hub con la identidad del dispositivo creada anteriormente, recibe la llamada al método directo firmwareUpdate y se ejecuta a través de una simulación de actualización de firmware

Para completar este tutorial, necesitará lo siguiente:

* La versión más reciente de [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Desencadenamiento de una actualización de firmware remota en el dispositivo con un método directo
En esta sección, creará una aplicación de consola de Java que inicia una actualización de firmware remota en un dispositivo. La aplicación usa un método directo para iniciar la actualización y utiliza consultas de dispositivos gemelos para obtener periódicamente el estado de la actualización del firmware activa.

1. Cree una carpeta vacía llamada fw-get-started.

1. En la carpeta fw-get-started, cree un proyecto de Maven denominado **firmware-update** con el comando siguiente en el símbolo del sistema. Observe que este es un comando único y largo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=firmware-update -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. En el símbolo del sistema, navegue hasta la carpeta firmware-update.

1. Con un editor de texto, abra el archivo pom.xml en la carpeta firmware-update y agregue la dependencia siguiente al nodo **dependencies**. Esta dependencia le permite usar el paquete iot-service-client en la aplicación para comunicarse con la instancia de IoT Hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Puede comprobar la versión más reciente de **iot-service-client** mediante la [búsqueda de Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

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

1. Con un editor de texto, abra el archivo de origen firmware-update\src\main\java\com\mycompany\app\App.java.

1. Agregue las siguientes instrucciones **import** al archivo:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    ```

1. Agregue las siguientes variables de nivel de clase a la clase **App** . Reemplace **{youriothubconnectionstring}** por la cadena de conexión de IoT Hub que anotó en la sección *Creación de un IoT Hub*:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "firmwareUpdate";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. Para implementar un método que lee las propiedades notificadas desde el dispositivo gemelo, agregue lo siguiente a la clase **App**:

    ```java
    public static void ShowReportedProperties() 
    {
        try 
        {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          
          Boolean firmwareUpdated = false;
          Integer timeoutCycle = 0;
    
          while (!firmwareUpdated)
          {
            if (timeoutCycle > 5)
            {
              System.out.println("Operation timed out");
              break;
            }
    
            Thread.sleep(1000);
              
            deviceTwins.getTwin(twinDevice);
    
            String reportedProperties = twinDevice.reportedPropertiesToString();
              
            if(reportedProperties.contains("status=waiting"))
            {
              System.out.println("Waiting on device...");
            }
            else if(reportedProperties.contains("status=downloadComplete"))
            {
              System.out.println("Download complete, applying firmware...");
            }
            else if (reportedProperties.contains("status=applyComplete"))
            {
              System.out.println("Firmware applied");
              System.out.println("Get reported properties from device twin");
              System.out.println(twinDevice.reportedPropertiesToString());
              firmwareUpdated = true;
            }
            else
            {
              timeoutCycle++;
            }
          }
        } catch (Exception ex) {
            System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
    }
    ```

1. Modifique la signatura del método **main** para generar las excepciones siguientes:

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. Para invocar al método directo firmwareUpdate en el dispositivo simulado, agregue el código siguiente al método **main**:

    ```java
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      String payload = "https://someurl";
      
      System.out.println("Invoked firmware update on device.");
      System.out.println("Sent URL: " + payload);
      
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, payload);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }

      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
      System.out.println(e.getMessage());
    }
    ```

1. Para sondear las propiedades notificadas desde el dispositivo simulado, agregue el código siguiente al método **main**:

    ```java
    ShowReportedProperties();
    ```

1. Para poder detener la aplicación, agregue el código siguiente al método **main**:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    System.out.println("Shutting down sample...");
    ```

1. Guarde y cierre el archivo firmware-update\src\main\java\com\mycompany\app\App.java.

1. Compile la aplicación de back-end **firmware-update** y corrija los errores. En el símbolo del sistema, navegue hasta la carpeta firmware-update y ejecute el comando siguiente:

    `mvn clean package -DskipTests`

## <a name="simulate-a-device-to-handle-direct-method-calls"></a>Simule un dispositivo para controlar las llamadas al método directo
En esta sección se crea una aplicación de dispositivo simulado de consola de Java que pueda recibir el método directo firmwareUpdate. Luego la aplicación se ejecuta en un proceso de varios estados que simular la actualización de firmware y usa reportedProperties para comunicar el estado.

1. En la carpeta fw-get-started, cree un proyecto Maven denominado **simulated-device** con el comando siguiente en el símbolo del sistema. Observe que este es un comando único y largo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. En el símbolo del sistema, vaya a la nueva carpeta simulated-device.

1. Con un editor de texto, abra el archivo pom.xml en la carpeta firmware-update y agregue la dependencia siguiente al nodo **dependencies**. Esta dependencia le permite usar el paquete iot-service-client en la aplicación para comunicarse con la instancia de IoT Hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Puede comprobar la versión más reciente de **iot-device-client** mediante la [búsqueda de Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

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

1. Con un editor de texto, abra el archivo de origen simulated-device\src\main\java\com\mycompany\app\App.java.

1. Agregue las siguientes instrucciones **import** al archivo:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    import java.util.HashMap;
    ```

1. Agregue las siguientes variables de nivel de clase a la clase **App** . Reemplace **{yourdeviceconnectionstring}** por la cadena de conexión de dispositivo que anotó en la sección *Creación de una identidad de dispositivo*:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring";
    private static DeviceClient client;

    private static String downloadURL = "unknown";
    ```

1. Para implementar funcionalidad del método directo, proporcione las devoluciones de llamada, para lo que debe agregar las siguientes clases anidadas a la clase **App**:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "firmwareUpdate" :
          {
            System.out.println("Response to method '" + methodName + "' sent successfully");
    
            downloadURL = new String((byte[])methodData);
    
            int status = METHOD_SUCCESS;
            System.out.println("Starting firmware update");
            deviceMethodData = new DeviceMethodData(status, "Started firmware update");
            FirmwareUpdateThread firmwareUpdateThread = new FirmwareUpdateThread();
            Thread t = new Thread(firmwareUpdateThread);
            t.start();
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

1. Para implementar la funcionalidad de dispositivo gemelo, proporcione las devoluciones de llamada, para lo que debe agregar las siguientes clases anidadas a la clase **App**:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

1. Para implementar la actualización del firmware, agregue la clase anidada siguiente a la clase **App**:

    ```java
    protected static class FirmwareUpdateThread implements Runnable {
      public void run() {
        try {      
          HashMap initialUpdate = new HashMap();
          Property sentProperty = new Property("firmwareUpdate", initialUpdate);
          Set<Property> sentPackage = new HashSet<Property>();
          
          System.out.println("Downloading from " + downloadURL);
    
          initialUpdate.put("status", "waiting");
          initialUpdate.put("fwPackageUri", downloadURL);
          initialUpdate.put("startedWaitingTime", LocalDateTime.now().toString());   
          sentPackage.add(sentProperty);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Download complete");
    
          HashMap downloadUpdate = new HashMap();
          downloadUpdate.put("status","downloadComplete");
          downloadUpdate.put("downloadCompleteTime", LocalDateTime.now().toString());
          downloadUpdate.put("startedApplyingImage", LocalDateTime.now().toString());
          sentProperty.setValue(downloadUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Apply complete");
    
          HashMap applyUpdate = new HashMap();
          applyUpdate.put("status","applyComplete");
          applyUpdate.put("lastFirmwareUpdate", LocalDateTime.now().toString());
          sentProperty.setValue(applyUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          HashMap resetUpdate = new HashMap();
          applyUpdate.put("status","reset");
          sentProperty.setValue(resetUpdate);
    
          client.sendReportedProperties(sentPackage);
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

1. Modifique la signatura del método **main** para generar las excepciones siguientes:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Para iniciar la rutina de métodos directos y dispositivos gemelos, agregue el código siguiente al método **main**:

    ```java
    client = new DeviceClient(connString, protocol);

    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Para poder detener la aplicación, agregue el código siguiente al final del método **main**:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. Guarde y cierre el archivo simulated-device\src\main\java\com\mycompany\app\App.java.

1. Compile la aplicación **simulated-device** y corrija los errores. En el símbolo del sistema, vaya a la carpeta simulated-device y ejecute el comando siguiente:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Ejecución de las aplicaciones
Ya está preparado para ejecutar las aplicaciones.

1. En un símbolo del sistema, en la carpeta **simulated-device**, ejecute el siguiente comando para empezar a escuchar el método de actualización directa del firmware.
   
    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. En un símbolo del sistema, en la carpeta **firmware-update**, ejecute el siguiente comando invocar la actualización del firmware y consultar los dispositivos gemelos en el dispositivo simulado desde la instancia de IoT Hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

3. La respuesta del dispositivo simulado al método directo se puede ver en la consola.

    ![Firmware actualizado correctamente][img-fwupdate]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha usado un método directo para desencadenar una actualización de firmware remota en un dispositivo y ha utilizado las propiedades notificadas para entender el progreso del proceso de actualización del firmware.

Para información sobre cómo ampliar la solución de IoT y programar llamadas de método en varios dispositivos, consulte el tutorial [Programación de trabajos en varios dispositivos][lnk-tutorial-jobs].

<!-- images -->
[img-fwupdate]: media/iot-hub-java-java-firmware-update/firmwareUpdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-java-java-device-management-getstarted.md
[lnk-tutorial-jobs]: iot-hub-java-java-schedule-jobs.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device