> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-node-twin-getstarted.md)
> 
> 

## <a name="introduction"></a>Introducción
Los dispositivos gemelos son documentos JSON que almacenan información sobre el estado de los dispositivos (metadatos, configuraciones y condiciones). IoT Hub conserva un dispositivo gemelo por cada dispositivo que se conecta a él.

Use los dispositivos gemelos para:

* Almacenar metadatos del dispositivo desde el back-end de la solución.
* Notificar la información sobre el estado actual, como las funcionalidades y las condiciones disponibles (por ejemplo, el método de conectividad usado) de la aplicación del dispositivo.
* Sincronizar el estado de flujos de trabajo de larga duración (como las actualizaciones del firmware y de la configuración) entre la aplicación del dispositivo y la del back-end.
* Consultar los metadatos, la configuración o el estado del dispositivo.

> [!NOTE]
> Los dispositivos gemelos están diseñados para la sincronización y para consultar las condiciones y configuraciones del dispositivo. Para más información sobre cuándo usar dispositivos gemelos, consulte [Introducción a los dispositivos gemelos][lnk-twins].
> 
> 

Los dispositivos gemelos se almacenan en un IoT Hub y contienen:

* *etiquetas*, metadatos de dispositivo a los que solo puede acceder el back-end de la solución;
* *propiedades deseadas*, objetos JSON que puede modificar el back-end de la solución y puede observar la aplicación del dispositivo; y
* *propiedades indicadas*, objetos JSON que puede modificar la aplicación del dispositivo y que puede leer el back-end de la solución. Las etiquetas y propiedades no pueden contener matrices, pero se pueden anidar objetos.

![][img-twin]

Además, el back-end de la solución puede consultar los dispositivos gemelos en función de todos los datos descritos anteriormente.
Consulte [Introducción a los dispositivos gemelos][lnk-twins] para más información acerca de los dispositivos gemelos, y la referencia sobre [el lenguaje de consultas de IoT Hub][lnk-query] para realizar consultas.

> [!NOTE]
> En la actualidad, solo se puede acceder a los dispositivos gemelos desde dispositivos conectados a IoT Hub mediante el protocolo MQTT. Para instrucciones acerca de cómo convertir la aplicación de dispositivo existente para usar MQTT, consulte el artículo sobre [compatibilidad con MQTT][lnk-devguide-mqtt].
> 
> 

En este tutorial se muestra cómo realizar las siguientes acciones:

* Crear una aplicación de back-end que agrega *etiquetas* a un dispositivo gemelo y una aplicación de dispositivo simulado que notifica su canal de conectividad como *propiedad notificada* en el dispositivo gemelo.
* Consultar dispositivos desde la aplicación de back-end mediante filtros en las etiquetas y propiedades que se han creado anteriormente.

<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md