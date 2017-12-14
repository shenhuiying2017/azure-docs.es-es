---
title: Uso de las propiedades de dispositivos gemelos de IoT Hub de Azure (Java) | Microsoft Docs
description: "En este artículo se describe cómo usar los dispositivos gemelos de IoT Hub de Azure para configurar dispositivos. Se usa el SDK de dispositivo IoT de Azure para Java con el fin de implementar una aplicación de dispositivo simulado, además del SDK del servicio IoT de Azure para Java para implementar una aplicación de servicio que modifica una configuración de dispositivo que emplea un dispositivo gemelo."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: dobett
ms.openlocfilehash: 72b57a2495d1a03a57923fb171ee17c0f870ddc7
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>Uso de las propiedades deseadas para configurar dispositivos

[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

Al final de este tutorial, tendrá dos aplicaciones de consola de Java:

* **simulated-device**, una aplicación de dispositivo simulado que espera una actualización de la configuración deseada y notifica el estado de un proceso de actualización de configuración simulada.
* **set-configuration**, una aplicación de back-end que establece la configuración deseada en un dispositivo y consulta el proceso de actualización de la configuración.

> [!NOTE]
> En el artículo [SDK de IoT de Azure][lnk-hub-sdks] se proporciona información sobre los SDK que puede usar para crear aplicaciones de dispositivo y de back-end.
> 
> 

Para completar este tutorial, necesitará lo siguiente:

* La versión más reciente de [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

Si ha seguido el tutorial [Introducción a los dispositivos gemelos][lnk-twin-tutorial], ya tiene una instancia de IoT Hub y una identidad de dispositivo denominada **myDeviceId**. En ese caso, puede ir directamente a la sección [Creación de la aplicación de dispositivo simulado][lnk-how-to-configure-createapp].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>Creación de la aplicación de dispositivo simulado
En esta sección, creará una aplicación de consola de Java que se conecta a su centro como **myDeviceId**, espera una actualización de la configuración deseada e informa de las actualizaciones en el proceso de actualización de la configuración simulada.

1. Cree una carpeta vacía llamada dt-get-started.

1. En la carpeta dt-get-started, cree un proyecto Maven denominado **simulated-device** con el comando siguiente en el símbolo del sistema. Observe que este es un comando único y largo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. En el símbolo del sistema, vaya a la nueva carpeta simulated-device.

1. Con un editor de texto, abra el archivo pom.xml en la carpeta simulated-device y agregue la dependencia siguiente al nodo **dependencies**. Esta dependencia le permite usar el paquete iot-service-client en la aplicación para comunicarse con la instancia de IoT Hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.30</version>
    </dependency>
    ```

    > [!NOTE]
    > Puede comprobar la versión más reciente de **iot-device-client** mediante la [funcionalidad de búsqueda de Maven] [lnk-maven-device-search].

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
    import java.util.Scanner;
    ```

1. Agregue las siguientes variables de nivel de clase a la clase **App** . Reemplace **{yourdeviceconnectionstring}** por la cadena de conexión de dispositivo que anotó en la sección *Creación de una identidad de dispositivo*:

    ```java
    private static String deviceId = "myDeviceId";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    private static TelemetryConfig telemetryConfig;
    ```

1. Para implementar un controlador de devolución de llamada para los eventos de estado de dispositivo gemelo [con fines de depuración], agregue la clase anidada siguiente a la clase **App**:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback 
    {
        public void execute(IotHubStatusCode status, Object context) 
        {
            //System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. Agregue la siguiente clase anidada a la clase **App** :

    ```java
    private static class TelemetryConfig extends Device 
    {
        private String configId = "0000";
        private String sendFrequency = "45m";

        private Boolean initialRun = true;
        
        private Property telemetryConfig = new Property("telemetryConfig", "{configId=" + configId + ", sendFrequency=" + sendFrequency + "}");

        public void InitTelemetry() throws IOException 
        {
            System.out.println("Report initial telemetry config:");
            System.out.println(this.telemetryConfig);

            this.setReportedProp(this.telemetryConfig);

            client.sendReportedProperties(this.getReportedProp());
        }

        private void UpdateReportedConfiguration() 
        {
            try {
                System.out.println("Initiating config change");
                    
                this.setReportedProp(this.telemetryConfig);
                client.sendReportedProperties(this.getReportedProp());
                    
                System.out.println("Simulating device reset");
                
                Thread.sleep(10000);
                
                System.out.println("Completing config change");
                System.out.println("Config change complete");
            } catch (Exception e) {
                System.out.println("Exception \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
            }
        }

        @Override
        public void PropertyCall(String propertyKey, Object propertyValue, Object context) 
        {
            if (propertyKey.equals("telemetryConfig")) {
                if (!(initialRun)) {
                    System.out.println("Desired property change:");
                    System.out.println(propertyKey + ":" + propertyValue);

                    telemetryConfig.setValue(propertyValue);

                    UpdateReportedConfiguration();
                } else {
                    initialRun = false;
                }
            } 
        }
    }
    ```

    > [!NOTE]
    > La clase TelemetryConfig amplía la clase [Device][lnk-java-device-class] para obtener acceso a las devoluciones de llamada de propiedades deseadas.

1. Modifique la signatura del método **main** para generar las excepciones siguientes:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Agregue el código siguiente al método **main** para crear instancias de **DeviceClient** y **TelemetryConfig**:

    ```java
    client = new DeviceClient(connString, protocol);

    telemetryConfig = new TelemetryConfig();
    ```

1. Agregue el código siguiente al método **main** para iniciar los servicios del dispositivo gemelo:

    ```java
    try 
    {
        System.out.println("Connecting to hub");
        client.open();
        client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, telemetryConfig, null);

        telemetryConfig.InitTelemetry();

        System.out.println("Wait for desired telemetry...");
        client.subscribeToDesiredProperties(telemetryConfig.getDesiredProp());
    }
    catch (Exception e) 
    {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
        telemetryConfig.clean();
        client.close();
        System.out.println("Shutting down...");
    }
    ```

1. Agregue el código siguiente al método **main** para apagar el simulador de dispositivos cuando sea necesario:

    ```java
    System.out.println("Press enter to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    telemetryConfig.clean();
    client.close();
    ```

1. Guarde y cierre el archivo simulated-device\src\main\java\com\mycompany\app\App.java.

1. Compile la aplicación back-end **simulated-device** y corrija los errores. En el símbolo del sistema, vaya a la carpeta simulated-device y ejecute el comando siguiente:

    `mvn clean package -DskipTests`

   > [!NOTE]
   > Este tutorial no simula ningún comportamiento de las actualizaciones de configuración simultáneas. Algunos procesos de actualización de configuración pueden dar cabida a cambios de configuración de destino mientras se está ejecutando la actualización, otros tiene que ponerlos en cola, y otros podría rechazarlos con una condición de error. Asegúrese de que considera el comportamiento deseado para el proceso de configuración específica y agregue la lógica adecuada antes de iniciar el cambio de configuración.
   > 
   > 

## <a name="create-the-service-app"></a>Creación de la aplicación de servicio
En esta sección, creará una aplicación de consola de Java que actualiza las *propiedades deseadas* en el dispositivo gemelo asociado con **myDeviceId** con un nuevo objeto de configuración de telemetría. A continuación, consulta los dispositivos gemelos almacenados en IoT Hub y muestra la diferencia entre las configuraciones deseada y notificada del dispositivo.

1. En la carpeta dt-get-started, cree un proyecto Maven denominado **set-configuration** con el comando siguiente en el símbolo del sistema. Observe que este es un comando único y largo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=set-configuration -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. En el símbolo del sistema, vaya a la nueva carpeta set-configuration.

1. Con un editor de texto, abra el archivo pom.xml en la carpeta trigger-reboot y agregue la dependencia siguiente al nodo **dependencies**. Esta dependencia le permite usar el paquete iot-service-client en la aplicación para comunicarse con la instancia de IoT Hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Puede comprobar la versión más reciente de **iot-service-client** mediante la [funcionalidad de búsqueda de Maven] [lnk-maven-service-search].

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

1. Con un editor de texto, abra el archivo de origen set-configuration\src\main\java\com\mycompany\app\App.java.

1. Agregue las siguientes instrucciones **import** al archivo:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

1. Agregue las siguientes variables de nivel de clase a la clase **App** . Reemplace **{youriothubconnectionstring}** por la cadena de conexión de IoT Hub que anotó en la sección *Creación de un IoT Hub*:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";
    ```

1. Para consultar y actualizar los dispositivos gemelos en el dispositivo simulado, agregue el código siguiente al método **main**:

    ```java
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);

    String sendFrequency= "12h";

    try {
        twinClient.getTwin(device);

        // make sure a differing value exists before calling the updateTwin() method, else a null device exception will be thrown
        // this allows the program to be run multiple times for demonstration purposes
        String desiredProperties = device.desiredPropertiesToString();
        if (desiredProperties.contains("sendFrequency=" + sendFrequency))
        {
            sendFrequency = "8h";
        }
            
        Set<Pair> tags = new HashSet<Pair>();
        tags.add(new Pair("telemetryConfig", "{configId=0001, sendFrequency=" + sendFrequency + "}"));

        twinClient.getTwin(device);
        device.setDesiredProperties(tags);

        System.out.println("Config report for: " + deviceId);   
        System.out.println(device);

        twinClient.updateTwin(device);

        String reportedProperties = device.reportedPropertiesToString();
        Boolean waitFlag = true;

        while (waitFlag) {
            if (!reportedProperties.contains("sendFrequency=" + sendFrequency)) {
                Thread.sleep(10000);
            }
            else 
            {
                waitFlag = false;
            }

            twinClient.getTwin(device);
            reportedProperties = device.reportedPropertiesToString();
        }
            
        SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "properties.reported.telemetryConfig='{configId=0001, sendFrequency=" + sendFrequency + "}'", null);

        Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
        while (twinClient.hasNextDeviceTwin(twinQuery)) {
            DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
            System.out.println(d.getDeviceId() + " found with changed telemetryConfig");
        }

        System.out.println("Config report for: " + deviceId);
        twinClient.getTwin(device);
        System.out.println(device);

        } catch (IotHubException e) {
            System.out.println(e.getMessage());
        } catch (IOException e) {
            System.out.println(e.getMessage());
        } catch (InterruptedException e) {
            System.out.println(e.getMessage());
        }
    ```

1. Guarde y cierre el archivo set-desired-configuration\src\main\java\com\mycompany\app\App.java.

1. Compile la aplicación de back-end **set-configuration** y corrija los errores. En el símbolo del sistema, vaya a la carpeta set-configuration y ejecute el comando siguiente:

    `mvn clean package -DskipTests`
   
    Este código recupera el dispositivo gemelo de **myDeviceId** y actualiza sus propiedades deseadas con un nuevo objeto de configuración de telemetría.
    Después, cada 10 segundos, consulta los dispositivos gemelos almacenados en IoT Hub e imprime las configuraciones de telemetría deseadas y notificadas. Consulte el [lenguaje de consulta de IoT Hub][lnk-query] para obtener información sobre cómo generar informes completos en todos los dispositivos.
   
   > [!IMPORTANT]
   > Esta aplicación consulta IoT Hub cada 10 segundos con fines ilustrativos hasta que el dispositivo se haya actualizado. Use consultas para generar informes de cara al usuario en muchos dispositivos y no para detectar cambios. Si la solución requiere notificaciones en tiempo real de eventos de dispositivo, use [notificaciones gemelas][lnk-twin-notifications].
   > 

   > [!IMPORTANT]
   > Hay un retraso de hasta un minuto entre la operación de informe de dispositivo y el resultado de la consulta. Esto es para habilitar la infraestructura de consulta para que funcione a gran escala.  Para recuperar vistas coherentes de un único dispositivo gemelo, use el método **getDeviceTwin** en la clase **DeviceTwin**.
   > 
   > 

## <a name="run-the-apps"></a>Ejecución de las aplicaciones

Ya está preparado para ejecutar las aplicaciones.

1. En un símbolo del sistema, en la carpeta simulated-device, ejecute el comando siguiente para empezar a escuchar las llamadas de dispositivo gemelo desde la instancia de IoT Hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. En un símbolo del sistema en la carpeta set-configuration, ejecute el comando siguiente para consultar y actualizar los dispositivos gemelos en el dispositivo simulado desde la instancia de IoT Hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. El dispositivo simulado responde a la llamada al método directo de reinicio:

    ![La aplicación de dispositivo simulado IoT Hub de Java responde a la llamada del dispositivo gemelo][img-deviceconfigured]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha establecido una configuración deseada como *propiedades deseadas* desde el back-end de solución, y ha escrito una aplicación de dispositivo para detectar ese cambio y simular un proceso de actualización de varios pasos que informe de su estado mediante las propiedades notificadas.

Use los siguientes recursos para obtener información sobre cómo:

* enviar telemetría desde dispositivos con el tutorial [Introducción a IoT Hub][lnk-iothub-getstarted];
* programar o realizar operaciones en grandes conjuntos de dispositivos (consulte el tutorial [Schedule and broadcast jobs][lnk-schedule-jobs] [Programación y difusión de trabajos]);
* controlar los dispositivos de forma interactiva (por ejemplo, encender un ventilador desde una aplicación controlada por el usuario), con el tutorial [Uso de métodos directos][lnk-methods-tutorial].

<!-- images -->
[img-deviceconfigured]: media/iot-hub-java-java-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-java-device-class]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._device_twin._device

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app
