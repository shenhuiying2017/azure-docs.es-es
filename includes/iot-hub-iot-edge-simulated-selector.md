> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-simulated-device.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-simulated-device.md)

En este tutorial del [ejemplo de carga en la nube de dispositivos simulados], se muestra cómo usar [Azure IoT Edge][lnk-sdk] para enviar los datos de telemetría que envían los dispositivos a la nube a IoT Hub desde dispositivos simulados.

En este tutorial, se describen los siguientes procedimientos:

* **Arquitectura**: información de arquitectura sobre el [ejemplo de carga en la nube de dispositivos simulados].
* **Compilación y ejecución**: los pasos necesarios para compilar y ejecutar el ejemplo.

## <a name="architecture"></a>Arquitectura

El [ejemplo de carga en la nube de dispositivos simulados] muestra cómo crear una puerta de enlace que envíe datos de telemetría desde dispositivos simulados a una instancia de IoT Hub. Es posible que un dispositivo no se pueda conectar directamente a IoT Hub si:

* No usa un protocolo de comunicaciones que entienda IoT Hub.
* No es lo suficientemente inteligente como para recordar la identidad que le ha asignado IoT Hub.

Una puerta de enlace de IoT Edge puede resolver estos problemas de las siguientes maneras:

* La puerta de enlace comprende el protocolo utilizado por el dispositivo, recibe la telemetría que envía el dispositivo a la nube de este y reenvía esos mensajes a IoT Hub mediante un protocolo comprendido por este.

* La puerta de enlace asigna las identidades de IoT Hub a los dispositivos y actúa como un servidor proxy cuando un dispositivo envía mensajes a IoT Hub.

En el siguiente diagrama se muestran los componentes principales del ejemplo, incluidos los módulos de IoT Edge:

![Diagrama: mensaje de dispositivo simulado pasa a través de puerta de enlace a IoT Hub][1]

Este ejemplo contiene tres módulos que constituyen la puerta de enlace:
1. Módulo de ingesta de protocolos
1. Módulo de identificación MAC &lt;-&gt; IoT Hub
1. Módulo de comunicación del Centro de IoT

Los módulos no se pasan mensajes directamente. Los módulos publican mensajes en un agente interno que entrega los mensajes a los otros módulos mediante un mecanismo de suscripción. Para más información, consulte [Introducción a Azure IoT Edge][lnk-gw-getstarted].

![Diagrama: módulos de puerta de enlace se comunican con agente][2]

### <a name="protocol-ingestion-module"></a>Módulo de ingesta de protocolos

El módulo de ingesta de protocolos es el punto de partida del proceso para tomar datos de dispositivos mediante la puerta de enlace y depositarlos en la nube. 

En el ejemplo, este módulo:

1. Crea datos de temperatura simulados. Si usa dispositivos físicos, el módulo leerá datos de esos dispositivos físicos.
1. Crea un mensaje.
1. Coloca los datos de temperatura simulados en el contenido del mensaje.
1. Agrega una propiedad con una dirección de MAC falsa al mensaje.
1. Permite que el mensaje esté disponible para el siguiente módulo de la cadena.

El módulo de ingesta de protocolos es **simulated_device.c** en el código fuente.

### <a name="mac-lt-gt-iot-hub-id-module"></a>Módulo de identificación MAC &lt;-&gt; IoT Hub

El módulo de identificación MAC &lt;-&gt; IoT Hub funciona como un traductor. En este ejemplo se usa una dirección MAC como identificador único del dispositivo y se correlaciona con una identidad de dispositivo del Centro de IoT. Sin embargo, puede escribir su propio módulo para que utilice un identificador único diferente. Por ejemplo, puede que los dispositivos tengan números de serie exclusivos o puede que los datos de telemetría incluyan un nombre de dispositivo incrustado único.

En el ejemplo, este módulo:

1. Busca los mensajes que tienen una propiedad de dirección de MAC.
1. Si hay una dirección MAC, agrega otra propiedad con una clave de dispositivo de IoT Hub al mensaje. 
1. Permite que el mensaje esté disponible para el siguiente módulo de la cadena.

El desarrollador configura una asignación entre las direcciones de MAC y las identidades de IoT Hub para asociar los dispositivos simulados a identidades de dispositivos de IoT Hub. El desarrollador agrega la asignación manualmente como parte de la configuración del módulo.

El módulo de identificación MAC &lt;-&gt; IoT Hub es **identitymap.c** en el código fuente. 

### <a name="iot-hub-communication-module"></a>Módulo de comunicación del Centro de IoT

El módulo de comunicación de IoT Hub abre una única conexión HTTP desde la puerta de enlace a IoT Hub. HTTP es uno de los tres protocolos que IoT Hub comprende. Gracias a este módulo no tiene que abrir una conexión para cada dispositivo al multiplexar conexiones desde todos los dispositivos a través de una conexión. Este enfoque permite conectar muchos dispositivos a una única puerta de enlace. 

En el ejemplo, este módulo:

1. Toma los mensajes con una propiedad de clave de dispositivo de IoT Hub que le asignó el módulo anterior. 
1. Envía el contenido del mensaje a IoT Hub mediante el protocolo HTTP. 

El módulo de comunicación de IoT Hub es **iothub.c** en el código fuente.

## <a name="before-you-get-started"></a>Antes de comenzar

Antes de comenzar, realice los siguientes pasos:

* [Crear una instancia de IoT Hub][lnk-create-hub] en su suscripción de Azure. Necesita el nombre de su hub para este tutorial de ejemplo. Si no tiene ninguna, puede crear una [cuenta gratuita][lnk-free-trial] en tan solo unos minutos.
* Agregue dos dispositivos a su instancia de IoT Hub y tome nota de los identificadores y las claves de dispositivo. Puede usar la herramienta [Explorador de dispositivos][lnk-device-explorer] o las herramientas [iothub-explorer][lnk-iothub-explorer] para agregar los dispositivos a la instancia de IoT Hub y recuperar las claves.


<!-- Images -->
[1]: media/iot-hub-iot-edge-simulated-selector/image1.png
[2]: media/iot-hub-iot-edge-simulated-selector/image2.png

<!-- Links -->
[ejemplo de carga en la nube de dispositivos simulados]: https://github.com/Azure/iot-edge/blob/master/samples/simulated_device_cloud_upload/README.md
[lnk-sdk]: https://github.com/Azure/iot-edge
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
[lnk-create-hub]: ../articles/iot-hub/iot-hub-create-through-portal.md