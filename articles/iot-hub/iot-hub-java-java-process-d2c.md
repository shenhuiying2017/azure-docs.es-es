---
title: Enrutamiento de mensajes con Azure IoT Hub (Java) | Microsoft Docs
description: "Cómo procesar mensajes de dispositivo a nube de Azure IoT Hub mediante reglas de enrutamiento y puntos de conexión personalizados para enviar mensajes a otros servicios de back-end."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: dobett
ms.openlocfilehash: 92ab10e5b8487e03d92b69114a2e3c5302f95ed6
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2018
---
# <a name="routing-messages-with-iot-hub-java"></a>Enrutamiento de mensajes con IoT Hub (Java)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Azure IoT Hub es un servicio totalmente administrado que permite la comunicación bidireccional confiable y segura entre millones de dispositivos y un back-end de la solución. Otros tutoriales ([Introducción a IoT Hub] y [Envío de mensajes de nube a dispositivo con IoT Hub][lnk-c2d]) muestran cómo usar la funcionalidad básica de mensajería de dispositivo a nube y de nube a dispositivo de IoT Hub.

Este tutorial se basa en el código que se muestra en el tutorial [Introducción a IoT Hub] y le muestra cómo usar el enrutamiento de mensajes para procesar mensajes del dispositivo a la nube de manera escalable. El tutorial ilustra cómo procesar mensajes que requieren acción inmediata en el back-end de soluciones. Por ejemplo, un dispositivo puede enviar un mensaje de alarma que desencadena la inserción de una incidencia en un sistema CRM. Por el contrario, los mensajes de punto de datos simplemente se envían a un motor de análisis. Por ejemplo, la telemetría de temperatura de un dispositivo que se almacena para su posterior análisis es un mensaje de punto de datos.

Al final de este tutorial, ejecutará tres aplicaciones de consola de Java:

* **simulated-device**, una versión modificada de la aplicación creada en el tutorial [Introducción a IoT Hub] , que envía mensajes de dispositivo a nube de punto de datos cada segundo y mensajes de dispositivo a nube interactivos cada 10 segundos. Esta aplicación usa el protocolo AMQP para comunicarse con IoT Hub.
* **read-d2c-messages** muestra los datos de telemetría enviados por la aplicación de dispositivo.
* **read-critical-queue** quita de la cola los mensajes críticos de la cola de la instancia de Service Bus adjunta a IoT Hub.

> [!NOTE]
> El Centro de IoT ofrece compatibilidad con el SDK para muchas plataformas de dispositivos y lenguajes, entre los que se incluyen C, Java y JavaScript. Para instrucciones sobre cómo reemplazar el dispositivo de este tutorial con un dispositivo físico y sobre cómo conectar dispositivos a IoT Hub, consulte el [Centro para desarrolladores de Azure IoT].

Para completar este tutorial, necesitará lo siguiente:

* Una versión funcional y completa del tutorial [Introducción a IoT Hub] .
* La versión más reciente de [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

También se recomienda leer sobre [Azure Storage] y [Azure Service Bus].

## <a name="send-interactive-messages-from-a-device-app"></a>Envío de mensajes interactivos desde una aplicación de dispositivo
En esta sección, se modifica la aplicación de dispositivo que creó en el tutorial [Introducción a IoT Hub] a fin de enviar ocasionalmente mensajes que requieren un procesamiento inmediato.

1. Con un editor de texto, abra el archivo simulated-device\src\main\java\com\mycompany\app\App.java. Este archivo contiene el código para la aplicación **simulated-device** que creó en el tutorial [Introducción a IoT Hub] .

2. Reemplace la clase **MessageSender** por el código siguiente:

    ```java
    private static class MessageSender implements Runnable {

        public void run()  {
            try {
                double minTemperature = 20;
                double minHumidity = 60;
                Random rand = new Random();

                while (true) {
                    String msgStr;
                    Message msg;
                    if (new Random().nextDouble() > 0.7) {
                        if (new Random().nextDouble() > 0.5) {
                            msgStr = "This is a critical message.";
                            msg = new Message(msgStr);
                            msg.setProperty("level", "critical");
                        } else {
                            msgStr = "This is a storage message.";
                            msg = new Message(msgStr);
                            msg.setProperty("level", "storage");
                        }
                    } else {
                        double currentTemperature = minTemperature + rand.nextDouble() * 15;
                        double currentHumidity = minHumidity + rand.nextDouble() * 20; 
                        TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
                        telemetryDataPoint.deviceId = deviceId;
                        telemetryDataPoint.temperature = currentTemperature;
                        telemetryDataPoint.humidity = currentHumidity;

                        msgStr = telemetryDataPoint.serialize();
                        msg = new Message(msgStr);
                    }
                    
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
   
    Con este método se agrega aleatoriamente la propiedad `"level": "critical"` y `"level": "storage"` a los mensajes que envía el dispositivo, lo que simula un mensaje que requiere una acción inmediata del back-end de aplicaciones o uno que necesita almacenarse permanentemente. La aplicación admite el enrutamiento de mensajes basado en el cuerpo del mensaje.
   
   > [!NOTE]
   > Puede usar propiedades de mensaje a fin de enrutar mensajes en diferentes escenarios, como el procesamiento en frío, además del ejemplo de procesamiento en caliente que se muestra aquí.

2. Guarde y cierre el archivo simulated-device\src\main\java\com\mycompany\app\App.java.

    > [!NOTE]
    > Es muy recomendable implementar una directiva de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Control de errores transitorios].

3. Para compilar la aplicación **simulated-device** con Maven, ejecute el siguiente comando en el símbolo del sistema en la carpeta simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="add-a-queue-to-your-iot-hub-and-route-messages-to-it"></a>Adición de una cola a su IoT Hub y enrutamiento de mensajes a ella

En esta sección, se crea una cola de Service Bus, se conecta con el IoT Hub y se configura el IoT Hub para enviar mensajes a la cola en función de la presencia de una propiedad en el mensaje. Para obtener más información acerca de cómo procesar los mensajes desde las colas de Service Bus, consulte [Introducción a las colas][lnk-sb-queues-java].

1. Cree una cola de Service Bus como se describe en [Introducción a las colas][lnk-sb-queues-java]. Tome nota del espacio de nombres y del nombre de la cola.

    > [!NOTE]
    > Las colas y los temas de Service Bus usados como puntos de conexión de IoT Hub no deben tener habilitadas las opciones **Sesiones** o **Detección de duplicados**. Si cualquiera de estas opciones está habilitada, el punto de conexión aparece como **Inaccesible** en Azure Portal.

2. En Azure Portal, abra el centro de IoT y haga clic en **Puntos de conexión** .

    ![Puntos de conexión en IoT Hub][30]

3. En la hoja **Puntos de conexión**, haga clic en **Agregar** en la parte superior para agregar la cola al centro de IoT. Ponga al punto de conexión el nombre **CriticalQueue** y use las listas desplegables para seleccionar **Cola de Service Bus**, el espacio de nombres de Service Bus en el que reside la cola y el nombre de la cola. Cuando haya terminado, haga clic en **Guardar** de la parte inferior.

    ![Adición de un punto de conexión][31]

4. Ahora haga clic en **Rutas** en IoT Hub. Haga clic en **Agregar** en la parte superior de la hoja para crear una regla que enrute los mensajes a la cola que acaba de agregar. Seleccione **DeviceTelemetry** como origen de los datos. Escriba `level="critical"` como condición y elija la cola que acaba de agregar como punto de conexión personalizado como punto de conexión de regla de enrutamiento. Cuando haya terminado, haga clic en **Guardar** de la parte inferior.

    ![Adición de una ruta][32]

    Asegúrese de que la ruta de reserva se establece en **ON** (Activado). Esta es la configuración predeterminada del centro de IoT.

    ![Ruta de reserva][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Opcional) Lectura desde el punto de conexión de la cola

Opcionalmente, puede leer los mensajes desde el punto de conexión de la cola siguiendo las instrucciones de [Introducción a las colas][lnk-sb-queues-java]. Ponga a la aplicación el nombre **read-critical-queue**.

## <a name="run-the-applications"></a>Ejecución de las aplicaciones

Ahora está preparado para ejecutar las tres aplicaciones.

1. Para ejecutar la aplicación **process-d2c-messages**, en un símbolo del sistema o en el shell, vaya a la carpeta read-d2c y ejecute el siguiente comando:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Ejecución de read-d2c-messages][readd2c]

2. Para ejecutar la aplicación **read-critical-queue**, en un símbolo del sistema o el shell, navegue hasta la carpeta read-critical-queue y ejecute el comando siguiente:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Ejecución de read-critical-messages][readqueue]

3. Para ejecutar la aplicación **simulated-device**, en un símbolo del sistema o en el shell, vaya a la carpeta simulated-device y ejecute el siguiente comando:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Ejecución de simulated-device][simulateddevice]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Opcional) Adición de un contenedor de almacenamiento a IoT Hub y enrutamiento de mensajes a este

En esta sección, se crea una cuenta de Storage, se conecta con el IoT Hub y se configura este para enviar mensajes a la cuenta en función de la presencia de una propiedad en el mensaje. Para más información acerca de cómo administrar el almacenamiento, consulte [Introducción a Azure Storage][Azure Storage].

 > [!NOTE]
   > Si no está limitado a un **punto de conexión**, puede configurar **StorageContainer**, además de **CriticalQueue**, y ejecutar ambos simultáneamente.

1. Cree una cuenta de Storage como se describe en la [documentación de Azure Storage][lnk-storage]. Tome nota del nombre de la cuenta.

2. En Azure Portal, abra el centro de IoT y haga clic en **Puntos de conexión**.

3. En la hoja **Puntos de conexión**, seleccione el punto de conexión **CriticalQueue** y haga clic en **Eliminar**. Haga clic en **Sí** y, luego, en **Agregar**. Asigne el nombre **StorageContainer** al punto de conexión y use los menús desplegables para seleccionar **Contenedor de Azure Storage** y crear una **cuenta de Storage** y un **contenedor de Storage**.  Tome nota de los nombres.  Cuando haya terminado, haga clic en **Aceptar** en la parte inferior. 

 > [!NOTE]
   > Si no está limitado a un **punto de conexión**, no es necesario eliminar **CriticalQueue**.

4. Haga clic en **Rutas** en IoT Hub. Haga clic en **Agregar** en la parte superior de la hoja para crear una regla que enrute los mensajes a la cola que acaba de agregar. Seleccione **Mensajes del dispositivo** como origen de los datos. Escriba `level="storage"` como condición y elija **StorageContainer** como punto de conexión personalizado como punto de conexión de regla de enrutamiento. Haga clic en **Guardar** en la parte inferior.  

    Asegúrese de que la ruta de reserva se establece en **Activado**. Esta es la configuración predeterminada del centro de IoT.

1. Asegúrese de que no se ha detenido la ejecución de las aplicaciones anteriores. 

1. En Azure Portal, vaya a la cuenta de almacenamiento, en **Blob Service** y haga clic en **Examinar blobs...**.  Seleccione el contenedor, vaya al archivo JSON y haga clic en él. Después, haga clic en **Descargar** para ver los datos.

## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha aprendido a enviar de manera confiable mensajes del dispositivo a la nube mediante la funcionalidad de enrutamiento de mensajes de IoT Hub.

El tutorial sobre [cómo enviar mensajes de dispositivo a la nube con IoT Hub][lnk-c2d] muestra cómo enviar mensajes a los dispositivos desde la solución de back-end.

Para ver ejemplos de soluciones completas de un extremo a otro que usan IoT Hub, consulte [Azure IoT Suite][lnk-suite].

Para obtener más información sobre cómo desarrollar soluciones con IoT Hub, consulte la [Guía del desarrollador de IoTHub de Azure].

Para obtener más información sobre el enrutamiento de mensajes en IoT Hub, consulte [Envío y recepción de mensajes con IoT Hub][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-java-java-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-java-java-process-d2c/route-creation.png
[33]: ./media/iot-hub-java-java-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-java]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[Guía del desarrollador de IoTHub de Azure]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Introducción a IoT Hub]: iot-hub-java-java-getstarted.md
[Centro para desarrolladores de Azure IoT]: https://azure.microsoft.com/develop/iot
[Control de errores transitorios]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-java-java-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
