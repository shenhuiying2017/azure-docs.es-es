---
title: Configuración y supervisión de dispositivos IoT a escala con Azure IoT Hub | Microsoft Docs
description: Uso de las configuraciones automáticas del dispositivo de Azure IoT Hub para asignar una configuración a varios dispositivos
services: iot-hub
documentationcenter: ''
author: ChrisGMsft
manager: timlt
editor: ''
ms.service: iot-hub
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: 7146fba69857c3a612ce1b3dbb83387c1f3068d6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="configure-and-monitor-iot-devices-at-scale---preview"></a>Configuración y supervisión de dispositivos IoT a escala (versión preliminar)

La administración automática de dispositivos de Azure IoT Hub automatiza muchas de las tareas repetitivas y complejas de administración de grandes flotas de dispositivos durante su ciclo de vida completo. Con la administración automática de dispositivos, puede tener como destino un conjunto de dispositivos según sus propiedades, definir una configuración deseada y permitir que IoT Hub actualice los dispositivos en cuanto estén dentro del alcance.  Esto se realiza mediante la configuración automática de dispositivos, lo que también le permite resumir la finalización y la conformidad, controlar combinaciones y conflictos y revertir las configuraciones por fases.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Las configuraciones automáticas de dispositivos funcionan mediante la actualización de un conjunto de dispositivos gemelos con las propiedades deseadas y con la creación de informes de un resumen basado en las propiedades notificadas de los dispositivos gemelos.  Presenta una nueva clase y un documento JSON que llama a una _Configuración_ que tiene tres partes:

* La **condición de destino** define el ámbito de los dispositivos gemelos que se van a actualizar. La condición de destino se especifica como una consulta de las propiedades notificadas o de las etiquetas de los dispositivos gemelos.

* El **contenido de destino** define las propiedades deseadas que se van a agregar o actualizar en los dispositivos gemelos de destino. El contenido incluye una ruta de acceso a la sección de las propiedades que desea cambiar.

* Las **métricas** definen el número de resúmenes de los distintos estados de configuración, como **Correcto**, **En curso** y **Error**. Las métricas personalizadas se especifican como consultas de las propiedades notificadas de los dispositivos gemelos.  Las métricas del sistema son las métricas predeterminadas que miden el estado de los dispositivos gemelos, como el número de dispositivos gemelos de destino y el número de dispositivos gemelos que se han actualizado correctamente. 

## <a name="implement-device-twins-to-configure-devices"></a>Implementación de dispositivos gemelos para configurar dispositivos

Las configuraciones automáticas de dispositivos requieren el uso de dispositivos gemelos para sincronizar el estado entre la nube y los dispositivos.  Consulte [Información y uso de los dispositivos gemelos en IoT Hub][lnk-device-twin] para obtener orientación sobre el uso de los dispositivos gemelos.

## <a name="identify-devices-using-tags"></a>Identificación de dispositivos mediante etiquetas

Antes de crear una configuración, tendrá que especificar a qué dispositivos desea que afecte tal configuración. Azure IoT Hub identifica los dispositivos mediante etiquetas en el dispositivo gemelo. Cada dispositivo puede tener varias etiquetas y puede definirlas de cualquier manera que tenga sentido para su solución. Por ejemplo, si administra dispositivos en distintas ubicaciones, puede agregar las siguientes etiquetas a un dispositivo gemelo:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="create-a-configuration"></a>Creación de una configuración

1. En [Azure Portal][lnk-portal], vaya hasta su instancia de IoT Hub. 
1. Seleccione **Configuración del dispositivo (versión preliminar)**.
1. Seleccione **Agregar configuración**.

Hay cinco pasos para crear una configuración. En las siguientes secciones se abordan cada uno de ellos. 

### <a name="step-1-name-and-label"></a>Paso 1: Nombre y etiqueta

1. Asigne a su configuración un nombre exclusivo de hasta 128 letras en minúscula. Evite los espacios y los siguientes caracteres no válidos: `& ^ [ ] { } \ | " < > /`.
1. Agregue etiquetas para realizar un mejor seguimiento de las configuraciones. Las etiquetas son pares de **nombre** y **valor** que describen la configuración. Por ejemplo, `HostPlatform, Linux` o `Version, 3.0.1`.
1. Seleccione **Siguiente** para ir al segundo paso. 

### <a name="step-2-specify-settings"></a>Paso 2: Especificar la configuración

En esta sección se especifica el contenido de destino que se debe especificar en los dispositivos gemelos de destino. Hay dos entradas para cada conjunto de configuración. La primera es la ruta de acceso del dispositivo gemelo, que coincide con la ruta de acceso a la sección JSON dentro de las propiedades deseadas del dispositivo gemelo que se van a configurar.  La segunda es el contenido JSON que se va a insertar en dicha sección. Por ejemplo, defina la ruta de acceso y el contenido del dispositivo gemelo como sigue:

![Definición del contenido y la ruta de acceso del dispositivo gemelo](./media/iot-hub-auto-device-config/create-configuration-full-browser.png)

También puede definir opciones de configuración individuales al especificar la ruta de acceso completa en la ruta de acceso del dispositivo gemelo y el valor en el contenido sin corchetes. Por ejemplo, defina la ruta de acceso del dispositivo gemelo en `properties.desired.chiller-water.temperature` y establezca el contenido en: `66`.

Si dos o más configuraciones tienen como destino la misma ruta de acceso del dispositivo gemelo, se aplicará el contenido de la configuración más prioritaria (la prioridad se define en el paso 4).

Si desea quitar una propiedad, especifique el valor de la propiedad en `null`.

Puede agregar opciones de configuración adicionales si selecciona **Agregar configuración de dispositivo gemelo**.

### <a name="step-3-specify-metrics-optional"></a>Paso 3: Especificar métricas (opcional)

Las métricas proporcionan el número de resúmenes de los distintos estados que un dispositivo puede notificar como resultado de la aplicación del contenido de configuración. Por ejemplo, puede crear una métrica para los cambios de configuración pendientes, una métrica para los errores y una métrica para los cambios de configuración correctos.

1. Escriba un nombre para **Nombre de métrica**.
1. Escriba una consulta para **Criterios de las métricas**.  La consulta se basa en las propiedades notificadas de dispositivos gemelos.  La métrica representa el número de filas devueltas por la consulta.

Por ejemplo: `SELECT deviceId FROM devices WHERE properties.reported.chillerWaterSettings.status='pending'`

Puede incluir una cláusula a la que se aplicó la configuración, como por ejemplo: `SELECT deviceId FROM devices WHERE configurations.yourconfigname.status='Applied'`


### <a name="step-4-target-devices"></a>Paso 4: Dispositivos de destino

Use la propiedad de etiquetas en los dispositivos para dirigirse a los dispositivos específicos que deberían recibir esta configuración.  También puede tener como destino dispositivos en función de las propiedades notificadas de dispositivos gemelos.

Como varias configuraciones pueden tener como destino el mismo dispositivo, debe dar a cada configuración un número de prioridad. En caso de conflicto, gana la configuración con la prioridad más alta. 

1. Especifique un número entero positivo en el valor de **Prioridad** de la configuración. El valor numérico más alto se considera la máxima prioridad. Si dos configuraciones tienen el mismo número de prioridad, gana la que se creó más recientemente. 
1. Escriba una **condición de destino** para determinar qué dispositivos se dirigirán a esta configuración. La condición se basa en las etiquetas del dispositivo gemelo o en las propiedades notificadas del dispositivo gemelo y debe coincidir con el formato de expresión. Por ejemplo, `tags.environment='test'` o `properties.reported.chillerProperties.model='4000x'`. 
1. Seleccione **Siguiente** para pasar al último paso.

### <a name="step-5-review-configuration"></a>Paso 5: Revisar configuración

Revise la información de configuración y seleccione **Enviar**.

## <a name="monitor-a-configuration"></a>Supervisión de una configuración

Para ver los detalles de una configuración y supervisar los dispositivos que la ejecutan, siga estos pasos:

1. En [Azure Portal][lnk-portal], vaya hasta su instancia de IoT Hub. 
1. Seleccione **Configuración del dispositivo (versión preliminar)**.
1. Examine la lista de configuración. Para cada configuración, puede ver los detalles siguientes:
   * **ID** (Identificador): nombre de la configuración.
   * **Target condition** (Condición de destino): la consulta que se utiliza para definir los dispositivos de destino.
   * **Priority** (Prioridad): el número de prioridad asignado a la configuración.
   * **Creation time** (Hora de creación): la marca de tiempo de cuando se creó la configuración. Esta marca de tiempo se utiliza para dirimir cuando dos configuraciones tienen la misma prioridad. 
   * **System metrics** (Métricas del sistema): las métricas calculadas por IoT Hub y que los desarrolladores no pueden personalizar. Los destinos especifican el número de dispositivos gemelos que coinciden con la condición de destino. Aplica el número especificado de dispositivos gemelos modificados por la configuración, que puede incluir modificaciones parciales en caso de que una configuración independiente de mayor prioridad también realice cambios. 
   * **Custom metrics** (Métricas personalizadas): métricas especificadas por el desarrollador como consultas de las propiedades notificadas de dispositivos gemelos.  Se pueden definir hasta cinco métricas personalizadas por configuración. 
   
1. Seleccione la configuración que desea supervisar.  
1. Inspeccione los detalles de configuración. Puede utilizar las pestañas para ver detalles específicos sobre los dispositivos que han recibido la configuración: 
   * **Target Condition** (Condición de destino): los dispositivos que coinciden con la condición de destino. 
   * **Metrics** (Métricas): una lista de métricas del sistema y métricas personalizadas.  Para ver una lista de dispositivos que se enumeran para cada métrica, seleccione la métrica en el menú desplegable y luego seleccione **View Devices** (Ver dispositivos).
   * **Device Twin Settings** (Configuración del dispositivo gemelo): los ajustes del dispositivo gemelo definidos por la configuración. 
   * **Configuration Labels** (Etiquetas de configuración): pares clave-valor utilizados para describir una configuración.  Las etiquetas no tienen ningún impacto en la funcionalidad. 

## <a name="modify-a-configuration"></a>Modificación de una configuración

Cuando se modifica una configuración, los cambios se replican inmediatamente a todos los dispositivos seleccionados. 

Si actualiza la condición de destino, se producen las siguientes actualizaciones:
* Si un dispositivo gemelo no cumplía la antigua condición de destino, pero cumple la nueva condición de destino y esta configuración es la prioridad más alta para ese dispositivo gemelo, esta configuración se aplica al dispositivo gemelo. 
* Si un dispositivo gemelo ya no cumple la condición de destino, los ajustes de la configuración se eliminarán y el dispositivo gemelo se modificará con la configuración de la siguiente prioridad más alta. 
* Si un dispositivo gemelo que ejecuta actualmente esta configuración ya no cumple la condición de destino y tampoco la condición de destino de cualquier otra configuración, los ajustes de la configuración se eliminarán y no se realizará ningún otro cambio en el dispositivo gemelo. 

Para modificar una configuración, siga estos pasos: 

1. En [Azure Portal][lnk-portal], vaya hasta su instancia de IoT Hub. 
1. Seleccione **Configuración del dispositivo (versión preliminar)**. 
1. Seleccione la configuración que desea modificar. 
1. Realice las actualizaciones en los campos siguientes: 
   * Condición de destino 
   * Etiquetas 
   * Prioridad 
   * Métricas
1. Seleccione **Guardar**.
1. Siga los pasos de [Supervisión de una configuración][anchor-monitor] para observar la implementación de los cambios. 

## <a name="delete-a-configuration"></a>Eliminación de una configuración

Al eliminar una configuración, los dispositivos gemelos adoptan la configuración con la siguiente prioridad más alta. Si los dispositivos gemelos no cumplen la condición de destino de cualquier otra configuración, no se aplica ningún otro ajuste. 

1. En [Azure Portal][lnk-portal], vaya hasta su instancia de IoT Hub. 
1. Seleccione **Configuración del dispositivo (versión preliminar)**. 
1. Utilice la casilla de verificación para seleccionar la configuración que desea eliminar. 
1. Seleccione **Eliminar**.
1. Un símbolo del sistema le pedirá que confirme la operación.

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido cómo configurar y supervisar dispositivos IoT a escala. Siga estos vínculos para más información sobre la administración de Azure IoT Hub:

* [Administración de las identidades de dispositivo de IoT Hub de forma masiva][lnk-bulkIDs]
* [Métricas de IoT Hub][lnk-metrics]
* [Supervisión de operaciones][lnk-monitor]

Para explorar aún más las funcionalidades de IoT Hub, consulte:

* [Guía para desarrolladores de IoT Hub][lnk-devguide]
* [Implementación de Azure IoT Edge en un dispositivo simulado en Linux: versión preliminar][lnk-iotedge]

Para explorar el uso del servicio IoT Hub Device Provisioning para habilitar el aprovisionamiento Just-In-Time sin intervención del usuario, vea: 

* [Servicio Azure IoT Hub Device Provisioning][lnk-dps]

[lnk-device-twin]: iot-hub-devguide-device-twins.md
[lnk-bulkIDs]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
[lnk-portal]: https://portal.azure.com
