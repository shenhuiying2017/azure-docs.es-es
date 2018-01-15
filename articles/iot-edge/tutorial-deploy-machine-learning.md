---
title: "Implementación de Azure Machine Learning con Azure IoT Edge | Microsoft Docs"
description: "Implementación de Azure Machine Learning como módulo en un dispositivo perimetral"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/13/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: a0131fdbbf926d59eae06089cde109649a1433b8
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Implementación de Azure Machine Learning como un módulo de IoT Edge (versión preliminar)

Los módulos de IoT Edge se pueden usar para implementar código que, a su vez, implementa una lógica de negocios directamente en los dispositivos de IoT Edge. Este tutorial le guía a través de la implementación de un módulo de Azure Machine Learning que predice cuándo se produce un error en un dispositivo basándose en los datos de sensor del dispositivo de IoT Edge simulado que creó en los tutoriales [Implementación de Azure IoT Edge en un dispositivo simulado en Windows][lnk-tutorial1-win] o [Linux][lnk-tutorial1-lin]. 

En este tutorial, aprenderá a: 

> [!div class="checklist"]
> * Creación de un módulo de Azure Machine Learning
> * Insertar un contenedor de módulo en Azure Container Registry
> * Implementar un modelo de Azure Machine Learning en un dispositivo de IoT Edge
> * Visualización de datos generados

El módulo de Azure Machine Learning que se crea en este tutorial lee los datos de temperatura que genera el dispositivo y solo envía mensajes en dirección ascendente a Azure IoT Hub si predice un error (denominado anomalía). 


## <a name="prerequisites"></a>Requisitos previos

* El dispositivo de Azure IoT Edge que creó en la guía de inicio rápido o en el primer tutorial.
* La cadena de conexión de IoT Hub del centro de IoT al que se conecta el dispositivo de IoT Edge.
* Una cuenta de Azure Machine Learning. Para crear una cuenta, siga las instrucciones que se incluyen en [Creación de cuentas de Azure Machine Learning e instalación de Azure Machine Learning Workbench](../machine-learning/preview/quickstart-installation.md#create-azure-machine-learning-accounts). No es necesario instalar la aplicación de área de trabajo para este tutorial. 
* Administración del módulo de Azure Machine Learning en su equipo. Para configurar el entorno y crear una cuenta, siga las instrucciones de [Instalación de Administración de modelos](https://docs.microsoft.com/azure/machine-learning/preview/deployment-setup-configuration).

## <a name="create-the-azure-ml-container"></a>Creación del contenedor de Azure Machine Learning
En esta sección, descargará los archivos de modelo entrenados y los convertirá en un contenedor de Azure Machine Learning.  

En el equipo que ejecuta Administración del módulo de Azure Machine Learning, descargue y guarde [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) y [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl) del Kit de herramientas de IoT de Azure Machine Learning en GitHub. Estos archivos definen el modelo de aprendizaje automático entrenado que implementará en su dispositivo IoT Edge. 

Utilice el modelo entrenado para crear un contenedor que puede implementarse en dispositivos IoT Edge.

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```
El nombre del servicio, *machinelearningmodule*, en este ejemplo, se convierte en el nombre de la imagen de contenedor de Docker.

### <a name="view-the-container-repository"></a>Ver el repositorio de contenedores

Compruebe que la imagen de contenedor se creó y almacenó correctamente en el repositorio de contenedores de Azure asociado a su entorno de aprendizaje automático.

1. En [Azure Portal](https://portal.azure.com), vaya a **Todos los servicios** y seleccione **Registros de contenedor**.
2. Seleccione el registro. El nombre debe comenzar con **mlcr** y pertenece al grupo de recursos, a la ubicación y a la suscripción que usó para configurar Administración del módulo.
3. Seleccione **Claves de acceso**.
4. Copie los valores de **Servidor de inicio de sesión**, **Nombre de usuario** y **Contraseña**.  Necesitará estos valores para tener acceso al registro de los dispositivo Edge.
5. Seleccione **Repositorios**.
6. Seleccione **machinelearningmodule**.
7. Ya tiene la ruta completa de la imagen del contenedor. Apúntela para la sección siguiente. Debería tener el aspecto siguiente: **<registry_name>.azureacr.io/machinelearningmodule:1**

## <a name="add-registry-credentials-to-your-edge-device"></a>Adición de las credenciales del Registro a un dispositivo de Edge

Agregue las credenciales del Registro al runtime de Edge en el equipo en que ejecuta el dispositivo de Edge. Este comando proporciona acceso al runtime para extraer el contenedor.

Linux:
   ```cmd
   sudo iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password> 
   ```

Windows:
   ```cmd
   iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password> 
   ```

## <a name="run-the-solution"></a>Ejecución de la solución

1. En [Azure Portal](https://portal.azure.com), navegue hasta su centro de IoT.
1. Vaya a **IoT Edge (versión preliminar)** y seleccione el dispositivo de IoT Edge.
1. Seleccione **Set modules** (Establecer módulos).
1. Si anteriormente implementó el módulo tempSensor en su dispositivo IoT Edge, puede que se rellene automáticamente. Si todavía no está en la lista de módulos, agréguelo.
    1. Seleccione **Add IoT Edge module** (Agregar módulo de IoT Edge).
    2. En el campo **Nombre**, escriba `tempSensor`.
    3. En el campo **URI de la imagen**, escriba `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Seleccione **Guardar**.
1. Agregue el módulo de Machine Learning que creó.
    1. Seleccione **Add IoT Edge module** (Agregar módulo de IoT Edge).
    1. En el campo **Nombre**, escriba `machinelearningmodule`.
    1. En el campo **Imagen**, escriba la dirección de la imagen; por ejemplo, `<registry_name>.azurecr.io/machinelearningmodule:1`.
    1. Seleccione **Guardar**.
1. Vuelva al paso **Agregar módulos** y seleccione **Siguiente**.
1. En el paso **Specify Routes** (Especificar rutas), copie el archivo JSON siguiente en el cuadro de texto. La primera ruta transporta los mensajes del sensor de temperatura al módulo de Machine Learning a través del punto de conexión "amlInput", que es el punto de conexión que utilizan todos los módulos de Azure Machine Learning. La segunda ruta transporta mensajes del módulo de Machine Learning a IoT Hub. En esta ruta, ''amlOutput'' es el punto de conexión que usan todos los módulos de Azure Machine Learning para los datos de salida y "$upstream" indica a IoT Hub. 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. Seleccione **Siguiente**. 
1. En el paso **Revisar plantilla**, seleccione **Enviar**. 
1. Vuelva a la página de detalles del dispositivo y seleccione **Actualizar**.  Debería ver el nuevo módulo **machinelearningmodule** en ejecución junto con el módulo **tempSensor** y el módulo runtime de IoT Edge.

## <a name="view-generated-data"></a>Visualización de datos generados

Puede ver los mensajes del dispositivo a la nube que el dispositivo de IoT Edge envía con la extensión de Azure IoT Toolkit para Visual Studio Code. 

1. En Visual Studio Code, seleccione **IoT Hub Devices** (Dispositivos de IoT Hub). 
2. Seleccione **...**  y, después, seleccione **Set IoT Hub Connection String** (Establecer cadena de conexión de IoT Hub) en el menú. 

   ![Menú más de dispositivos de IoT Hub](./media/tutorial-deploy-machine-learning/set-connection.png)

3. En el cuadro de texto que aparece en la parte superior de la página, escriba la cadena de conexión iothubowner para IoT Hub. El dispositivo IoT Edge debe aparecer en la lista de dispositivos de IoT Hub.
4. Vuelva a seleccionar **...**  y, después, **Start monitoring D2C message** (Iniciar la supervisión del mensaje de D2C).
5. Observe los mensajes procedentes de tempSensor cada cinco segundos, que machinelearningmodule anexa con su evaluación del estado de dispositivo. 

   ![Respuesta de Azure Machine Learning en el cuerpo del mensaje](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha implementado un módulo de IoT Edge con tecnología de Azure Machine Learning. Puede continuar con cualquiera de los otros tutoriales para aprender otras formas en las que Azure IoT Edge puede ayudarle a convertir los datos en perspectivas empresariales en el perímetro.

> [!div class="nextstepaction"]
> [Implementación de una función de Azure como un módulo](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
