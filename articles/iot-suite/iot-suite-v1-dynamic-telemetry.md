---
title: "Telemetría dinámica | Microsoft Docs"
description: "Siga este tutorial para aprender a usar la telemetría dinámica con la solución de supervisión remota preconfigurada del Conjunto de aplicaciones de IoT de Azure."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 60e9ee00fabf15a62e782c70bca251b1a8e617c3
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>Uso de telemetría dinámica con la solución de supervisión remota preconfigurada

La telemetría dinámica le permite visualizar los datos de telemetría enviados a la solución preconfigurada de supervisión remota. Los dispositivos simulados que se implementan con la solución preconfigurada envían datos de temperatura y humedad de telemetría que puede visualizar en el panel. Si personaliza los dispositivos simulados existentes, crea nuevos dispositivos simulados o conecta dispositivos físicos a la solución preconfigurada, podrá enviar otros valores de telemetría, como la temperatura exterior, RPM o la velocidad del viento. A continuación podrá visualizar en el panel estos datos de telemetría adicionales.

Este tutorial usa un dispositivo simulado Node.js simple que puede modificar fácilmente para experimentar con la telemetría dinámica.

Para completar este tutorial necesitará lo siguiente:

* Una suscripción de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte la [evaluación gratuita de Azure][lnk_free_trial].
* [Node.js][lnk-node] versión 0.12.x, o posteriores.

Puede completar este tutorial en cualquier sistema operativo, como Windows o Linux, donde pueda instalar Node.js.

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

[!INCLUDE [iot-suite-v1-send-external-temperature](../../includes/iot-suite-v1-send-external-temperature.md)]

## <a name="add-a-telemetry-type"></a>Adición de un tipo de datos de telemetría

El siguiente paso es reemplazar los datos de telemetría generados por el dispositivo simulado de Node.js con un nuevo conjunto de valores:

1. Presione **Ctrl + C** en el símbolo del sistema o el shell para detener el dispositivo simulado de Node.js.
2. En el archivo remote_monitoring.js, verá los valores de la base de datos de telemetría para la temperatura, la humedad y la temperatura exterior existentes. Agregue un valor de la base de datos para **rpm** como sigue:

    ```nodejs
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. El dispositivo simulado de Node.js usa la función **generateRandomIncrement** del archivo remote_monitoring.js para agregar un incremento aleatorio a los valores de la base de datos. Aleatorice el valor de **rpm** agregando una línea de código después de la aleatorizaciones existentes como sigue:

    ```nodejs
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. Agregue el nuevo valor de rpm para la carga útil de JSON, que el dispositivo envía a IoT Hub:

    ```nodejs
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. Ejecute el dispositivo simulado de Node.js con el siguiente comando:

    `node remote_monitoring.js`

6. Observe el nuevo tipo de datos de telemetría RPM que se muestra en el gráfico en el panel:

![RPM agregado al panel][image3]

> [!NOTE]
> Puede que necesite deshabilitar y habilitar el dispositivo de Node.js en la página **Dispositivos** del panel para ver el cambio inmediatamente.

## <a name="customize-the-dashboard-display"></a>Personalización de la presentación en el panel

El mensaje **Device-Info** puede incluir metadatos sobre la telemetría que el dispositivo envíe a IoT Hub. Estos metadatos pueden especificar los tipos de datos de telemetría que envía el dispositivo. Modifique el valor **deviceMetaData** en el archivo remote_monitoring.js para que incluya una definición de **telemetría** después de la definición de **comandos**. El siguiente fragmento de código muestra la definición **Comandos** (asegúrese de agregar un `,` después de la definición **Comandos**):

```nodejs
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [!NOTE]
> La solución de supervisión remota utiliza a una coincidencia de mayúsculas y minúsculas para comparar la definición de metadatos con los datos del flujo de telemetría.


Agregar una definición de **telemetría** como la del fragmento de código anterior no cambia el comportamiento del panel. Sin embargo, los metadatos también pueden incluir los un atributo **DisplayName** para personalizar la presentación en el panel. Actualice la definición de los metadatos de **telemetría** como se muestra en el siguiente fragmento:

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

En la captura de pantalla siguiente se muestra cómo este cambio modifica la leyenda del gráfico en el panel:

![Personalización de la leyenda del gráfico][image4]

> [!NOTE]
> Puede que necesite deshabilitar y habilitar el dispositivo de Node.js en la página **Dispositivos** del panel para ver el cambio inmediatamente.

## <a name="filter-the-telemetry-types"></a>Filtrado de los tipos de datos de telemetría

De forma predeterminada, el gráfico del panel muestra las series de datos en el flujo de telemetría. Puede usar los metadatos de **Device-Info** para suprimir la presentación de determinados tipos de datos de telemetría en el gráfico. 

Para que en el gráfico se muestren solo los datos de telemetría relativos a la temperatura y la humedad, omita el dato de **ExternalTemperature** de los metadatos de **telemetría** de **Device-Info** como sigue:

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

La **temperatura exterior** ya no aparece en el gráfico:

![Datos de telemetría filtrados en el panel][image5]

Este cambio solo afecta a la visualización del gráfico. Los valores de datos de **ExternalTemperature** siguen almacenados y están disponibles para cualquier procesamiento de back-end.

> [!NOTE]
> Puede que necesite deshabilitar y habilitar el dispositivo de Node.js en la página **Dispositivos** del panel para ver el cambio inmediatamente.

## <a name="handle-errors"></a>errores

Para que el gráfico muestre el flujo de datos, su **Type** en los metadatos de **Device-Info** debe coincidir con el tipo de datos de los valores de telemetría. Por ejemplo, si los metadatos que especifican que el **tipo** de datos de humedad es **int** y se encuentra **double** en el flujo de telemetría, los datos de telemetría relativos a la humedad no se muestran en el gráfico. Sin embargo, los valores de **Humidity** (Humedad) siguen almacenados y están disponibles para cualquier procesamiento de back-end.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ya sabe cómo utilizar la telemetría dinámico, puede obtener más información sobre cómo las soluciones preconfiguradas emplean la información de los dispositivos: [Metadatos de información de dispositivo en la solución preconfigurada de supervisión remota][lnk-devinfo].

[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[image3]: media/iot-suite-v1-dynamic-telemetry/image3.png
[image4]: media/iot-suite-v1-dynamic-telemetry/image4.png
[image5]: media/iot-suite-v1-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
