---
title: Dispositivos gemelos de Azure IoT Hub | Microsoft Docs
description: "Guía para desarrolladores: uso de dispositivos gemelos para sincronizar los datos de estado y configuración entre IoT Hub y sus dispositivos"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 8a3da072-a5bf-46e5-8de4-24cdbb2a03fa
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3b2b2877efe5f898b5759c03ac0ddcf3ecc03901
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Dispositivos gemelos en IoT Hub

Los *dispositivos gemelos* son documentos JSON que almacenan información acerca del estado del dispositivo, incluidos metadatos, configuraciones y condiciones. Azure IoT Hub mantiene un dispositivo gemelo para cada dispositivo que se conecta a IoT Hub. En este artículo se describe:


* La estructura del dispositivo gemelo: *etiquetas*, las propiedades *deseadas* y las *notificadas*.
* Las operaciones que las aplicaciones de dispositivo y los back-ends pueden realizar en los dispositivos gemelos.

Use los dispositivos gemelos para:

* Almacenar metadatos específicos de dispositivo en la nube. Por ejemplo, la ubicación de implementación de una máquina expendedora.
* Notificar la información sobre el estado actual, como las funcionalidades y las condiciones disponibles de la aplicación del dispositivo. Por ejemplo, un dispositivo está conectado con su instancia de IoT Hub a través de un móvil o de WiFi.
* Sincronizar el estado de flujos de trabajo de larga duración entre la aplicación del dispositivo y el back-end. Por ejemplo, cuando el back-end de la solución especifica la nueva versión de firmware que se va a instalar y la aplicación de dispositivo informa de las distintas fases del proceso de actualización.
* Consultar los metadatos, la configuración o el estado del dispositivo.

Para obtener instrucciones sobre el uso de propiedades notificadas, mensajes de dispositivo a nube o carga de archivos, consulte [Guía de comunicación de dispositivo a nube][lnk-d2c-guidance].
Para obtener instrucciones sobre el uso de propiedades deseadas, los métodos directos o los mensajes de nube a dispositivo, consulte [Guía de comunicación de nube a dispositivo][lnk-c2d-guidance].

## <a name="device-twins"></a>Dispositivos gemelos
Los dispositivos gemelos almacenan información relacionada con el dispositivo que:

* El dispositivo y el back end pueden usar para sincronizar la configuración y las condiciones del dispositivo.
* El back-end de la solución se puede usar para consultar e identificar operaciones de larga duración.

El ciclo de vida de un dispositivo gemelo está vinculado a la [identidad del dispositivo][lnk-identity] correspondiente. Los dispositivos gemelos se crean y se eliminan implícitamente cuando se crea o se elimina una identidad de dispositivo en IoT Hub.

Un dispositivo gemelo es un documento JSON que incluye:

* **Etiquetas**. Una sección del documento JSON en la que el back-end de solución puede leer y escribir. Las etiquetas no son visibles para las aplicaciones de dispositivo.
* **Propiedades deseadas**. Se usan en conjunción con las propiedades notificadas para sincronizar la configuración o la condición del dispositivo. El back-end de solución puede establecer propiedades deseadas, y la aplicación de dispositivo puede leerlas. La aplicación de dispositivo también puede recibir notificaciones de los cambios en las propiedades deseadas.
* **Propiedades notificadas**. Se usan en conjunción con las propiedades deseadas para sincronizar la configuración o la condición del dispositivo. La aplicación de dispositivo puede establecer propiedades notificadas, y el back-end de solución puede leerlas y consultarlas.
* **Propiedades de identidad del dispositivo**. La raíz del documento JSON del dispositivo gemelo contiene las propiedades de solo lectura de la identidad de dispositivo correspondiente, almacenadas en el [registro de identidad de dispositivo][lnk-identity].

![][img-twin]

En el ejemplo siguiente se muestra un documento JSON del dispositivo gemelo:

        {
            "deviceId": "devA",
            "etag": "AAAAAAAAAAc=", 
            "status": "enabled",
            "statusReason": "provisioned",
            "statusUpdateTime": "0001-01-01T00:00:00",
            "connectionState": "connected",
            "lastActivityTime": "2015-02-30T16:24:48.789Z",
            "cloudToDeviceMessageCount": 0, 
            "authenticationType": "sas",
            "x509Thumbprint": {     
                "primaryThumbprint": null, 
                "secondaryThumbprint": null 
            }, 
            "version": 2, 
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

En el objeto raíz están las propiedades de identidad del sistema y los objetos de contenedor para `tags` y las propiedades `reported` y `desired`. El contenedor `properties` incluye algunos elementos de solo lectura (`$metadata`, `$etag` y `$version`) descritos en las secciones [Metadatos de dispositivo gemelo][lnk-twin-metadata] y [Simultaneidad optimista][lnk-concurrency].

### <a name="reported-property-example"></a>Ejemplo de propiedad notificada
En el ejemplo anterior, el dispositivo gemelo contiene una propiedad `batteryLevel` notificada por la aplicación de dispositivo. Esta propiedad permite consultar y operar en los dispositivos en función del último nivel de batería notificado. Otros ejemplos incluyen la aplicación de dispositivo notificando las funcionalidades del dispositivo o las opciones de conectividad.

> [!NOTE]
> Las propiedades notificadas simplifican los escenarios donde el back-end de la solución está interesado en el último valor conocido de una propiedad. Use [mensajes de dispositivo a nube][lnk-d2c] si el back-end de la solución debe procesar la telemetría del dispositivo en forma de secuencias de eventos con marca de tiempo, por ejemplo, series temporales.

### <a name="desired-property-example"></a>Ejemplo de propiedad deseada
En el ejemplo anterior, el back-end de la solución y la aplicación de dispositivo usan las propiedades deseadas y notificadas del dispositivo gemelo `telemetryConfig` para sincronizar la configuración de telemetría de este dispositivo. Por ejemplo: 

1. El back-end de la solución establece la propiedad deseada con el valor de configuración deseado. Esta es la parte del documento con el conjunto de propiedad deseada:
   
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
3. El back-end de la solución puede realizar un seguimiento de los resultados de la operación de configuración en varios dispositivos; para ello, [consulta][lnk-query] los dispositivos gemelos.

> [!NOTE]
> Los fragmentos de código anteriores son ejemplos, optimizados para mejorar su legibilidad, de una manera de codificar una configuración de dispositivo y su estado. IoT Hub no impone un esquema específico para las propiedades deseadas y notificadas en los dispositivos gemelos.
> 
> 

Se pueden usar los dispositivos gemelos para sincronizar operaciones de larga duración, como las actualizaciones de firmware. Para más información sobre cómo usar las propiedades para sincronizar y realizar un seguimiento de las operaciones de larga duración en todos los dispositivo consulte [Uso de las propiedades deseadas para configurar dispositivos][lnk-twin-properties].

## <a name="back-end-operations"></a>Operaciones de back-end
Para trabajar en el back-end de la solución, el dispositivo gemelo usa las siguientes operaciones atómicas expuestas mediante HTTPS:

* **Recuperación del dispositivo gemelo por el id**. Esta operación devuelve el documento del dispositivo gemelo, incluidas las etiquetas y las propiedades del sistema, deseadas y notificadas.
* **Actualización parcial de los dispositivos gemelos**. Esta operación permite que el back-end de la solución actualice parcialmente las etiquetas o las propiedades deseadas del dispositivo gemelo. La actualización parcial se expresa como un documento JSON que agrega o actualiza cualquier propiedad. Las propiedades establecidas en `null` se quitan. El ejemplo siguiente crea una nueva propiedad deseada con el valor `{"newProperty": "newValue"}`, sobrescribe el valor existente de `existingProperty` con `"otherNewValue"`, y quita `otherOldProperty`. No se realiza ningún cambio en otras etiquetas o propiedades deseadas existentes:
   
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
* **Reemplazar propiedades deseadas**. Esta operación permite que el back-end de la solución sobrescriba completamente todas las propiedades deseadas y sustituya un nuevo documento JSON para `properties/desired`.
* **Reemplazar etiquetas**. Esta operación permite que el back-end de la solución sobrescriba completamente todas las etiquetas y sustituya un nuevo documento JSON para `tags`.
* **Recibir notificaciones gemelas**. Esta operación permite que el back-end de la solución reciba una notificación cuando se modifique la gemela. Para ello, la solución de IoT debe crear una ruta y establecer el origen de datos igual a *twinChangeEvents*. De forma predeterminada, no se envían notificaciones gemelas, es decir, no existen previamente tales rutas. Si la tasa de cambio es demasiado alta, o por otras razones, como errores internos, IoT Hub podría enviar una sola notificación que contiene todos los cambios. Por lo tanto, si la aplicación necesita registro y auditoría confiables de todos los estados intermedios, la recomendación sigue siendo usar mensajes D2C. El mensaje de notificaciones gemelas incluye propiedades y el cuerpo.

    - Properties (Propiedades)

    | NOMBRE | Valor |
    | --- | --- |
    $content-type | application/json |
    $iothub-enqueuedtime |  Hora de envío de la notificación |
    $iothub-message-source | twinChangeEvents |
    $content-encoding | utf-8 |
    deviceId | Id. del dispositivo |
    hubName | Nombre de IoT Hub |
    operationTimestamp | Marca de tiempo [ISO8601] de operación |
    iothub-message-schema | deviceLifecycleNotification |
    opType | "replaceTwin" o "updateTwin" |

    Las propiedades del sistema de mensajes tienen como prefijo el símbolo `'$'`.

    - Cuerpo
        
    Esta sección incluye todos los cambios gemelos en formato JSON. Se usa el mismo formato que una revisión, con la diferencia de que puede contener todas las secciones gemelas: etiquetas, propiedades notificadas, propiedades deseadas, y que contiene los elementos "$metadata". Por ejemplo,
    ```
    {
        "properties": {
            "desired": {
                "$metadata": {
                    "$lastUpdated": "2016-02-30T16:24:48.789Z"
                },
                "$version": 1
            },
            "reported": {
                "$metadata": {
                    "$lastUpdated": "2016-02-30T16:24:48.789Z"
                },
                "$version": 1
            }
        }
    }
    ``` 

Todas las operaciones anteriores admiten la [simultaneidad optimista][lnk-concurrency] y requieren el permiso **ServiceConnect**, tal y como se define en el artículo sobre [seguridad][lnk-security].
 
Además de estas operaciones, el back-end de soluciones puede hacer lo siguiente:

* Consultar los dispositivos gemelos mediante el [lenguaje de consultas de IoT Hub][lnk-query] del tipo SQL.
* Realizar operaciones en conjuntos grandes de dispositivos gemelos usando [trabajos][lnk-jobs].

## <a name="device-operations"></a>Operaciones de dispositivo
La aplicación de dispositivo aplica las siguientes operaciones atómicas en el dispositivo gemelo:

* **Recuperación del dispositivo gemelo**. Esta operación devuelve el documento del dispositivo gemelo (incluidas las etiquetas y las propiedades del sistema, deseadas y notificadas) para el dispositivo conectado actualmente.
* **Actualizar parcialmente propiedades notificadas**. Esta operación permite la actualización parcial de las propiedades notificadas del dispositivo conectado actualmente. Esta operación usa el mismo formato de actualización JSON que el back-end de la solución usa para una actualización parcial de propiedades deseadas.
* **Observar las propiedades deseadas**. El dispositivo conectado actualmente puede elegir recibir notificaciones de las actualizaciones de las propiedades deseadas cuando se produzcan. El dispositivo recibe la misma forma de actualización (sustitución parcial o completa) ejecutada por el back-end de la solución.

Todas las operaciones anteriores requieren el permiso **DeviceConnect**, tal y como se define en el artículo sobre [seguridad][lnk-security].

Los [SDK de dispositivos IoT de Azure][lnk-sdks] permiten usar fácilmente las operaciones anteriores desde numerosos lenguajes y plataformas. Para obtener más información detallada sobre los tipos primitivos de IoT Hub para la sincronización de propiedades deseadas, consulte [Flujo de reconexión de dispositivos][lnk-reconnection].

## <a name="tags-and-properties-format"></a>Formato de etiquetas y propiedades
Las etiquetas y las propiedades deseadas y notificadas son objetos JSON con las siguientes restricciones:

* Todas las claves en objetos JSON son cadenas UNICODE UTF-8 de 64 caracteres que distinguen entre mayúsculas y minúsculas. Entre los caracteres permitidos no se incluyen los caracteres de control UNICODE (segmentos C0 y C1), ni `'.'`, `' '` y `'$'`.
* Todos los valores en un objeto JSON pueden ser de los siguientes tipos JSON: booleano, número, cadena, objeto. No se permiten matrices. El valor máximo de enteros es 4503599627370495 y el valor mínimo de enteros es-4503599627370496.
* Todos los objetos JSON en etiquetas y propiedades deseadas y notificadas pueden tener una profundidad máxima de 5. Por ejemplo, el objeto siguiente es válido:

        {
            ...
            "tags": {
                "one": {
                    "two": {
                        "three": {
                            "four": {
                                "five": {
                                    "property": "value"
                                }
                            }
                        }
                    }
                }
            },
            ...
        }

* Todos los valores de cadena pueden tener una longitud de 4 KB como máximo.

## <a name="device-twin-size"></a>Tamaño del dispositivo gemelo
IoT Hub impone un límite de tamaño de 8 KB en cada uno de los valores totales respectivos `tags`, `properties/desired` y `properties/reported`, excepto los elementos de solo lectura.
El tamaño se calcula contando todos los caracteres, excepto los caracteres de control UNICODE (segmentos C0 y C1) y los espacios que se encuentran fuera de las constantes de cadena.
IoT Hub rechaza con un error todas las operaciones que podrían aumentar el tamaño de los documentos por encima del límite.

## <a name="device-twin-metadata"></a>Metadatos de dispositivo gemelo
IoT Hub conserva la marca de tiempo de la última actualización para cada objeto JSON en las propiedades deseadas y notificadas del dispositivo gemelo. Las marcas de tiempo están en formato UTC y se codifican con el formato [ISO8601]`YYYY-MM-DDTHH:MM:SS.mmmZ`.
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

## <a name="optimistic-concurrency"></a>Simultaneidad optimista
Tanto las etiquetas como las propiedades deseadas y notificadas admiten la simultaneidad optimista.
Las etiquetas tienen una ETag, según la norma [RFC7232], que es la representación JSON de la etiqueta. Puede usar ETags en operaciones de actualización condicional desde el back-end de la solución para garantizar la coherencia.

Las propiedades deseadas y notificadas no tienen ETags, pero tienen un valor `$version` que se garantiza que será incremental. De forma similar a una ETag, la parte que realiza la actualización puede usar la versión para garantizar la coherencia de las actualizaciones. Por ejemplo, una aplicación de dispositivo para una propiedad notificada o la solución de back-end para una propiedad deseada.

Las versiones también son útiles cuando un agente de observación (por ejemplo, la aplicación de dispositivo que observa las propiedades deseadas) tiene que conciliar las carreras entre el resultado de una operación de recuperación y una notificación de actualización. En la sección [Flujo de reconexión de dispositivos][lnk-reconnection] se proporciona más información.

## <a name="device-reconnection-flow"></a>Flujo de reconexión de dispositivos
IoT Hub no conserva las notificaciones de actualización de las propiedades deseadas para los dispositivos desconectados. Se supone que un dispositivo que se está conectando debe recuperar el documento con todas las propiedades deseadas, además de suscribirse a las notificaciones de actualización. Dada la posibilidad de carreras entre las notificaciones de actualización y la recuperación completa, se debe asegurar el flujo siguiente:

1. La aplicación de dispositivo se conecta a un centro de IoT.
2. La aplicación de dispositivo se suscribe a las notificaciones de actualización de las propiedades deseadas.
3. La aplicación de dispositivo recupera el documento completo de las propiedades deseadas.

La aplicación de dispositivo puede pasar por alto todas las notificaciones cuyo valor de `$version` sea anterior o igual a la versión del documento completo recuperado. Este enfoque es posible porque IoT Hub garantiza que las versiones siempre se incrementan.

> [!NOTE]
> Esta lógica ya se implementó en los [SDK de dispositivo IoT de Azure][lnk-sdks]. Esta descripción es útil solo si la aplicación del dispositivo no puede usar ninguno de los SDK de dispositivos de IoT de Azure y debe programar directamente la interfaz MQTT.
> 
> 

## <a name="additional-reference-material"></a>Material de referencia adicional
Otros temas de referencia en la guía del desarrollador de IoT Hub son los siguientes:

* En el artículo [Puntos de conexión de IoT Hub][lnk-endpoints] se describen los diferentes puntos de conexión que expone cada centro de IoT Hub para las operaciones en tiempo de ejecución y de administración.
* En el artículo [Cuotas y limitación][lnk-quotas], se describen las cuotas que se aplican al servicio IoT Hub y el comportamiento de limitación esperado al usar el servicio.
* En el artículo sobre [SDK de dispositivos y servicio de Azure IoT][lnk-sdks] se muestran los SDK de los diferentes lenguajes que puede usar para desarrollar aplicaciones para dispositivo y de servicio que interactúen con IoT Hub.
* En el artículo [Referencia: Lenguaje de consulta de IoT Hub para dispositivos gemelos, trabajos y enrutamiento de mensajes][lnk-query], se describe el lenguaje de consulta de IoT Hub que se puede usar para recuperar información de IoT Hub sobre los dispositivos gemelos y trabajos.
* En el artículo sobre la [compatibilidad con MQTT de IoT Hub][lnk-devguide-mqtt], se proporciona más información sobre la compatibilidad de IoT Hub con el protocolo MQTT.

## <a name="next-steps"></a>pasos siguientes
Ahora que ya conoce algo más sobre los dispositivos gemelos, quizás le interesen los siguientes temas de la guía para desarrolladores de IoT Hub:

* [Invocación de un método directo en un dispositivo][lnk-methods]
* [Programación de trabajos en varios dispositivos][lnk-jobs]

Si desea probar algunos de los conceptos descritos en este artículo, puede interesarle los siguientes tutoriales de IoT Hub:

* [Uso de dispositivos gemelo][lnk-twin-tutorial]
* [Uso de propiedades de dispositivos gemelos][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#device-twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png
