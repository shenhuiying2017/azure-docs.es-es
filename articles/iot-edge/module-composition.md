---
title: "Composición del módulo de Azure IoT Edge | Microsoft Docs"
description: "Información sobre qué incluyen los módulos de Azure IoT Edge y cómo se pueden reutilizar"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: f3bc2f14b182e502c651ff44ef49b88cd34e1f50
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>Descripción de cómo se pueden utilizar, configurar y reutilizar los módulos de IoT Edge (versión preliminar)

Azure IoT Edge permite crear varios módulos de IoT Edge antes de implementarlos en dispositivos IoT Edge. En este artículo se explican los conceptos más importantes en torno a la composición de varios módulos de IoT Edge antes de realizar la implementación. 

## <a name="the-deployment-manifest"></a>El manifiesto de implementación
El *manifiesto de implementación* es un documento JSON que describe lo siguiente:

* Qué módulos de IoT Edge tienen que implementarse, junto con las opciones de creación y administración.
* La configuración del centro de Edge, que describe cómo deberían fluir los mensajes entre los módulos, y entre los módulos e IoT Hub.
* De forma opcional, los valores para establecer en las propiedades deseadas de los módulos gemelos con el objetivo de configurar las aplicaciones de los módulos.

En los tutoriales de Azure IoT Edge, creará un manifiesto de implementación a través de un asistente del portal de Azure IoT Edge. También puede aplicar un manifiesto de implementación mediante programación con REST o el SDK del servicio IoT Hub. Consulte el artículo de [implementación y supervisión ][lnk-deploy] para obtener más información sobre implementaciones de IoT Edge.

A nivel general, el manifiesto de implementación configura las propiedades deseadas de los módulos de IoT Edge implementadas en un dispositivo IoT Edge. Dos de estos módulos siempre están presentes: el agente y el centro de Edge.

El manifiesto sigue esta estructura:

    {
        "moduleContent": {
            "$edgeAgent": {
                "properties.desired": {
                    // desired properties of the Edge agent
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    // desired properties of the Edge hub
                }
            },
            "{module1}": {  // optional
                "properties.desired": {
                    // desired properties of module with id {module1}
                }
            },
            "{module2}": {  // optional
                ...
            },
            ...
        }
    }

Al final de esta sección encontrará un manifiesto de implementación de ejemplo.

> [!IMPORTANT]
> Todos los dispositivos IoT Edge deben configurarse con un manifiesto de implementación. Si no, una instancia de IoT Edge en tiempo de ejecución recién instalada notificará un código de error hasta que se configuren con un manifiesto válido. 

### <a name="specify-the-modules"></a>Especificación de los módulos
Las propiedades deseadas del módulo gemelo del agente de Edge contienen los siguientes elementos: los módulos deseados, sus opciones de configuración y administración, y los parámetros de configuración del agente de Edge.

A nivel general, en esta sección del manifiesto están las referencias a las imágenes del módulo, las opciones de administración de los módulos de la instancia en tiempo de ejecución de IoT Edge (el agente y el centro de Edge) y los módulos especificados por el usuario.

Consulte la sección sobre las [propiedades deseadas del agente de Edge][lnk-edgeagent-desired] para obtener una descripción detallada de este manifiesto.

> [!NOTE]
> Un manifiesto de implementación que contiene solo la instancia en tiempo de ejecución de IoT Edge (agente y centro) es válido.


### <a name="specify-the-routes"></a>Especificación de las rutas
El centro de Edge proporciona una manera de enrutar de forma declarativa los mensajes entre los módulos y entre los módulos e IoT Hub.

Las rutas tienen la sintaxis siguiente:

        FROM <source>
        [WHERE <condition>]
        INTO <sink>

El *origen* puede ser cualquier de los siguientes elementos:

| Origen | DESCRIPCIÓN |
| ------ | ----------- |
| `/*` | Todos los mensajes de dispositivo a nube desde cualquier dispositivo o módulo. |
| `/messages/*` | Cualquier mensaje de dispositivo a nube que envíe un dispositivo o un módulo con alguna salida o sin ninguna. |
| `/messages/modules/*` | Cualquier mensaje de dispositivo a nube que envíe un módulo con alguna salida o sin ninguna. |
| `/messages/modules/{moduleId}/*` | Cualquier mensaje de dispositivo a nube que envíe un módulo concreto sin ninguna salida. |
| `/messages/modules/{moduleId}/outputs/*` | Cualquier mensaje de dispositivo a nube que envíe un módulo concreto con alguna salida. |
| `/messages/modules/{moduleId}/outputs/{output}` | Cualquier mensaje de dispositivo a nube que envíe un módulo concreto con una salida específica. |

La condición puede ser cualquiera que admita el [lenguaje de consultas de IoT Hub][lnk-iothub-query] en lo que respecta a las reglas de enrutamiento de IoT Hub.

El receptor puede ser uno de los siguientes:

| Receptor | DESCRIPCIÓN |
| ---- | ----------- |
| `$upstream` | Envía el mensaje a IoT Hub. |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | Envía el mensaje a la entrada `{input}` del módulo`{moduleId}`. |

Es importante tener en cuenta que el centro Edge proporciona garantías de tipo "at-least-once" (por lo menos una vez); es decir, los mensajes se almacenarán localmente en caso de que una ruta no pueda entregarlo a su receptor. Por ejemplo, el centro de Edge no se puede conectar a IoT Hub o el módulo de destino no está conectado.

El centro de Edge almacena los mensajes durante el tiempo especificado en la propiedad `storeAndForwardConfiguration.timeToLiveSecs` de las propiedades deseadas del centro de Edge.

### <a name="specifying-the-desired-properties-of-the-module-twin"></a>Especificación de las propiedades deseadas del módulo gemelo

El manifiesto de implementación puede especificar las propiedades deseadas del módulo gemelo de cada uno de los módulos de usuario especificados en la sección del agente de Edge.

Cuando se especifican las propiedades deseadas en el manifiesto de implementación, sobrescriben a las que estén presentes en el módulo gemelo.

Si no se especifican las propiedades deseadas del módulo gemelo en el manifiesto de implementación, IoT Hub no modificará el módulo y se podrán establecer las propiedades deseadas mediante programación.

### <a name="deployment-manifest-example"></a>Ejemplo de manifiesto de implementación

Esto es un ejemplo de documento JSON de manifiesto de implementación.

    {
    "moduleContent": {
        "$edgeAgent": {
            "properties.desired": {
                "schemaVersion": "1.0",
                "runtime": {
                    "type": "docker",
                    "settings": {
                        "minDockerVersion": "v1.25",
                        "loggingOptions": ""
                    }
                },
                "systemModules": {
                    "edgeAgent": {
                        "type": "docker",
                        "settings": {
                        "image": "microsoft/azureiotedge-agent:1.0-preview",
                        "createOptions": ""
                        }
                    },
                    "edgeHub": {
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "microsoft/azureiotedge-hub:1.0-preview",
                        "createOptions": ""
                        }
                    }
                },
                "modules": {
                    "tempSensor": {
                        "version": "1.0",
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
                        "createOptions": "{}"
                        }
                    },
                    "filtermodule": {
                        "version": "1.0",
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "myacr.azurecr.io/filtermodule:latest",
                        "createOptions": "{}"
                        }
                    }
                }
            }
        },
        "$edgeHub": {
            "properties.desired": {
                "schemaVersion": "1.0",
                "routes": {
                    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
                    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
                },
                "storeAndForwardConfiguration": {
                    "timeToLiveSecs": 10
                }
            }
        }
    }
    }

## <a name="reference-edge-agent-module-twin"></a>Referencia: Módulo gemelo del agente de Edge

El módulo gemelo del agente de Edge se denomina `$edgeAgent` y coordina las comunicaciones entre el agente de Edge que se ejecuta en un dispositivo y en IoT Hub.
Las propiedades deseadas se establecen al aplicar un manifiesto de implementación en un dispositivo específico como parte de la implementación de un solo dispositivo o a escala. Consulte el artículo sobre [implementación y supervisión][lnk-deploy] para obtener más información sobre cómo implementar módulos en dispositivos IoT Edge.

### <a name="edge-agent-twin-desired-properties"></a>Propiedades deseadas del módulo gemelo del agente de Edge

| Propiedad | DESCRIPCIÓN | Requerido |
| -------- | ----------- | -------- |
| schemaVersion | Debe ser "1.0". | Sí |
| runtime.type | Debe ser "docker". | Sí |
| runtime.settings.minDockerVersion | Establece la propiedad en la versión de Docker mínima que requiere este manifiesto de implementación. | Sí |
| runtime.settings.loggingOptions | Cadenas JSON que contienen las opciones de registro del contenedor del agente de Edge. [Opciones de registro de Docker][lnk-docker-logging-options] | Sin  |
| systemModules.edgeAgent.type | Debe ser "docker". | Sí |
| systemModules.edgeAgent.settings.image | El URI de la imagen del agente de Edge. Actualmente, el agente de Edge no puede actualizarse automáticamente. | Sí |
| systemModules.edgeAgent.settings.createOptions | Cadenas JSON que contienen las opciones de creación del contenedor del agente de Edge. [Opciones de creación de Docker][lnk-docker-create-options] | Sin  |
| systemModules.edgeAgent.configuration.id | El identificador de la implementación que implementó este módulo. | Esta propiedad la establece IoT Hub cuando se aplica este manifiesto mediante una implementación. No forma parte de un manifiesto de implementación. |
| systemModules.edgeHub.type | Debe ser "docker". | Sí |
| systemModules.edgeHub.status | Debe ser "running". | Sí |
| systemModules.edgeHub.restartPolicy | Debe ser "always". | Sí |
| systemModules.edgeHub.settings.image | El URI de la imagen del centro de Edge. | Sí |
| systemModules.edgeHub.settings.createOptions | Cadenas JSON que contienen las opciones de creación del contenedor del centro de Edge. [Opciones de creación de Docker][lnk-docker-create-options] | Sin  |
| systemModules.edgeHub.configuration.id | El identificador de la implementación que implementó este módulo. | Esta propiedad la establece IoT Hub cuando se aplica este manifiesto mediante una implementación. No forma parte de un manifiesto de implementación. |
| modules.{moduleId}.version | Una cadena definida por el usuario que representa la versión de este módulo. | Sí |
| modules.{moduleId}.type | Debe ser "docker". | Sí |
| modules.{moduleId}.restartPolicy | {"never" \| "on-failed" \| "on-unhealthy" \| "always"} | Sí |
| modules.{moduleId}.settings.image | El URI de la imagen del módulo. | Sí |
| modules.{moduleId}.settings.createOptions | Cadenas JSON que contienen las opciones de creación del contenedor del módulo. [Opciones de creación de Docker][lnk-docker-create-options] | Sin  |
| modules.{moduleId}.configuration.id | El identificador de la implementación que implementó este módulo. | Esta propiedad la establece IoT Hub cuando se aplica este manifiesto mediante una implementación. No forma parte de un manifiesto de implementación. |

### <a name="edge-agent-twin-reported-properties"></a>Propiedades notificadas del módulo gemelo del agente de Edge

Las propiedades notificadas del agente de Edge incluyen tres fragmentos principales de información:

1. El estado de la aplicación de las propiedades deseadas procesadas por última vez.
2. El estado de los módulos que se están ejecutando actualmente en el dispositivo, tal y como ha notificado el agente de Edge.
3. Una copia de las propiedades deseadas que se están ejecutando actualmente en el dispositivo.

Este último fragmento de información es útil en caso de que las propiedades deseadas más recientes no las aplicara correctamente la instancia en tiempo de ejecución, y de que el dispositivo aún esté ejecutando un manifiesto de implementación anterior.

> [!NOTE]
> Las propiedades notificadas del agente de Edge son útiles porque pueden consultarse con el [lenguaje de consultas de IoT Hub][lnk-iothub-query] para investigar a escala el estado de las implementaciones. Consulte el artículo sobre [implementaciones][lnk-deploy] para obtener más información sobre cómo usar esta característica.

La tabla siguiente no incluye la información que se copia de las propiedades deseadas.

| Propiedad | DESCRIPCIÓN |
| -------- | ----------- |
| lastDesiredVersion | Esta propiedad hace referencia a la última versión de las propiedades deseadas procesadas mediante el agente de Edge. |
| lastDesiredStatus.code | Se trata del código de estado que hace referencia a las últimas propiedades que procesó el agente de Edge. Valores permitidos: `200` (correcto), `400` (configuración no válida), `412` (versión de esquema no válido), `417` (las propiedades deseadas están vacías) y `500` (error). |
| lastDesiredStatus.description | Descripción de texto del estado. |
| deviceHealth | `healthy` si el estado en tiempo de ejecución de todos los módulos es `running` o `stopped`; `unhealthy` otra condición. |
| configurationHealth.{deploymentId}.health | `healthy` si el estado en tiempo de ejecución de todos los módulos establecidos mediante la implementación {deploymentId} es `running` o `stopped`; `unhealthy` otra condición. |
| runtime.platform.OS | Notifica el sistema operativo que se ejecuta en el dispositivo. |
| runtime.platform.architecture | Notifica la arquitectura de la CPU del dispositivo. |
| systemModules.edgeAgent.runtimeStatus | El estado notificado del agente de Edge: {"running" \| "unhealthy"}. |
| systemModules.edgeAgent.statusDescription | Descripción de texto del estado notificado del agente de Edge. |
| systemModules.edgeHub.runtimeStatus | Estado actual del centro de Edge: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" }. |
| systemModules.edgeHub.statusDescription | Descripción de texto del estado actual del centro de Edge si es el estado es unhealthy. |
| systemModules.edgeHub.exitCode | Si se produce un evento de salida, será el código de salida que notifica el contenedor del centro de Edge. |
| systemModules.edgeHub.startTimeUtc | Hora a la que se inició por última vez el centro de Edge. |
| systemModules.edgeHub.lastExitTimeUtc | Hora a la que se salió por última vez del centro de Edge. |
| systemModules.edgeHub.lastRestartTimeUtc | Hora a la que se reinició por última vez el centro de Edge. |
| systemModules.edgeHub.restartCount | Número de veces que se ha reiniciado este módulo como parte de la directiva de reinicio. |
| modules.{moduleId}.runtimeStatus | Estado actual del módulo: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" }. |
| modules.{moduleId}.statusDescription | Descripción de texto del estado actual del módulo si el estado es unhealthy. |
| modules.{moduleId}.exitCode | Si se produce un evento de salida, será el código de salida que notifica el contenedor del módulo. |
| modules.{moduleId}.startTimeUtc | Hora a la que se inició por última vez el módulo. |
| modules.{moduleId}.lastExitTimeUtc | Hora a la que se salió por última vez del módulo. |
| modules.{moduleId}.lastRestartTimeUtc | Hora a la que se reinició por última vez el módulo. |
| modules.{moduleId}.restartCount | Número de veces que se ha reiniciado este módulo como parte de la directiva de reinicio. |

## <a name="reference-edge-hub-module-twin"></a>Referencia: Módulo gemelo del centro de Edge

El módulo gemelo del centro de Edge se denomina `$edgeHub` y coordina las comunicaciones entre el centro de Edge que se ejecuta en un dispositivo y en IoT Hub.
Las propiedades deseadas se establecen al aplicar un manifiesto de implementación en un dispositivo específico como parte de la implementación de un solo dispositivo o a escala. Consulte el artículo sobre [implementaciones][lnk-deploy] para obtener más información sobre cómo implementar módulos en dispositivos IoT Edge.

### <a name="edge-hub-twin-desired-properties"></a>Propiedades deseadas de la central gemela de Edge

| Propiedad | DESCRIPCIÓN | Requerida en el manifiesto de implementación |
| -------- | ----------- | -------- |
| schemaVersion | Debe ser "1.0". | Sí |
| routes.{routeName} | Una cadena que representa una ruta del centro de Edge. | El elemento `routes` puede estar presente, pero vacío. |
| storeAndForwardConfiguration.timeToLiveSecs | El tiempo en segundos que el centro de Edge conserva los mensajes en el caso de que se desconecten puntos de conexión de enrutamiento, por ejemplo, de IoT Hub o del módulo local. | Sí |

### <a name="edge-hub-twin-reported-properties"></a>Propiedades notificadas del centro gemela de Edge

| Propiedad | DESCRIPCIÓN |
| -------- | ----------- |
| lastDesiredVersion | Esta propiedad hace referencia a la última versión de las propiedades deseadas procesadas mediante el centro de Edge. |
| lastDesiredStatus.code | Se trata del código de estado que hace referencia a las últimas propiedades que procesó el centro de Edge. Valores permitidos: `200` (correcto), `400` (configuración no válida) y `500` (error). |
| lastDesiredStatus.description | Descripción de texto del estado. |
| clients.{identidad de dispositivo o módulo}.status | El estado de conectividad de este dispositivo o módulo. Valores posibles: "connected" \| "disconnected"}. Solo las identidades de módulo pueden estar en el estado disconnected. Los dispositivos de nivel inferior que se conectan al centro de Edge solo aparecen cuando se conectan. |
| clients.{identidad de dispositivo o módulo}.lastConnectTime | Última vez que se conectó el dispositivo o módulo. |
| clients.{identidad de dispositivo o módulo}.lastDisconnectTime | Última vez que se desconectó el dispositivo o módulo. |

## <a name="next-steps"></a>pasos siguientes

Ahora que sabe cómo se usan los módulos de IoT Hub, [descubra los requisitos y las herramientas para desarrollar módulos de IoT Edge][lnk-module-dev].

[lnk-deploy]: module-deployment-monitoring.md
[lnk-edgeagent-desired]: #edge-agent-twin-desired-properties
[lnk-edgeagent-reported]: #edge-agent-twin-reported-properties
[lnk=edgehub-desired]: #edge-hub-twin-desired-properties
[lnk-edgehub-reported]: #edge-hub-twin-reported-properties
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
