---
title: "Implementación de Azure Stream Analytics con Azure IoT Edge | Microsoft Docs"
description: "Implementación de Azure Stream Analytics como módulo en un dispositivo perimetral"
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/28/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: f40fb81fc03e796b906db12bf3bf6904b27b46eb
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Implementación de Azure Stream Analytics como un módulo de IoT Edge (versión preliminar)

Los dispositivos de IoT pueden generar grandes cantidades de datos. Para disminuir la cantidad de datos cargados o eliminar la latencia de ida y vuelta de una información práctica, a veces los datos se tienen que analizar o procesar antes de llegar a la nube.

Azure IoT Edge aprovecha los módulos de Azure Service IoT Edge creados previamente para realizar una implementación rápida. [Azure Stream Analytics][azure-stream] es un módulo de ese tipo. Puede crear un trabajo de Azure Stream Analytics desde su portal y luego dirigirse al portal de Azure IoT Hub para implementarlo como un módulo de IoT Edge. 

Azure Stream Analytics proporciona una sintaxis de consulta con una estructura compleja para el análisis de datos tanto en la nube como en los dispositivos de IoT Edge. Para más información sobre Azure Stream Analytics en IoT Edge, consulte la [documentación de Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md).

Este tutorial le guiará a través de la creación de un trabajo de Azure Stream Analytics y su implementación en un dispositivo de IoT Edge. Esto le permite procesar un flujo de telemetría local directamente en el dispositivo y generar alertas que impulsen una acción inmediata en el dispositivo. 

El tutorial presenta dos módulos: 
* Un módulo de sensor de temperatura simulado (tempSensor) que genera datos de temperatura que oscila de 20 a 120 grados, incrementados en 1 cada 5 segundos. 
* Un módulo de Stream Analytics que restablece tempSensor cuando la media de 30 segundos alcanza el valor de 70. En un entorno de producción, podría usar esta funcionalidad para apagar una máquina o tomar medidas preventivas cuando la temperatura alcance niveles peligrosos. 

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un trabajo de Azure Stream Analytics para procesar los datos en los dispositivos perimetrales.
> * Conectar el trabajo de Azure Stream Analytics nuevo con otros módulos de IoT Edge.
> * Implemente el trabajo de Azure Stream Analytics en un dispositivo de IoT Edge.

## <a name="prerequisites"></a>Requisitos previos

* Una instancia de IoT Hub. 
* El dispositivo que creó y configuró en el tutorial de inicio rápido o en los artículos sobre la implementación de Azure IoT Edge en un dispositivo simulado en [Windows][lnk-tutorial1-win] o en [Linux][lnk-tutorial1-lin]. Debe conocer la clave de conexión del dispositivo y el identificador del dispositivo. 
* Docker en ejecución en el dispositivo de IoT Edge.
    * [Instalar Docker en Windows][lnk-docker-windows].
    * [Instalar Docker en Linux][lnk-docker-linux].
* Python 2.7.x en un dispositivo de IoT Edge.
    * [Instalar Python 2.7 en Windows][lnk-python].
    * La mayoría de las distribuciones de Linux, incluida Ubuntu, ya tienen Python 2.7 instalado. Para asegurarse de que PIP está instalado, use el comando siguiente: `sudo apt-get install python-pip`.

## <a name="create-an-azure-stream-analytics-job"></a>Creación de un trabajo de Azure Stream Analytics

En esta sección, se crea un trabajo de Azure Stream Analytics para tomar datos de su centro de IoT, realizar consultas en los datos de telemetría enviados desde su dispositivo y luego reenviar los resultados a un contenedor de Azure Blob Storage. Para más información, consulte la sección "Introducción" de la [documentación de Stream Analytics][azure-stream]. 

### <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Se requiere una cuenta de Azure Storage para proporcionar un punto de conexión que se use como salida en su trabajo de Azure Stream Analytics. En el ejemplo de esta sección se usa el tipo de almacenamiento de blobs. Para más información, consulte la sección "Blobs" de [la documentación de Azure Storage][azure-storage].

1. En Azure Portal, vaya a **Crear un recurso**, escriba **Cuenta de almacenamiento** en el cuadro de búsqueda y, luego, seleccione **Cuenta de almacenamiento: blob, archivo, tabla, cola**.

2. En el panel **Crear cuenta de almacenamiento**, escriba un nombre para la cuenta de almacenamiento, seleccione la misma ubicación donde está almacenada la instancia de IoT Hub y, luego, seleccione **Crear**. Anote el nombre para usarlo más adelante.

    ![Crear una cuenta de almacenamiento][1]

3. Vaya a la cuenta de almacenamiento que acaba de crear y, luego, seleccione **Examinar blobs**. 

4. Cree un contenedor para que el módulo de Azure Stream Analytics almacene datos, establezca el nivel de acceso en **Contenedor** y, luego, seleccione **Aceptar**.

    ![Configuración de almacenamiento][10]

### <a name="create-a-stream-analytics-job"></a>Creación de un trabajo de Stream Analytics

1. En Azure Portal, vaya a **Crear un recurso** > **Internet de las cosas** y, luego, seleccione **Trabajo de Stream Analytics**.

2. En el panel **Nuevo trabajo de Stream Analytics**, haga lo siguiente:

    a. En el cuadro **Nombre del trabajo**, escriba un nombre de trabajo.
    
    b. En **Entorno de hospedaje**, seleccione **Edge**.
    
    c. En el resto de los campos, use los valores predeterminados.

    > [!NOTE]
    > Actualmente, no se admiten los trabajos de Azure Stream Analytics en IoT Edge en la región Oeste de EE. UU. 2. 

3. Seleccione **Crear**.

4. En el trabajo creado, en **Topología de trabajo**, seleccione **Entradas** y, luego, seleccione **Agregar**.

5. En el panel **Nueva entrada**, haga lo siguiente:

    a. En el cuadro **Alias de entrada**, escriba **temperatura**.
    
    b. En el cuadro **Tipo de origen**, seleccione **Flujo de datos**.
    
    c. En el resto de los campos, use los valores predeterminados.

   ![Entrada de Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_input.png)

6. Seleccione **Crear**.

7. En **Topología de trabajo**, seleccione **Salidas** y haga clic en **Agregar**.

8. En el panel **Nueva salida**, haga lo siguiente:

    a. En el cuadro **Alias de salida**, escriba **alerta**.
    
    b. En el resto de los campos, use los valores predeterminados. 
    
    c. Seleccione **Crear**.

   ![Salida de Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_output.png)


9. En **Topología de trabajo**, seleccione **Consulta** y reemplace el texto predeterminado por la consulta siguiente:

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 70
    ```

10. Seleccione **Guardar**.

## <a name="deploy-the-job"></a>Implementación del trabajo

Ya está listo para implementar el trabajo de Azure Stream Analytics en el dispositivo de IoT Edge.

1. En Azure Portal, en la instancia de IoT Hub, vaya a **IoT Edge (versión preliminar)** y abra la página de detalles del dispositivo de IoT Edge.

2. Seleccione **Set modules** (Establecer módulos).  
    Si anteriormente implementó el módulo tempSensor en este dispositivo, puede que se rellene automáticamente. Si no es así, haga lo siguiente para agregar el módulo:

   a. Seleccione **Add IoT Edge module** (Agregar módulo de IoT Edge).

   b. Para el nombre, escriba **tempSensor**.
    
   c. Para el URI de la imagen, escriba **microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview**. 

   d. Deje intactos los valores restantes.
   
   e. Seleccione **Guardar**.

3. Para agregar el trabajo Edge de Azure Stream Analytics, seleccione **Import Azure Stream Analytics IoT Edge Module** (Importar módulo de IoT Edge de Azure Stream Analytics).

4. Seleccione la suscripción y el trabajo Edge de Azure Stream Analytics que creó. 

5. Seleccione la suscripción y la cuenta de almacenamiento que creó y, luego, seleccione **Guardar**.

    ![Establecer módulo][6]

6. Copie el nombre de su módulo de Azure Stream Analytics. 

    ![Módulo de temperatura][11]

7. Para configurar las rutas, seleccione **Siguiente**.

8. Copie el código siguiente en **Rutas**. Reemplace _{moduleName}_ por el nombre del módulo que ha copiado:

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

9. Seleccione **Siguiente**.

10. En el paso **Revisar plantilla**, seleccione **Enviar**.

11. Vuelva a la página de detalles del dispositivo y, luego, seleccione **Actualizar**.  
    Debería ver el nuevo módulo de Stream Analytics en ejecución junto con el módulo del agente de IoT Edge y el centro de IoT Edge.

    ![Salida del módulo][7]

## <a name="view-data"></a>Visualización de datos

Ahora puede ir a su dispositivo de IoT Edge para revisar la interacción entre el módulo Azure Stream Analytics y el módulo tempSensor.

1. Compruebe que todos los módulos se ejecutan en Docker:

   ```cmd/sh
   docker ps  
   ```

   ![Salida de Docker][8]

2. Vea todos los registros del sistema y datos de métrica. Use el nombre del módulo de Stream Analytics:

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

Podrá observar que la temperatura de la máquina sube gradualmente hasta alcanzar 70 grados durante 30 segundos. Después, el módulo de Stream Analytics desencadena un restablecimiento y la temperatura de la máquina vuelve a bajar a 21. 

   ![Registro de Docker][9]


## <a name="next-steps"></a>pasos siguientes

En este tutorial ha configurado un contenedor de Azure Storage y un trabajo de Stream Analytics para analizar datos desde un dispositivo de IoT Edge. Luego, ha cargado un módulo Azure Stream Analytics personalizado para mover datos desde el dispositivo a un blob, a través del flujo de datos para su descarga. Para ver cómo Azure IoT Edge puede crear más soluciones para la empresa, siga con los otros tutoriales.

> [!div class="nextstepaction"] 
> [Implementación de un modelo de Azure Machine Learning como módulo][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
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
[azure-iot]: https://docs.microsoft.com/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/azure/storage/
[azure-stream]: https://docs.microsoft.com/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/
