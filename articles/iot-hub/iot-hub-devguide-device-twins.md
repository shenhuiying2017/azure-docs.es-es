<properties
 pageTitle="Guía para desarrolladores: introducción a los dispositivos gemelos | Microsoft Azure"
 description="Guía para desarrolladores de Azure IoT Hub: use dispositivos gemelos para sincronizar el estado y los datos de configuración entre IoT Hub y sus dispositivos."
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>


# <a name="understand-device-twins-preview"></a>Introducción a los dispositivos gemelos (versión preliminar)

## <a name="overview"></a>Información general

Los *dispositivos gemelos* son documentos JSON que almacenan información sobre el estado de los dispositivos (metadatos, configuraciones y condiciones). IoT Hub conserva un dispositivo gemelo por cada dispositivo que se conecta a IoT Hub. En este artículo se describe:

* la estructura del dispositivo gemelo: *etiquetas*, las propiedades *deseadas* y *notificadas*, y
* las operaciones que las aplicaciones de dispositivo y los back-ends pueden realizar en los dispositivos gemelos.

> [AZURE.NOTE] En la actualidad, solo se puede acceder a los dispositivos gemelos desde dispositivos conectados a IoT Hub mediante el protocolo MQTT. Para instrucciones acerca de cómo convertir la aplicación de dispositivo existente para usar MQTT, consulte el artículo sobre [compatibilidad con MQTT][lnk-devguide-mqtt].

### <a name="when-to-use"></a>Cuándo se debe usar

Use los dispositivos gemelos para:

* Almacenar datos de metadatos específicos de un dispositivo en la nube, por ejemplo, la ubicación de implementación de una máquina expendedora.
* Notificar la información sobre el estado actual como las funcionalidades disponibles y las condiciones de la aplicación de dispositivo, por ejemplo, un dispositivo que se conecta mediante un operador de telefonía móvil o Wi-Fi.
* Sincronizar el estado de los flujos de trabajo de larga duración entre la aplicación de dispositivo y el back-end, por ejemplo, el back-end especifica la nueva versión de firmware que se va a instalar y la aplicación de dispositivo informa de las distintas fases del proceso de actualización.
* Consultar los metadatos, la configuración o el estado del dispositivo.

Use [mensajes de dispositivo a nube][lnk-d2c] para las secuencias de eventos con marca de tiempo, por ejemplo, series temporales de datos de sensores o alarmas. Use [métodos de nube a dispositivo][lnk-methods] para el control interactivo de dispositivos, como la activación de un ventilador.

## <a name="device-twins"></a>Dispositivos gemelos

Los dispositivos gemelos almacenan información relacionada con el dispositivo que:

- El dispositivo y el back end pueden usar para sincronizar la configuración y las condiciones del dispositivo.
- El back-end de la aplicación puede usar para consultar e identificar operaciones de larga duración.

El ciclo de vida de un dispositivo gemelo está vinculado a la [identidad de dispositivo][lnk-identity] correspondiente. Los dispositivos gemelos se crean y se eliminan implícitamente cuando se crea o se elimina una nueva identidad de dispositivo en IoT Hub.

Un dispositivo gemelo es un documento JSON que incluye:

* **Etiquetas**. Un documento JSON que el back-end lee y escribe. Las etiquetas no son visibles para las aplicaciones de dispositivo.
* **Propiedades deseadas**. Se usan junto con propiedades notificadas para sincronizar la configuración o la condición del dispositivo. Solo el back-end puede establecer las propiedades deseadas y solo la aplicación de dispositivo las puede leer. La aplicación de dispositivo también puede recibir notificaciones en tiempo real de los cambios en las propiedades deseadas.
* **Propiedades notificadas**. Se usan junto con las propiedades deseadas para sincronizar la configuración o la condición del dispositivo. Solo la aplicación de dispositivo puede establecer las propiedades deseadas y solo el back-end las puede consultar.

Además, la raíz del dispositivo gemelo contiene las propiedades de solo lectura de la identidad correspondiente, contenidas en el [registro de identidad de dispositivo][lnk-identity].

![][img-twin]

Este es un ejemplo de un documento JSON de dispositivo gemelo:

        {
            "deviceId": "devA",
            "generationId": "123",
            "status": "enabled",
            "statusReason": "provisioned",
            "connectionState": "connected",
            "connectionStateUpdatedTime": "2015-02-28T16:24:48.789Z",
            "lastActivityTime": "2015-02-30T16:24:48.789Z",

            "tags": {
                "$etag": "123",
                "deploymentLocation": {
                    "building": "43",
                    "floor": "1"
                }
            },
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata" : {...},
                    "$version": 1
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": 55,
                    "$metadata" : {...},
                    "$version": 4
                }
            }
        }

En el objeto raíz, están las propiedades del sistema y los objetos de contenedor para `tags`, `reported` y `desired properties`. El contenedor `properties` incluye algunos elementos de solo lectura (`$metadata`, `$etag` y `$version`) que se describen en respectivamente en las secciones [Metadatos de dispositivo gemelo][lnk-twin-metadata] y [Simultaneidad optimista][lnk-concurrency].

### <a name="reported-property-example"></a>Ejemplo de propiedad notificada

En el ejemplo anterior, el dispositivo gemelo contiene una propiedad `batteryLevel` notificada por la aplicación de dispositivo. Esta propiedad permite consultar y operar en los dispositivos en función del último nivel de batería notificado. En otro ejemplo, la aplicación de dispositivo notificaría las funcionalidades del dispositivo o las opciones de conectividad.

Observe cómo las propiedades notificadas simplifican los escenarios donde el back-end está interesado en el último valor conocido de una propiedad. Use [mensajes de dispositivo a nube][lnk-d2c] si el back-end debe procesar la telemetría del dispositivo en forma de secuencias de eventos con marca de tiempo, por ejemplo, series temporales.

### <a name="desired-configuration-example"></a>Ejemplo de configuración deseada

En el ejemplo anterior, el back-end y la aplicación de dispositivo usan las propiedades deseada y notificada `telemetryConfig` para sincronizar la configuración de la telemetría para este dispositivo. Por ejemplo:

1. El back-end de la aplicación establece la propiedad deseada con el valor de configuración deseado. Esta es la parte del documento con la propiedad deseada:

        ...
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            ...
        },
        ...
        
2. La aplicación de dispositivo recibe una notificación del cambio inmediatamente si está conectado, o en cuanto vuelva a conectarse. Después, la aplicación de dispositivo notifica la configuración actualizada (o una condición de error mediante la propiedad `status`. Esta es la parte de las propiedades notificadas:

        ...
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            ...
        }
        ...

3. El back-end de la aplicación puede realizar un seguimiento de los resultados de la operación de configuración en varios dispositivos; para ello, [consulta][lnk-query] los dispositivos gemelos.

> [AZURE.NOTE] Los fragmentos de código anteriores son ejemplos, optimizados para mejorar su legibilidad, de una posible manera de codificar una configuración de dispositivo y su estado. IoT Hub no impone un esquema específico para las propiedades deseadas y notificadas en los dispositivos gemelos.

En muchos casos, los dispositivos gemelos se usan para sincronizar operaciones de larga duración, como las actualizaciones de firmware. Consulte [Uso de propiedades deseadas para configurar dispositivos][lnk-twin-properties] para más información sobre cómo usar las propiedades para sincronizar y realizar un seguimiento de las operaciones de larga duración en todos los dispositivos.

## <a name="back-end-operations"></a>Operaciones de back-end

Para trabajar en el back-end, el dispositivo gemelo usa las siguientes operaciones atómicas mediante HTTP:

1. **Recuperar dispositivo gemelo por identificador**. Esta operación devuelve el contenido del documento del dispositivo gemelo, incluidas las etiquetas y las propiedades del sistema, deseadas y notificadas.
2. **Actualizar dispositivo gemelo parcialmente**. Esta operación permite que el back-end actualice parcialmente las etiquetas o las propiedades deseadas del dispositivo gemelo. La actualización parcial se expresa como un documento JSON que agrega o actualiza cualquier propiedad mencionada. Las propiedades establecidas en `null` se quitan. Por ejemplo, lo siguiente crea una nueva propiedad deseada con el valor `{"newProperty": "newValue"}`, sobrescribe el valor existente de `existingProperty` con `"otherNewValue"`, y quita completamente `otherOldProperty`. No se realiza ningún cambio en otras etiquetas o propiedades deseadas existentes:

        {
            "properties": {
                "desired": {
                    "newProperty": {
                        "nestedProperty": "newValue"
                    },
                    "existingProperty": "otherNewValue",
                    "otherOldProperty": null
                }
            }
        }

3. **Reemplazar propiedades deseadas**. Esta operación permite que el back-end sobrescriba completamente todas las propiedades deseadas y sustituya un nuevo documento JSON para `properties/desired`.
4. **Reemplazar etiquetas**. De forma similar al reemplazo de las propiedades deseadas, esta operación permite que el back-end sobrescriba completamente todas las etiquetas existentes y sustituya un nuevo documento JSON para `tags`.

Todas las operaciones anteriores admiten la [simultaneidad optimista][lnk-concurrency] y requieren el permiso **ServiceConnect**, tal y como se define en el artículo [Seguridad][lnk-security].

Además de estas operaciones, el back-end puede consultar los dispositivos gemelos con un [lenguaje de consulta][lnk-query] de tipo SQL, y realizar operaciones en conjuntos grandes de dispositivos gemelos mediante [trabajos][lnk-jobs].

## <a name="device-operations"></a>Operaciones de dispositivo

La aplicación de dispositivo aplica las siguientes operaciones atómicas en el dispositivo gemelo:

1. **Recuperar gemelo**. Esta operación devuelve el contenido del documento del dispositivo gemelo (incluidas las etiquetas y las propiedades del sistema, deseadas y notificadas) para el dispositivo conectado actualmente.
2. **Actualizar parcialmente propiedades notificadas**. Esta operación permite la actualización parcial de las propiedades notificadas del dispositivo conectado actualmente. Utiliza el mismo formato de actualización JSON que el back-end cuyas propiedades deseadas se van a actualizar parcialmente.
3. **Observar las propiedades deseadas**. El dispositivo conectado actualmente puede elegir recibir notificaciones de las actualizaciones de las propiedades deseadas en cuanto se produzcan. El dispositivo recibe la misma forma de actualización (sustitución parcial o completa) ejecutada por el back-end.

Todas las operaciones anteriores requieren el permiso **DeviceConnect**, tal y como se define en el artículo [Seguridad][lnk-security].

Los [SDK de dispositivo IoT de Azure][lnk-sdks] permiten usar fácilmente las operaciones anteriores desde numerosos lenguajes y plataformas. Encontrará más información detallada sobre los tipos primitivos de IoT Hub para la sincronización de propiedades deseadas en [Flujo de reconexión de dispositivos][lnk-reconnection].

> [AZURE.NOTE] Actualmente, los dispositivos gemelos solo son accesibles desde los dispositivos que se conectan a IoT Hub mediante el protocolo MQTT.

## <a name="reference"></a>Referencia

### <a name="tags-and-properties-format"></a>Formato de etiquetas y propiedades

Las etiquetas y las propiedades deseadas y notificadas son objetos JSON con las siguientes restricciones:

* Todas las claves en objetos JSON son cadenas UNICODE de 128 caracteres que distinguen entre mayúsculas y minúsculas. Entre los caracteres permitidos no se incluyen los caracteres de control UNICODE (segmentos C0 y C1), ni `'.'`, `' '` y `'$'`.
* Todos los valores en un objeto JSON pueden ser de los siguientes tipos JSON: booleano, número, cadena, objeto. No se permiten matrices.

### <a name="twin-size"></a>Tamaño de dispositivo gemelo

IoT Hub impone un límite de tamaño de 8 KB en los valores de `tags`, `properties/desired` y `properties/reported`, excepto los elementos de solo lectura.
El tamaño se calcula contando todos los caracteres, excepto los caracteres de control UNICODE (segmentos C0 y C1) y el espacio `' '` cuando aparece fuera de una constante de cadena.
IoT Hub rechazará con error todas las operaciones que podrían aumentar el tamaño de los documentos por encima del límite.

### <a name="twin-metadata"></a>Metadatos de dispositivos gemelos

IoT Hub conserva la marca de tiempo de la última actualización para cada objeto JSON en las propiedades deseadas y notificadas. Las marcas de tiempo están en formato UTC y se codifican con el formato [ISO8601]`YYYY-MM-DDTHH:MM:SS.mmmZ`.
Por ejemplo:

        {
            ...
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": {
                                "$lastUpdated": "2016-03-30T16:24:48.789Z"
                            },
                            "$lastUpdated": "2016-03-30T16:24:48.789Z"
                        },
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$version": 23
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": "55%",
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": "5m",
                            "status": {
                                "$lastUpdated": "2016-03-31T16:35:48.789Z"
                            },
                            "$lastUpdated": "2016-03-31T16:35:48.789Z"
                        }
                        "batteryLevel": {
                            "$lastUpdated": "2016-04-01T16:35:48.789Z"
                        },
                        "$lastUpdated": "2016-04-01T16:24:48.789Z"
                    },
                    "$version": 123
                }
            }
            ...
        }

Esta información se conserva en todo los niveles (no solo en las hojas de la estructura JSON) para conservar las actualizaciones que quitan las claves de objeto.

### <a name="optimistic-concurrency"></a>Simultaneidad optimista

Tanto las etiquetas como las propiedades deseadas y notificadas admiten la simultaneidad optimista.
Las etiquetas tienen una etag, según la norma [RFC7232], que es la representación JSON de la etiqueta. Puede usarla en operaciones de actualización condicional desde el back-end para garantizar la coherencia.

Las propiedades deseadas y notificadas no tienen etags, pero tienen un valor `$version` que se garantiza que será incremental. De forma análoga a las etags, la parte que realiza la actualización (una aplicación de dispositivo para una propiedad notificada o el back-end para una propiedad deseada) puede utilizar la versión para garantizar la coherencia de las actualizaciones.

Las versiones también son útiles cuando un agente de observación (por ejemplo, la aplicación de dispositivo que observa las propiedades deseadas) tiene que conciliar las carreras entre el resultado de una operación de recuperación y una notificación de actualización. La sección [Flujo de reconexión de dispositivos][lnk-reconnection] proporciona más información.

### <a name="device-reconnection-flow"></a>Flujo de reconexión de dispositivos

IoT Hub no conserva las notificaciones de actualización de las propiedades deseadas para los dispositivos desconectados. Se supone que un dispositivo que se está conectando debe recuperar el documento con todas las propiedades deseadas, además de suscribirse a las notificaciones de actualización. Dada la posibilidad de carreras entre las notificaciones de actualización y la recuperación completa, se debe asegurar el flujo siguiente:

1. La aplicación de dispositivo se conecta a un centro de IoT.
2. La aplicación de dispositivo se suscribe a las notificaciones de actualización de las propiedades deseadas.
3. La aplicación de dispositivo recupera el documento completo de las propiedades deseadas.

La aplicación de dispositivo puede pasar por alto todas las notificaciones cuyo valor de `$version` sea anterior o igual a la versión del documento completo recuperado. Esto es posible porque IoT Hub garantiza que las versiones siempre se incrementan.

> [AZURE.NOTE] Esta lógica se implementó en los [SDK de dispositivo IoT de Azure][lnk-sdks]. Esta descripción es útil solo si la aplicación del dispositivo no puede usar ninguno de los SDK de dispositivos de IoT de Azure y debe programar directamente la interfaz MQTT.

### <a name="additional-reference-material"></a>Material de referencia adicional

Otros temas de referencia en la Guía para desarrolladores son:

- En [IoT Hub endpoints][lnk-endpoints] (Puntos de conexión de IoT Hub), se describen los diferentes puntos de conexión que expone cada centro de IoT para las operaciones en tiempo de ejecución y de administración.
- En [Throttling and quotas][lnk-quotas] (Limitación y cuotas), se describen las cuotas que se aplican al servicio de IoT Hub y el comportamiento de limitación que se espera al usar el servicio.
- En [IoT Hub device and service SDKs][lnk-sdks] (SDK de servicio y de dispositivo de IoT Hub) se muestran los diversos SDK de lenguaje que puede usar para desarrollar aplicaciones de dispositivo y de servicio que interactúen con IoT Hub.
- En [Lenguaje de consulta para dispositivos gemelos, métodos y trabajos][lnk-query], se describe el lenguaje de consulta que se puede usar para recuperar información desde IoT Hub sobre sus dispositivos gemelos, métodos y trabajos.
- En [Compatibilidad con MQTT de IoT Hub][lnk-devguide-mqtt], se proporciona más información acerca de la compatibilidad de IoT Hub con el protocolo MQTT.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ya conoce algo más sobre los dispositivos gemelos, quizás le interesen los siguientes temas de la Guía para desarrolladores:

- [Invocación de un método directo en un dispositivo][lnk-methods]
- [Schedule jobs on multiple devices][lnk-jobs] (Programación de trabajos en varios dispositivos)

Si desea probar algunos de los conceptos descritos en este artículo, pueden interesarle los siguientes tutoriales de IoT Hub:

- [Cómo utilizar los dispositivos gemelos][lnk-twin-tutorial]
- [Cómo utilizar las propiedades de los dispositivos gemelos][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png


<!--HONumber=Oct16_HO2-->


