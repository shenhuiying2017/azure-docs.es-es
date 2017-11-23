---
title: "Personalizar una solución preconfigurada | Microsoft Docs"
description: "Proporciona directrices sobre la personalización de ñas soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure."
services: 
suite: iot-suite
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 4653ae53-4110-4a10-bd6c-7dc034c293a8
ms.service: iot-suite
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: ba965b9bc23b96adb2b1b7c9306cb7f508f820bf
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2017
---
# <a name="customize-a-preconfigured-solution"></a>Personalizar una solución preconfigurada

Las soluciones preconfiguradas proporcionadas con el conjunto de aplicaciones de IoT de Azure muestran los servicios del conjunto de aplicaciones que funcionan juntos para proporcionar una solución completa. Desde este punto de partida, existen varios lugares donde puede ampliar y personalizar la solución para cada situación específica. Las secciones siguientes describen estos puntos de personalización comunes.

## <a name="find-the-source-code"></a>Ubicación del código fuente

El código fuente de su solución preconfigurada está disponible en Github en los repositorios siguientes:

* Supervisión remota: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
* Mantenimiento predictivo: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)
* Fábrica conectada: [https://github.com/Azure/azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory)

El código fuente de las soluciones preconfiguradas se proporciona para demostrar los patrones y los procedimientos que se usan para implementar la funcionalidad de extremo a extremo de una solución de IoT mediante el conjunto de aplicaciones de IoT de Azure. Puede encontrar más información sobre cómo crear e implementar las soluciones en los repositorios de GitHub.

## <a name="change-the-preconfigured-rules"></a>Cambio de las reglas previamente configuradas

La solución de supervisión remota incluye tres trabajos de [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) para controlar la información del dispositivo, la telemetría y la lógica de reglas de la solución.

Los tres trabajos de Stream Analytics y su sintaxis se describen detalladamente en el [Tutorial de la solución preconfigurada de supervisión remota](iot-suite-v1-remote-monitoring-sample-walkthrough.md). 

Puede editar directamente estos trabajos y modificar la lógica, o agregar lógica específica a su escenario. Para encontrar los trabajos de Stream Analytics, siga estos pasos:

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. Vaya al grupo de recursos con el mismo nombre que la solución de IoT. 
3. Seleccione el trabajo de Azure Stream Analytics que desea modificar. 
4. Detenga el trabajo seleccionando **Detener** en el conjunto de comandos. 
5. Edite las entradas, la consulta y las salidas.
   
    Una modificación sencilla consiste en cambiar la consulta para que el trabajo **Reglas** use **"<"** en lugar de **">"**. El portal de la solución sigue mostrando **">"** al editar una regla, pero observe cómo varía el comportamiento debido al cambio en el trabajo subyacente.
6. Inicio del trabajo

> [!NOTE]
> El panel de supervisión remota depende de datos específicos, así que la modificación de los trabajos puede provocar errores del panel.

## <a name="add-your-own-rules"></a>Adición de reglas propias

Además de cambiar los trabajos de Azure Stream Analytics preconfigurados, puede usar Azure Portal para agregar nuevos trabajos o nuevas consultas a los trabajos existentes.

## <a name="customize-devices"></a>Personalización de dispositivos

Una de las actividades de extensión más comunes es trabajar con dispositivos específicos de su escenario. Existen varios métodos para trabajar con dispositivos. Entre estos se incluye la modificación de un dispositivo simulado para adaptarlo a su situación, o bien el uso del [SDK de dispositivo de IoT][IoT Device SDK] para conectar el dispositivo físico a la solución.

Para obtener una guía paso a paso sobre cómo agregar dispositivos, consulte el artículo [Dispositivos de conexión del conjunto de aplicaciones de IoT](iot-suite-v1-connecting-devices.md) y el [ejemplo del SDK de C de supervisión remota](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer/samples/remote_monitoring). Este ejemplo está diseñado para funcionar con la solución de supervisión remota preconfigurada.

### <a name="create-your-own-simulated-device"></a>Creación de dispositivo simulado propio

El [código fuente de la solución de supervisión remota](https://github.com/Azure/azure-iot-remote-monitoring) incluye un simulador. NET. Este simulador es el que se aprovisiona como parte de la solución y puede modificarlo para enviar distintos metadatos, telemetría y responder a distintos comandos y métodos.

El simulador preconfigurado en la solución preconfigurada de supervisión remota imita un dispositivo de refrigeración que emite la telemetría de temperatura y humedad. Puede modificar el simulador en el proyecto [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) cuando haya bifurcado el repositorio de GitHub.

### <a name="available-locations-for-simulated-devices"></a>Ubicaciones disponibles para dispositivos simulados

El conjunto predeterminado de ubicaciones es Seattle/Redmond, Washington (Estados Unidos de América). Puede cambiar estas ubicaciones en [SampleDeviceFactory.cs][lnk-sample-device-factory].

### <a name="add-a-desired-property-update-handler-to-the-simulator"></a>Adición de un controlador de actualización de propiedad deseada al simulador

Puede establecer un valor para una propiedad deseada de un dispositivo en el portal de la solución. Es responsabilidad del dispositivo controlar la solicitud de cambio de la propiedad cuando el dispositivo recupera el valor de la propiedad deseada. Para agregar compatibilidad para un cambio del valor de propiedad mediante una propiedad deseada, debe agregar un controlador al simulador.

El simulador contiene controladores para las propiedades **SetPointTemp** y **TelemetryInterval** que puede actualizar mediante el establecimiento de los valores deseados en el portal de soluciones.

En el ejemplo siguiente se muestra el controlador para la propiedad deseada **SetPointTemp** de la clase **CoolerDevice**:

```csharp
protected async Task OnSetPointTempUpdate(object value)
{
    var telemetry = _telemetryController as ITelemetryWithSetPointTemperature;
    telemetry.SetPointTemperature = Convert.ToDouble(value);

    await SetReportedPropertyAsync(SetPointTempPropertyName, telemetry.SetPointTemperature);
}
```

Este método permite actualizar la temperatura de telemetría y, a continuación, informa del cambio a IoT Hub estableciendo una propiedad notificada.

Puede agregar sus propios controladores a sus propias propiedades siguiendo el patrón del ejemplo anterior.

También debe enlazar la propiedad deseada al controlador tal y como se muestra en el siguiente ejemplo del constructor **CoolerDevice**:

```csharp
_desiredPropertyUpdateHandlers.Add(SetPointTempPropertyName, OnSetPointTempUpdate);
```

Tenga en cuenta que **SetPointTempPropertyName** es una constante definida como "Config.SetPointTemp".

### <a name="add-support-for-a-new-method-to-the-simulator"></a>Adición de compatibilidad con un nuevo método al simulador

Puede personalizar el simulador para agregar compatibilidad con un nuevo [método (método directo)][lnk-direct-methods]. Se necesitan dos pasos principales:

- El simulador debe notificar a la instancia de IoT Hub de la solución preconfigurada los detalles del método.
- El simulador debe incluir código para controlar la llamada al método cuando se invoca desde el panel **Detalles del dispositivo** en el Explorador de soluciones o a través de un trabajo.

La solución preconfigurada de supervisión remota usa *propiedades notificadas* para enviar los detalles sobre los métodos admitidos a IoT Hub. El back-end de soluciones mantiene una lista de todos los métodos admitidos por cada dispositivo junto con un historial de las invocaciones de cada método. Puede ver esta información acerca de los dispositivos e invocar métodos en el portal de la solución.

Para notificar a IoT Hub que un dispositivo es compatible con un método, el dispositivo debe agregar los detalles del método al nodo **SupportedMethods** en las propiedades notificadas:

```json
"SupportedMethods": {
  "<method signature>": "<method description>",
  "<method signature>": "<method description>"
}
```

La firma del método tiene el formato siguiente: `<method name>--<parameter #0 name>-<parameter #1 type>-...-<parameter #n name>-<parameter #n type>`. Por ejemplo, para especificar que el método **InitiateFirmwareUpdate** espera un parámetro de cadena denominado **FwPackageURI**, use la siguiente firma de método:

```json
InitiateFirmwareUpate--FwPackageURI-string: "description of method"
```

Para obtener una lista de los tipos de parámetro admitidos, consulte la clase **CommandTypes** del proyecto Infrastructure.

Para eliminar un método, establezca la firma del método en `null` en las propiedades notificadas.

> [!NOTE]
> El back-end de soluciones solo actualiza información acerca de los métodos compatibles cuando recibe un mensaje de *información del dispositivo* desde este.

El siguiente ejemplo de código de la clase **SampleDeviceFactory** del proyecto Common muestra cómo agregar un método a la lista de **SupportedMethods** en las propiedades notificadas enviadas por el dispositivo:

```csharp
device.Commands.Add(new Command(
    "InitiateFirmwareUpdate",
    DeliveryType.Method,
    "Updates device Firmware. Use parameter 'FwPackageUri' to specifiy the URI of the firmware file, e.g. https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin",
    new[] { new Parameter("FwPackageUri", "string") }
));
```

Este fragmento de código permite agregar los detalles del método **InitiateFirmwareUpdate**, incluido el texto para mostrar en el portal de solución, y los detalles sobre los parámetros de método necesarios.

Al iniciarse, el simulador envía propiedades notificadas, incluida la lista de métodos admitidos, a IoT Hub.

Agregue un controlador al código del simulador de cada método que admite. Puede ver los controladores existentes en la clase **CoolerDevice** del proyecto Simulator.WebJob. En el ejemplo siguiente se muestra el controlador del método **InitiateFirmwareUpdate**:

```csharp
public async Task<MethodResponse> OnInitiateFirmwareUpdate(MethodRequest methodRequest, object userContext)
{
    if (_deviceManagementTask != null && !_deviceManagementTask.IsCompleted)
    {
        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = "Device is busy"
        }, 409));
    }

    try
    {
        var operation = new FirmwareUpdate(methodRequest);
        _deviceManagementTask = operation.Run(Transport).ContinueWith(async task =>
        {
            // after firmware completed, we reset telemetry
            var telemetry = _telemetryController as ITelemetryWithTemperatureMeanValue;
            if (telemetry != null)
            {
                telemetry.TemperatureMeanValue = 34.5;
            }

            await UpdateReportedTemperatureMeanValue();
        });

        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = "FirmwareUpdate accepted",
            Uri = operation.Uri
        }));
    }
    catch (Exception ex)
    {
        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = ex.Message
        }, 400));
    }
}
```

Los nombres de controlador de método deben empezar por `On` seguido del nombre del método. El parámetro **methodRequest** contiene todos los parámetros pasados con la invocación del método desde el back-end de soluciones. El valor devuelto debe ser del tipo **Task&lt;RespuestaDelMétodo&gt;**. El método de la utilidad **BuildMethodResponse** le ayudará a crear el valor devuelto.

Dentro del controlador de método, puede:

- Iniciar una tarea asincrónica.
- Recuperar las propiedades deseadas desde el *dispositivo gemelo* de IoT Hub.
- Actualizar una única propiedad notificada mediante el método **SetReportedPropertyAsync** de la clase **CoolerDevice**.
- Actualizar varias propiedades notificadas mediante la creación de una instancia de **TwinCollection** y la llamada al método **Transport.UpdateReportedPropertiesAsync**.

El ejemplo de actualización de firmware anterior realiza los pasos siguientes:

- Comprueba que el dispositivo puede aceptar la solicitud de actualización de firmware.
- Inicia la operación de actualización de firmware de forma asincrónica y restablece la telemetría cuando se completa la operación.
- Devuelve inmediatamente el mensaje "FirmwareUpdate aceptado" para indicar que el dispositivo aceptó la solicitud.

### <a name="build-and-use-your-own-physical-device"></a>Creación y uso de dispositivos propios (físicos)

Los [SDK de IoT de Azure](https://github.com/Azure/azure-iot-sdks) proporcionan bibliotecas que permiten conectar distintos tipos de dispositivos (lenguajes y sistemas operativos) a soluciones IoT.

## <a name="modify-dashboard-limits"></a>Modificación de los límites de panel

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Número de dispositivos que se muestran en la lista desplegable del panel

El valor predeterminado es 200. Puede cambiar este número en [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Número de chinchetas que se muestran en el control de Bing Maps

El valor predeterminado es 200. Puede cambiar este número en [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Periodo del gráfico de telemetría

El valor predeterminado es 10 minutos. Puede cambiar este valor en [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="feedback"></a>Comentarios

¿Tiene una personalización que le gustaría que se tratara en este documento? Agregue las sugerencias de características a [User Voice](https://feedback.azure.com/forums/321918-azure-iot) o comente este artículo. 

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre las opciones para personalizar las soluciones preconfiguradas, consulte estos artículos:

* [Tutorial: Conexión de una aplicación lógica a la solución preconfigurada de supervisión remota del Conjunto de aplicaciones de IoT de Azure][lnk-logicapp]
* [Uso de telemetría dinámica con la solución de supervisión remota preconfigurada][lnk-dynamic]
* [Metadatos de información de dispositivo en la solución preconfigurada de supervisión remota][lnk-devinfo]
* [Customize how the connected factory solution displays data from your OPC UA servers][lnk-cf-customize] (Personalización de cómo muestra la solución de fábrica conectada los datos de los servidores de OPC UA)

[lnk-logicapp]: iot-suite-v1-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-v1-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[IoT Device SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-cf-customize]: iot-suite-connected-factory-customize.md