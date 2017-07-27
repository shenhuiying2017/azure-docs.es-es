> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-how-to-configure.md)
> 
> 

## <a name="introduction"></a>Introducción

En [Introducción a los dispositivos gemelos de IoT Hub][lnk-twin-tutorial], ha aprendido a establecer metadatos del dispositivo desde el back-end de la solución mediante *etiquetas*, a informar de las condiciones del dispositivo desde una aplicación de dispositivo mediante *propiedades notificadas* y a consultar esta información con un lenguaje similar a SQL.

En este tutorial, aprenderá a usar las *propiedades deseadas* del dispositivo gemelo, además de las *propiedades notificadas*, para configurar aplicaciones de dispositivo de forma remota. Más concretamente, este tutorial muestra la forma en que las propiedades notificadas y deseadas de un dispositivo gemelo permiten configurar una aplicación de dispositivo en varios pasos, y proporcionan al back-end de la solución visibilidad sobre el estado de esta operación en todos los dispositivos. Puede encontrar más información sobre el rol de las configuraciones de dispositivo en [Información general sobre la administración de dispositivos con IoT Hub][lnk-dm-overview].

De forma general, el uso de dispositivos gemelos permite que el back-end de la solución especifique la configuración deseada para los dispositivos administrados, en lugar de enviar comandos específicos. De esta forma, se encarga al dispositivo que establezca la mejor manera de actualizar su configuración (algo muy importante en los escenarios de IoT en los que las condiciones específicas del dispositivo afectan a la capacidad de ejecutar comandos específicos de manera inmediata), al mismo tiempo que envía de forma continua al back-end de la solución notificaciones sobre el estado actual y las posibles condiciones de error del proceso de actualización. Este patrón es fundamental para la administración de grandes conjuntos de dispositivos, ya que permite que el back-end de la solución tenga una visibilidad completa del estado del proceso de configuración en todos los dispositivos.

> [!NOTE]
> En aquellos escenarios en los que los dispositivos se controlen de forma más interactiva (activar un ventilador desde una aplicación controlada por el usuario), considere la posibilidad de usar [métodos directos][lnk-methods].
> 
> 

En este tutorial, el back-end de la solución cambia la configuración de la telemetría de un dispositivo de destino y, como consecuencia, la aplicación del dispositivo sigue un proceso con varios pasos para aplicar una actualización de la configuración (por ejemplo, que requiera que se reinicie un módulo de software), algo que en este tutorial se simula con un simple retraso.

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

> [!NOTE]
> Dado que las configuraciones pueden ser objetos complejos, normalmente se les asignan identificadores únicos (hashes o [GUID][lnk-guid]) para simplificar las comparaciones.
> 
> 

La aplicación del dispositivo notifica su configuración actual, para lo que crea un reflejo de la propiedad deseada **telemetryConfig** en las propiedades notificadas:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

Tenga en cuenta que la propiedad **telemetryConfig** notificada tiene un **estado** de propiedad adicional, que se usa para notificar el estado del proceso de actualización de la configuración.

Cuando se recibe una nueva configuración deseada, la aplicación del dispositivo notifica que hay una configuración pendiente, para lo que cambia la información:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "changeId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

Posteriormente, la aplicación del dispositivo le indicará el acierto o error de esta operación mediante la actualización de la propiedad anterior.
Tenga en cuenta que el back-end de la solución puede, en cualquier momento, consultar el estado del proceso de configuración de todos los dispositivos.

En este tutorial se muestra cómo realizar las siguientes acciones:

* Crear una aplicación de dispositivo simulado que reciba las actualizaciones de configuración del back-end de la solución y notifique varias actualizaciones como *propiedades notificadas* en el proceso de actualización de la configuración.
* Crear una aplicación de back-end que actualice la configuración deseada de un dispositivo y, a continuación, consulte el proceso de actualización de la configuración.

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier
