## <a name="iot-edge"></a>IoT Edge
Azure IoT Edge permite una implementación controlada por la nube de los servicios de Azure y código específico de solución para dispositivos locales. Los dispositivos IoT Edge pueden agregar datos de otros dispositivos para realizar computación y análisis antes de que los datos se envíen a la nube. Para más información, consulte [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>Agente de IoT Edge
La parte del sistema de tiempo de ejecución de IoT Edge responsable de implementar y supervisar los módulos.

## <a name="iot-edge-device"></a>Dispositivo de IoT Edge
Los dispositivos de IoT Edge tienen instalado el sistema de tiempo de ejecución de IoT Edge y se marcan como "Dispositivo de IoT Edge" en los detalles del dispositivo. Aprenda sobre la [Implementación de Azure IoT Edge en un dispositivo simulado en Linux: versión preliminar](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-deployment"></a>Implementación de IoT Edge
Una implementación de IoT Edge configura un conjunto de destinos de dispositivos de IoT Edge para ejecutar un conjunto de módulos de IoT Edge. Cada implementación garantiza continuamente que todos los dispositivos que coinciden con su condición de destino están ejecutando el conjunto especificado de módulos, incluso cuando se crean nuevos dispositivos o se modifican para que coincidan con la condición de destino. Cada dispositivo IoT Edge solo recibe la implementación de prioridad más alta cuya condición de destino cumple. Obtenga más información acerca de la [implementación de IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>Manifiesto de implementación de IoT Edge
Un documento Json que contiene la información que se va a copiar en uno o más del módulo o módulos gemelos de los dispositivos IoT Edge para implementar un conjunto de módulos, rutas y propiedades adecuadas del módulo asociado.

## <a name="iot-edge-gateway-device"></a>Dispositivo de puerta de enlace con IoT Edge
Un dispositivo IoT Edge con un dispositivo de nivel inferior. El dispositivo de nivel inferior puede ser un dispositivo IoT Edge o uno que no lo es.

## <a name="iot-edge-hub"></a>Centro de IoT Edge
La parte del sistema de tiempo de ejecución de IoT Edge responsable de las comunicaciones de módulo a módulo, comunicaciones ascendentes (hacia IoT Hub) y descendentes (desde IoT Hub). 

## <a name="iot-edge-leaf-device"></a>Dispositivo de hoja de IoT Edge
Un dispositivo de hoja IoT Edge sin dispositivo de nivel inferior. 

## <a name="iot-edge-module"></a>Módulo IoT Edge
Un módulo IoT Edge es un contenedor de Docker que puede implementar en dispositivos IoT Edge. Realiza una tarea específica,tal como ingerir un mensaje desde un dispositivo, transformar un mensaje o enviarlo a una instancia de IoT Hub. Se comunica con otros módulos y envía datos al sistema de tiempo de ejecución de IoT Edge. [Descripción de los requisitos y las herramientas para desarrollar módulos de IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>Identidad de módulo IoT Edge
Un registro en el registro de identidad de módulo de IoT Hub que detalla la existencia y las credenciales de seguridad que utiliza un módulo para autenticar con una instancia de Edge Hub o IoT Hub.

## <a name="iot-edge-module-image"></a>Imagen de módulo IoT Edge
La imagen de Docker que usa el sistema de tiempo de ejecución de IoT Edge para crear instancias del módulo.

## <a name="iot-edge-module-twin"></a>Módulo gemelo IoT Edge
Un documento Json que se conserva en IoT Hub y que almacena la información de estado de una instancia del módulo.

## <a name="iot-edge-priority"></a>Prioridad de IoT Edge
Cuando dos implementaciones IoT Edge tienen como destino el mismo dispositivo, se aplica la implementación con prioridad más alta. Si dos implementaciones tienen la misma prioridad, se aplica la implementación con la fecha de creación más reciente. Más información sobre [prioridad](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>Entorno de tiempo de ejecución de IoT Edge
El sistema de tiempo de ejecución de IoT Edge incluye todo lo que Microsoft distribuye para instalarse en un dispositivo IoT Edge. Incluye el agente de Edge, el centro de Edge y la herramienta Edge CTL.

## <a name="iot-edge-set-modules-to-a-single-device"></a>IoT Edge establece módulos en un solo dispositivo
Una operación que copia el contenido de un manifiesto IoT Edge en el módulo gemelo de un dispositivo. La API subyacente es un tipo genérico "aplicar configuración", que simplemente toma un manifiesto de IoT Edge como entrada.

## <a name="iot-edge-target-condition"></a>Condición de destino de IoT Edge
En una implementación IoT Edge, la condición de destino es cualquier condición booleana en las etiquetas de dispositivo gemelo para seleccionar los dispositivos de destino de la implementación, por ejemplo, "tag.environment = prod". La condición de destino se evalúa continuamente para incluir todos los nuevos dispositivos que cumplen los requisitos o desconectar los dispositivos que ya no lo hacen. Obtenga más información sobre la [condición de destino](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)