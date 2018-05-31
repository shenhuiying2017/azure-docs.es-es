---
title: Módulos gemelos de Azure IoT Hub | Microsoft Docs
description: 'Guía para desarrolladores: uso de módulos gemelos para sincronizar los datos de estado y configuración entre IoT Hub y sus dispositivos'
services: iot-hub
documentationcenter: .net
author: chrissie926
manager: timlt
editor: ''
ms.assetid: 8a3da072-a5bf-46e5-8de4-24cdbb2a03fa
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: menchi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1e6f52da6f7524828cef928c2ee5d5cd92a1de1e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201081"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Uso de módulos gemelos en IoT Hub

En este artículo se da por supuesto que ha leído primero [Dispositivos gemelos en IoT Hub][lnk-devguide-device-twins]. En IoT Hub, en cada identidad de dispositivo, puede crear hasta 20 identidades de módulo. Cada identidad de módulo genera implícitamente un módulo gemelo. De forma muy parecida a los dispositivos gemelos, los módulos gemelos son documentos JSON que almacenan información acerca del estado del módulo, incluidos metadatos, configuraciones y condiciones. Azure IoT Hub mantiene un módulo gemelo para cada módulo que se conecta a IoT Hub. 

En el lado del dispositivo, los SDK de dispositivo de IoT Hub le permiten crear módulos cada uno de los cuales abre una conexión independiente a IoT Hub. Esto le permite usar espacios de nombres distintos para distintos componentes del dispositivo. Por ejemplo, tiene una máquina expendedora con tres sensores diferentes. Cada sensor se controla mediante diferentes departamentos de su empresa. Puede crear un módulo para cada sensor. De esta manera, cada departamento solo puede enviar trabajos o métodos directos al sensor que controlan, con lo que se evitan conflictos y errores de usuario.

 La identidad del módulo y el módulo gemelo proporcionan las mismas funcionalidades que la identidad del dispositivo y el dispositivo gemelo, pero de manera mucho más pormenorizada. Esto permite a los dispositivos compatibles, como dispositivos basados en el sistema operativo o dispositivos de firmware que administran varios componentes, aislar la configuración y las condiciones de cada uno de esos componentes. La identidad del módulo y el módulo gemelo proporcionan la separación administrativa de problemas al trabajar con dispositivos IoT que tienen componentes de software modulares. Nuestro objetivo es respaldar toda la funcionalidad de dispositivo gemelo en el nivel de módulo gemelo mediante la disponibilidad general del este. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

En este artículo se describe:

* La estructura del módulo gemelo: *etiquetas*, *propiedades deseadas* y *propiedades notificadas*.
* Las operaciones que pueden realizar los módulos y los servidores back-end en los módulos gemelos.

Para obtener instrucciones sobre el uso de propiedades notificadas, mensajes de dispositivo a nube o carga de archivos, consulte [Guía de comunicación de dispositivo a nube][lnk-d2c-guidance].
Para obtener instrucciones sobre el uso de propiedades deseadas, los métodos directos o los mensajes de nube a dispositivo, consulte [Guía de comunicación de nube a dispositivo][lnk-c2d-guidance].

## <a name="module-twins"></a>Módulos gemelos
Los módulos gemelos almacenan información relacionada con el módulo que:

* Los módulos del dispositivo e IoT Hub pueden usar para sincronizar la configuración y las condiciones del módulo.
* El back-end de la solución se puede usar para consultar e identificar operaciones de larga duración.

El ciclo de vida de un módulo gemelo está vinculado a la [identidad del módulo][lnk-identity] correspondiente. Los módulos gemelos se crean y se eliminan implícitamente cuando se crea o se elimina una identidad del módulo en IoT Hub.

Un módulo gemelo es un documento JSON que incluye:

* **Etiquetas**. Una sección del documento JSON en la que el back-end de solución puede leer y escribir. Las etiquetas no son visibles para los módulos en el dispositivo. Las etiquetas se establecen con fines de consulta.
* **Propiedades deseadas**. Se usan junto con las propiedades notificadas para sincronizar la configuración o las condiciones del módulo. El back-end de solución puede establecer propiedades deseadas, y la aplicación de módulo puede leerlas. La aplicación de módulo también puede recibir notificaciones de cambios en las propiedades deseadas.
* **Propiedades notificadas**. Se usan junto con las propiedades deseadas para sincronizar la configuración o las condiciones del módulo. La aplicación de módulo puede establecer propiedades notificadas, y el back-end de solución puede leerlas y consultarlas.
* **Propiedades de identidad del módulo**. La raíz del documento JSON del módulo gemelo contiene las propiedades de solo lectura de la identidad de módulo correspondiente, almacenadas en el [registro de identidades][lnk-identity].

![][img-module-twin]

En el ejemplo siguiente se muestra un documento JSON del módulo gemelo:

```json
{
    "deviceId": "devA",
    "moduleId": "moduleA",
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
```

En el objeto raíz están las propiedades de identidad del módulo y los objetos de contenedor para `tags` y las propiedades `reported` y `desired`. El contenedor `properties` incluye algunos elementos de solo lectura (`$metadata`, `$etag` y `$version`) descritos en las secciones [Metadatos de módulo gemelo][lnk-module-twin-metadata] y [Simultaneidad optimista][lnk-concurrency].

### <a name="reported-property-example"></a>Ejemplo de propiedad notificada
En el ejemplo anterior, el módulo gemelo contiene una propiedad `batteryLevel` notificada por la aplicación de módulo. Esta propiedad permite consultar y operar en los módulos en función del último nivel de batería notificado. Otros ejemplos incluyen la aplicación de módulo que notifica las funcionalidades del módulo o las opciones de conectividad.

> [!NOTE]
> Las propiedades notificadas simplifican los escenarios donde el back-end de la solución está interesado en el último valor conocido de una propiedad. Use [mensajes de dispositivo a nube][lnk-d2c] si el back-end de la solución debe procesar la telemetría del módulo en forma de secuencias de eventos con marca de tiempo, por ejemplo, series temporales.

### <a name="desired-property-example"></a>Ejemplo de propiedad deseada
En el ejemplo anterior, el back-end de la solución y la aplicación de módulo usan las propiedades deseadas y notificadas del módulo gemelo `telemetryConfig` para sincronizar la configuración de telemetría de este módulo. Por ejemplo: 

1. El back-end de la solución establece la propiedad deseada con el valor de configuración deseado. Esta es la parte del documento con el conjunto de propiedad deseada:

    ```json
    ...
    "desired": {
        "telemetryConfig": {
            "sendFrequency": "5m"
        },
        ...
    },
    ...
    ```

2. La aplicación de módulo recibe una notificación del cambio inmediatamente si está conectado, o en cuanto vuelva a conectarse. Después, la aplicación de módulo notifica la configuración actualizada (o una condición de error mediante la propiedad `status`). Esta es la parte de las propiedades notificadas:

    ```json
    ...
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ...
    ```

3. El back-end de la solución puede realizar un seguimiento de los resultados de la operación de configuración en varios módulos; para ello, [consulta][lnk-query] los módulos gemelos.

> [!NOTE]
> Los fragmentos de código anteriores son ejemplos, optimizados para mejorar su legibilidad, de una manera de codificar una configuración de módulo y su estado. IoT Hub no impone un esquema específico para las propiedades deseadas y notificadas en los módulos gemelos.
> 
> 

## <a name="back-end-operations"></a>Operaciones de back-end
Para trabajar en el back-end de la solución, el módulo gemelo usa las siguientes operaciones atómicas expuestas mediante HTTPS:

* **Recuperación del módulo gemelo por identificador**. Esta operación devuelve el documento del módulo gemelo, incluidas las etiquetas y las propiedades del sistema, deseadas y notificadas.
* **Actualización parcial del módulo gemelo**. Esta operación permite que el back-end de la solución actualice parcialmente las etiquetas o las propiedades deseadas del módulo gemelo. La actualización parcial se expresa como un documento JSON que agrega o actualiza cualquier propiedad. Las propiedades establecidas en `null` se quitan. El ejemplo siguiente crea una nueva propiedad deseada con el valor `{"newProperty": "newValue"}`, sobrescribe el valor existente de `existingProperty` con `"otherNewValue"`, y quita `otherOldProperty`. No se realiza ningún cambio en otras etiquetas o propiedades deseadas existentes:

    ```json
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
    ```

* **Reemplazar propiedades deseadas**. Esta operación permite que el back-end de la solución sobrescriba completamente todas las propiedades deseadas y sustituya un nuevo documento JSON para `properties/desired`.
* **Reemplazar etiquetas**. Esta operación permite que el back-end de la solución sobrescriba completamente todas las etiquetas y sustituya un nuevo documento JSON para `tags`.
* **Recibir notificaciones gemelas**. Esta operación permite que el back-end de la solución reciba una notificación cuando se modifique la gemela. Para ello, la solución de IoT debe crear una ruta y establecer el origen de datos igual a *twinChangeEvents*. De forma predeterminada, no se envían notificaciones gemelas, es decir, no existen previamente tales rutas. Si la tasa de cambio es demasiado alta, o por otras razones, como errores internos, IoT Hub podría enviar una sola notificación que contiene todos los cambios. Por lo tanto, si la aplicación necesita registro y auditoría confiables de todos los estados intermedios, debe usar mensajes del dispositivo a la nube. El mensaje de notificaciones gemelas incluye propiedades y el cuerpo.

    - Properties (Propiedades)

    | NOMBRE | Valor |
    | --- | --- |
    $content-type | application/json |
    $iothub-enqueuedtime |  Hora de envío de la notificación |
    $iothub-message-source | twinChangeEvents |
    $content-encoding | utf-8 |
    deviceId | Id. del dispositivo |
    moduleId | Identificador del módulo. |
    hubName | Nombre de IoT Hub |
    operationTimestamp | Marca de tiempo [ISO8601] de operación |
    iothub-message-schema | deviceLifecycleNotification |
    opType | "replaceTwin" o "updateTwin" |

    Las propiedades del sistema de mensajes tienen como prefijo el símbolo `'$'`.

    - Cuerpo
        
    Esta sección incluye todos los cambios gemelos en formato JSON. Se usa el mismo formato que una revisión, con la diferencia de que puede contener todas las secciones gemelas: etiquetas, propiedades notificadas, propiedades deseadas, y que contiene los elementos "$metadata". Por ejemplo,

    ```json
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

* Consultar los módulos gemelos mediante el [lenguaje de consulta de IoT Hub][lnk-query] de tipo SQL.

## <a name="module-operations"></a>Operaciones de módulo
La aplicación de módulo opera en el módulo gemelo mediante las siguientes operaciones atómicas:

* **Recuperación del módulo gemelo**. Esta operación devuelve el documento del módulo gemelo (incluidas las etiquetas y las propiedades del sistema, deseadas y notificadas) para el módulo conectado actualmente.
* **Actualizar parcialmente propiedades notificadas**. Esta operación permite la actualización parcial de las propiedades notificadas del módulo conectado actualmente. Esta operación usa el mismo formato de actualización JSON que el back-end de la solución usa para una actualización parcial de propiedades deseadas.
* **Observar las propiedades deseadas**. El módulo conectado actualmente puede elegir recibir notificaciones de las actualizaciones de las propiedades deseadas cuando se produzcan. El módulo recibe la misma forma de actualización (sustitución parcial o completa) ejecutada por el back-end de la solución.

Todas las operaciones anteriores requieren el permiso **ModuleConnect**, tal y como se define en el artículo sobre [seguridad][lnk-security].

Los [SDK de dispositivos IoT de Azure][lnk-sdks] permiten usar fácilmente las operaciones anteriores desde numerosos lenguajes y plataformas.

## <a name="tags-and-properties-format"></a>Formato de etiquetas y propiedades
Las etiquetas y las propiedades deseadas y notificadas son objetos JSON con las siguientes restricciones:

* Todas las claves en objetos JSON son cadenas UNICODE UTF-8 de 64 caracteres que distinguen entre mayúsculas y minúsculas. Entre los caracteres permitidos no se incluyen los caracteres de control UNICODE (segmentos C0 y C1), ni `'.'`, `' '` y `'$'`.
* Todos los valores en un objeto JSON pueden ser de los siguientes tipos JSON: booleano, número, cadena, objeto. No se permiten matrices. El valor máximo de enteros es 4503599627370495 y el valor mínimo de enteros es-4503599627370496.
* Todos los objetos JSON en etiquetas y propiedades deseadas y notificadas pueden tener una profundidad máxima de 5. Por ejemplo, el objeto siguiente es válido:

    ```json
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
    ```

* Todos los valores de cadena pueden tener una longitud de 4 KB como máximo.

## <a name="module-twin-size"></a>Tamaño del módulo gemelo
IoT Hub impone un límite de tamaño de 8 KB en cada uno de los valores totales respectivos `tags`, `properties/desired` y `properties/reported`, excepto los elementos de solo lectura.
El tamaño se calcula contando todos los caracteres, excepto los caracteres de control UNICODE (segmentos C0 y C1) y los espacios que se encuentran fuera de las constantes de cadena.
IoT Hub rechaza con un error todas las operaciones que podrían aumentar el tamaño de los documentos por encima del límite.

## <a name="module-twin-metadata"></a>Metadatos del módulo gemelo
IoT Hub conserva la marca de tiempo de la última actualización para cada objeto JSON en las propiedades deseadas y notificadas del módulo gemelo. Las marcas de tiempo están en formato UTC y se codifican con el formato [ISO8601]`YYYY-MM-DDTHH:MM:SS.mmmZ`.
Por ejemplo: 

```json
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
```

Esta información se conserva en todo los niveles (no solo en las hojas de la estructura JSON) para conservar las actualizaciones que quitan las claves de objeto.

## <a name="optimistic-concurrency"></a>Simultaneidad optimista
Tanto las etiquetas como las propiedades deseadas y notificadas admiten la simultaneidad optimista.
Las etiquetas tienen una ETag, según la norma [RFC7232], que es la representación JSON de la etiqueta. Puede usar ETags en operaciones de actualización condicional desde el back-end de la solución para garantizar la coherencia.

Las propiedades deseadas y notificadas del módulo gemelo no tienen ETags, pero tienen un valor `$version` que se garantiza que será incremental. De forma similar a una ETag, la parte que realiza la actualización puede usar la versión para garantizar la coherencia de las actualizaciones. Por ejemplo, una aplicación de módulo para una propiedad notificada o la solución de back-end para una propiedad deseada.

Las versiones también son útiles cuando un agente de observación (por ejemplo, la aplicación de módulo que observa las propiedades deseadas) tiene que conciliar las carreras entre el resultado de una operación de recuperación y una notificación de actualización. En la sección [Flujo de reconexión de dispositivos] [lnk-reconnection] se proporciona más información. 

## <a name="next-steps"></a>Pasos siguientes
Para probar algunos de los conceptos descritos en este artículo, vea los siguientes tutoriales de IoT Hub:

* [Introducción a la identidad de módulo y los módulos gemelos de IoT Hub con un dispositivo .NET y la copia de seguridad de .NET][lnk-module-twin-tutorial]

<!-- links and images -->

[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232

[lnk-module-twin-tutorial]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-module-twin-metadata]: iot-hub-devguide-module-twins.md#module-twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[img-module-twin]: media/iot-hub-devguide-device-twins/module-twin.jpg
