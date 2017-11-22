---
title: "Implementación de Azure Stream Analytics con Azure IoT Edge | Microsoft Docs"
description: "Implementación de Azure Stream Analytics como módulo en un dispositivo perimetral"
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: ebda79442b8feb9f052c3ae455fa43aafb7b5a6a
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Implementación de Azure Stream Analytics como un módulo de IoT Edge (versión preliminar)

Los dispositivos de IoT pueden generar grandes cantidades de datos. A veces, estos datos se tienen que analizar o procesar antes de llegar a la nube, con el fin de reducir el tamaño de los datos cargados o para eliminar la latencia de ida y vuelta de una información que requiere acción.

[Azure Stream Analytics][azure-stream] (ASA) proporciona una sintaxis de consulta con una estructura compleja para el análisis de datos tanto en la nube como en los dispositivos de IoT Edge. Para más información acerca de ASA en IoT Edge, consulte la [documentación de ASA](../stream-analytics/stream-analytics-edge.md).

Este tutorial le guía en la creación de un trabajo de Azure Stream Analytics y su implementación en un dispositivo de IoT Edge para procesar un flujo de datos de telemetría local directamente en el dispositivo y generar alertas para potenciar una acción inmediata en el dispositivo.  En este tutorial se usan dos módulos. Un módulo de sensor de temperatura simulado (tempSensor) que genera datos de temperatura de 20 a 120 grados, y que se incrementa en 1 cada 5 segundos, y un módulo ASA que filtra las temperaturas que superan los 100 grados. El módulo ASA también restablece tempSensor cuando la media de 30 segundos alcanza el valor de 100.

Aprenderá a:

> [!div class="checklist"]
> * Crear un trabajo de ASA para procesar los datos perimetrales
> * Conectar el nuevo trabajo de ASA con otros módulos de IoT Edge
> * Implementar el trabajo de ASA en un dispositivo de IoT Edge

## <a name="prerequisites"></a>Requisitos previos

* Una instancia de IoT Hub 
* El dispositivo que ha creado y configurado en la guía de inicio rápido o al implementar Azure IoT Edge en un dispositivo simulado en [Windows][lnk-tutorial1-win] y [Linux][lnk-tutorial1-lin].
* Docker en un dispositivo de IoT Edge
    * [Instale Docker en Windows] [ lnk-docker-windows] y asegúrese de que se ejecuta.
    * [Instale Docker en Linux] [ lnk-docker-linux] y asegúrese de que se ejecuta.
* Python 2.7.x en un dispositivo de IoT Edge
    * [Instalar Python 2.7 en Windows][lnk-python].
    * La mayoría de las distribuciones de Linux, incluida Ubuntu, ya tienen Python 2.7 instalado.  Use el siguiente comando para asegurarse de que PIP está instalado: `sudo apt-get install python-pip`.

> [!NOTE]
> Tenga en cuenta que tanto la cadena de conexión del dispositivo como el identificador del dispositivo de IoT Edge serán necesarios para este tutorial.

IoT Edge aprovecha la ventaja de los módulos de Azure Service IoT Edge creados previamente para realizar una implementación rápida, y Azure Stream Analytics (ASA) es uno de esos módulos. Puede crear un trabajo de ASA desde su portal y luego dirigirse al portal de IoT Hub para implementarlo como un módulo de IoT Edge.  

Para más información acerca de Azure Stream Analytics, consulte la sección **Introducción** de [Documentación de Stream Analytics][azure-stream].

## <a name="create-an-asa-job"></a>Creación de un trabajo de ASA

En esta sección, se crea un trabajo de Azure Stream Analytics para tomar datos de su centro de IoT, realizar consultas en los datos de telemetría enviados desde su dispositivo y reenviar los resultados a un contenedor de Azure Storage (BLOB). Para más información, consulte la sección **Introducción** de [Documentación de Stream Analytics][azure-stream]. 

> [!NOTE]
> Se requiere una cuenta de Azure Storage para proporcionar un punto de conexión que se use como salida en su trabajo de ASA. En el ejemplo siguiente utiliza el tipo de almacenamiento de blobs.  Para más información, consulte la sección **Blobs** de la [documentación de Azure Storage][azure-storage].

1. En Azure Portal, navegue hasta **Crear un recurso -> Almacenamiento**, haga clic en **Ver todo** y, luego, en **Cuenta de almacenamiento: blob, archivo, tabla, cola**.

2. Escriba el nombre de la cuenta de almacenamiento y seleccione la misma ubicación en que se almacena su instancia de IoT Hub. Haga clic en **Crear**. Anote el nombre para más adelante.

    ![nueva cuenta de almacenamiento][1]

3. En Azure Portal, navegue hasta la cuenta de almacenamiento que acaba de crear. Seleccione **Examinar blobs** en **Blob Service**. 
1. Cree un nuevo contenedor para el módulo ASA para almacenar datos. Establezca el nivel de acceso en _Container_. Haga clic en **Aceptar**.

    ![configuración de almacenamiento][10]

1. En Azure Portal, navegue hasta **Crear un recurso** > **Internet de las cosas** y seleccione **Trabajo de Stream Analytics**.

1. Escriba un nombre, **Elija "Edge" como entorno de hospedaje** y use los restantes valores predeterminados.  Haga clic en **Crear**.

    ![Creación de ASA][5]

2. Vaya al trabajo creado, en **Topología de trabajo**, seleccione **Entradas** y haga clic en **Agregar**.

3. Escriba el nombre `temperature`, elija **Flujo de datos** como el tipo de origen y utilice los valores predeterminados para los restantes parámetros. Haga clic en **Crear**.

    ![Entrada de ASA][2]

    > [!NOTE]
    > Las entradas adicionales pueden incluir puntos de conexión concretos de IoT Edge.

4. En **Topología de trabajo**, seleccione **Salidas** y haga clic en **Agregar**.

5. Escriba el nombre `alert` y use los valores predeterminados. Haga clic en **Crear**.

    ![Salida ASA][3]

6. En **Topología de trabajo**, seleccione **Consulta** y escriba lo siguiente:

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 100
    ```

## <a name="deploy-the-job"></a>Implementación del trabajo

Ya está listo para implementar el trabajo de ASA en su dispositivo de IoT Edge.

1. En Azure Portal, en la instancia de IoT Hub, navegue hasta **IoT Edge (versión preliminar)** y abra la hoja de su *{deviceId}*.

1. Seleccione **Set modules** (Establecer módulos) y, después, seleccione **Import Azure Service IoT Edge Module** (Importar módulo de Azure Service IoT Edge).

1. Seleccione la suscripción y el trabajo Edge de ASA que ha creado. Después, seleccione su cuenta de almacenamiento. Haga clic en **Guardar**.

    ![establecer módulo][6]

1. Haga clic en **Add IoT Edge Module** (Agregar módulo de IoT Edge) para agregar el módulo del sensor de temperatura. Escriba _tempSensor_ como nombre y `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` como dirección URL de la imagen. Deje los restantes valores intactos y haga clic en **Guardar**.

    ![módulo de temperatura][11]

1. Copie el nombre del módulo ASA. Haga clic en **Siguiente** para configurar las rutas.

1. Copie lo siguiente en **Rutas**.  Reemplace _{moduleName}_ por el nombre del módulo que ha copiado:

    ```json
    {
        "routes": {                                                               
          "telemetryToCloud": "FROM /messages/modules/tempSensor/* INTO $upstream", 
          "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream", 
          "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")", 
          "telemetryToAsa": "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")" 
        }
    }
    ```

1. Haga clic en **Siguiente**.

1. En el paso **Review Template** (Revisar plantilla), haga clic en **Enviar**.

1. Vuelva a la página de detalles del dispositivo y haga clic en **Actualizar**.  Debería ver el nuevo módulo _{moduleName}_ en ejecución, junto con el módulo **IoT Edge agent** e **IoT Edge hub**.

    ![salida del módulo][7]

## <a name="view-data"></a>Visualización de datos

Ahora puede ir a su dispositivo de IoT Edge para restaurar la interacción entre el módulo ASA y el módulo tempSensor.

1. En el símbolo del sistema, configure el runtime con la cadena de conexión del dispositivo de IoT Edge:

    ```cmd/sh
    iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords  
    ```

1. Ejecute el comando para iniciar el runtime:

    ```cmd/sh
    iotedgectl start  
    ```

1. Ejecute el comando para ver los módulos en ejecución:

    ```cmd/sh
    docker ps  
    ```

    ![salida de docker][8]

1. Ejecute el comando para ver todos los registros del sistema y datos de métricas. Utilice el nombre del módulo anterior:

    ```cmd/sh
    docker logs -f {moduleName}  
    ```

    ![registro de docker][9]

1. En Azure Portal, en su cuenta de Storage, en **Blob service**, haga clic en **Examinar blobs**, seleccione el contenedor y seleccione el archivo JSON recién creado.

1. Haga clic en **Descargar** y vea los resultados.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha configurado un contenedor de Azure Storage y un trabajo de Stream Analytics para analizar datos desde un dispositivo de IoT Edge.  Luego, ha cargado un módulo de ASA personalizado para mover datos desde el dispositivo a un BLOB, a través del flujo de datos para su descarga.  Puede continuar con otros tutoriales para ver con más detalle de qué formas puede Azure IoT Edge crear soluciones para su negocio.

> [!div class="nextstepaction"] 
> [Implementación de un modelo de Azure Machine Learning como módulo][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[2]: ./media/tutorial-deploy-stream-analytics/asa_input.png
[3]: ./media/tutorial-deploy-stream-analytics/asa_output.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/en-us/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/en-us/azure/storage/
[azure-stream]: https://docs.microsoft.com/en-us/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/