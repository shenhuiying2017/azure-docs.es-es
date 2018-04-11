> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-how-to-configure.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-how-to-configure.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-twin-how-to-configure.md)
> 
> 

## <a name="introduction"></a>Introducción

En [Introducción a los dispositivos gemelos de IoT Hub][lnk-twin-tutorial], aprendió a establecer metadatos de dispositivo en *etiquetas*. Recibió condiciones de dispositivo de una aplicación de dispositivo mediante *propiedades notificadas* y, a continuación, consultó esta información mediante un lenguaje similar a SQL.

En este tutorial se describe cómo usar las *propiedades deseadas* del dispositivo gemelo, además de las *propiedades notificadas*, para configurar aplicaciones de dispositivo de forma remota. Las propiedades notificadas y deseadas de un dispositivo gemelo permiten configurar una aplicación de dispositivo en varios pasos, y proporcionan visibilidad sobre el estado de esta operación en todos los dispositivos. Puede encontrar más información sobre el rol de las configuraciones de dispositivo en [Información general sobre la administración de dispositivos con IoT Hub][lnk-dm-overview].

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

De forma general, el uso de dispositivos gemelos permite que el back-end de la solución especifique la configuración deseada para los dispositivos administrados, en lugar de enviar comandos específicos. Se encarga al dispositivo que establezca la mejor manera de actualizar su configuración (algo importante en los escenarios de IoT en los que las condiciones específicas del dispositivo afectan a la capacidad de ejecutar comandos específicos de manera inmediata), al mismo tiempo que envía de forma continua al back-end de la solución notificaciones sobre el estado actual y las posibles condiciones de error del proceso de actualización. Este patrón es fundamental para la administración de grandes conjuntos de dispositivos, ya que proporciona al back-end de la solución una visibilidad completa del estado del proceso de configuración en todos los dispositivos.

> [!TIP]
> En aquellos escenarios en los que los dispositivos se controlen de forma más interactiva (por ejemplo, activar un ventilador desde una aplicación controlada por el usuario), considere la posibilidad de usar [métodos directos][lnk-methods].

En este tutorial, el back-end de la solución cambia la configuración de telemetría de un dispositivo de destino de forma que la aplicación de dispositivo aplique una actualización de la configuración. Por ejemplo, una actualización de la configuración requeriría el reinicio de un módulo de software, algo que simula este tutorial con un simple retraso.

El back-end de la solución almacena la configuración en las propiedades deseadas del dispositivo gemelo como se indica a continuación:

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

Dado que las configuraciones pueden ser objetos complejos, se les asignan identificadores únicos (hashes o [GUID][lnk-guid]).


La aplicación del dispositivo notifica su configuración actual, para lo que crea un reflejo de la propiedad deseada **telemetryConfig** en las propiedades notificadas:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "configId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

Tenga en cuenta que la propiedad **telemetryConfig** notificada tiene un **estado** de propiedad adicional, que se usa para notificar el estado del proceso de actualización de la configuración.

Cuando se recibe una nueva configuración deseada, la aplicación del dispositivo notifica que hay una configuración pendiente, para lo que cambia el estado:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "configId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "configId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

Posteriormente, la aplicación del dispositivo le indica el acierto o error de esta operación mediante la actualización de la propiedad. El back-end de la solución puede consultar el estado del proceso de configuración de todos los dispositivos en cualquier momento.

En este tutorial se muestra cómo realizar las siguientes acciones:

* Crear una aplicación de dispositivo simulado que reciba las actualizaciones de configuración del back-end de la solución y notifique varias actualizaciones como *propiedades notificadas* en el proceso de actualización de la configuración.
* Crear una aplicación de back-end que actualice la configuración deseada de un dispositivo y, a continuación, consulte el proceso de actualización de la configuración.

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier
