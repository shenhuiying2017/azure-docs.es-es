---
title: "Telemetría dinámica | Microsoft Docs"
description: "Siga este tutorial para aprender a usar la telemetría dinámica con la solución de supervisión remota preconfigurada."
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
ms.date: 11/16/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 1a6dd35278f0a4a4f972642c40a0976986dd79ae
ms.openlocfilehash: 000bc72e5c880abdd455d50cd7ac0bb57a51391d


---
# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>Uso de telemetría dinámica con la solución de supervisión remota preconfigurada
## <a name="introduction"></a>Introducción
La telemetría dinámica le permite visualizar los datos de telemetría enviados a la solución preconfigurada de supervisión remota. Los dispositivos simulados que se implementan con la solución preconfigurada envían datos de temperatura y humedad de telemetría que puede visualizar en el panel. Si personaliza los dispositivos simulados existentes, crea nuevos dispositivos simulados o conecta dispositivos físicos a la solución preconfigurada, podrá enviar otros valores de telemetría, como la temperatura exterior, RPM o la velocidad del viento. A continuación podrá visualizar en el panel estos datos de telemetría adicionales.

Este tutorial usa un dispositivo simulado Node.js simple que puede modificar fácilmente para experimentar con la telemetría dinámica.

Para completar este tutorial necesitará lo siguiente:

* Una suscripción de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte [Evaluación gratuita de Azure][lnk_free_trial].
* [Node.js][lnk-node] versión 0.12.x o posteriores.

Puede completar este tutorial en cualquier sistema operativo, como Windows o Linux, donde pueda instalar Node.js.

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="configure-the-nodejs-simulated-device"></a>Configuración del dispositivo simulado de Node.js
1. En el panel de supervisión remota, haga clic en **+ Agregar un dispositivo** y agregue un dispositivo personalizado. Tome nota del nombre de host del Centro de IoT, del identificador de dispositivo y de la clave de dispositivo. Los necesitará más adelante en este tutorial al preparar la aplicación cliente del dispositivo remote_monitoring.js.
2. Asegúrese de que tiene instalada la versión 0.12.x o posterior de Node.js en el equipo de desarrollo. Ejecute `node --version` en un símbolo del sistema o en un shell para comprobar la versión. Para obtener información sobre el uso de un administrador de paquetes para instalar Node.js en Linux, consulte [Installing Node.js via package manager][node-linux] (Instalación de Node.js con un administrador de paquetes).
3. Con Node.js instalado, clone la versión más reciente del repositorio [azure-iot-sdks][lnk-github-repo] en el equipo de desarrollo. Para ver la versión más reciente de las bibliotecas y los ejemplos siempre debe usar siempre la rama **principal** .
4. Desde su copia local del repositorio [azure-iot-sdks][lnk-github-repo], copie los siguientes dos archivos de la carpeta node/device/samples a una carpeta vacía de su equipo de desarrollo:
   
   * packages.json
   * remote_monitoring.js
5. Abra el archivo remote-monitoring.js y busque la siguiente definición de variable:
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
6. Reemplace **[IoT Hub device connection string]** con la cadena de conexión al dispositivo. Use los valores para el nombre de host del Centro de IoT, el identificador de dispositivo y la clave de dispositivo que anotó en el paso 1. Una cadena de conexión de dispositivo tiene el siguiente formato:
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    Si su nombre de host de IoT Hub es **contoso** y el id. del dispositivo es **mydevice**, su cadena de conexión tendrá este aspecto:
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
7. Guarde el archivo . Para instalar los paquetes necesarios, ejecute los siguientes comandos en un shell o símbolo del sistema de la carpeta que los contenga y ejecute la aplicación de ejemplo:
   
    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>Observación de la telemetría dinámica en acción
El panel muestra los datos de telemetría relativos a la temperatura y humedad de los dispositivos simulados existentes:

![Panel predeterminado][image1]

Si selecciona el dispositivo simulado de Node.js que se ejecutó en la sección anterior, verá los datos de telemetría relativos a la temperatura, la humedad y la temperatura externa:

![Temperatura exterior agregada al panel][image2]

La solución de supervisión remota detecta automáticamente el tipo de datos adicionales de telemetría relativos a la temperatura exterior y los agrega al gráfico del panel.

## <a name="add-a-telemetry-type"></a>Adición de un tipo de datos de telemetría
El siguiente paso es reemplazar los datos de telemetría generados por el dispositivo simulado de Node.js con un nuevo conjunto de valores:

1. Presione **Ctrl + C** en el símbolo del sistema o el shell para detener el dispositivo simulado de Node.js.
2. En el archivo remote_monitoring.js, verá los valores de la base de datos de telemetría para la temperatura, la humedad y la temperatura exterior existentes. Agregue un valor de la base de datos para **rpm** como sigue:
   
    ```
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```
3. El dispositivo simulado de Node.js usa la función **generateRandomIncrement** del archivo remote_monitoring.js para agregar un incremento aleatorio a los valores de la base de datos. Aleatorice el valor de **rpm** agregando una línea de código después de la aleatorizaciones existentes como sigue:
   
    ```
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```
4. Agregue el nuevo valor de rpm para la carga útil de JSON, que el dispositivo envía al Centro de IoT:
   
    ```
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```
5. Ejecute el dispositivo simulado de Node.js con el siguiente comando:
   
    ```
    node remote_monitoring.js
    ```
6. Observe el nuevo tipo de datos de telemetría RPM que se muestra en el gráfico en el panel:

![RPM agregado al panel][image3]

> [!NOTE]
> Puede que necesite deshabilitar y habilitar el dispositivo de Node.js en la página **Dispositivos** del panel para ver el cambio inmediatamente.
> 
> 

## <a name="customize-the-dashboard-display"></a>Personalización de la presentación en el panel
El mensaje **Device-Info** puede incluir metadatos sobre la telemetría que el dispositivo envíe al Centro de IoT. Estos metadatos pueden especificar los tipos de datos de telemetría que envía el dispositivo. Modifique el valor **deviceMetaData** en el archivo remote_monitoring.js para que incluya una definición de **telemetría** después de la definición de **comandos**. El siguiente fragmento de código muestra la definición **Comandos** (asegúrese de agregar un `,` después de la definición **Comandos**):

```
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
> 
> 

Agregar una definición de **telemetría** como la del fragmento de código anterior no cambia el comportamiento del panel. Sin embargo, los metadatos también pueden incluir los un atributo **DisplayName** para personalizar la presentación en el panel. Actualice la definición de los metadatos de **telemetría** como se muestra en el siguiente fragmento:

```
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
> 
> 

## <a name="filter-the-telemetry-types"></a>Filtrado de los tipos de datos de telemetría
De forma predeterminada, el gráfico del panel muestra las series de datos en el flujo de telemetría. Puede usar los metadatos de **Device-Info** para suprimir la presentación de determinados tipos de datos de telemetría en el gráfico. 

Para que en el gráfico se muestren solo los datos de telemetría relativos a la temperatura y la humedad, omita el dato de **ExternalTemperature** de los metadatos de **telemetría** de **Device-Info** como sigue:

```
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
> 
> 

## <a name="handle-errors"></a>errores
Para que el gráfico muestre el flujo de datos, su **Type** en los metadatos de **Device-Info** debe coincidir con el tipo de datos de los valores de telemetría. Por ejemplo, si los metadatos que especifican que el **tipo** de datos de humedad es **int** y se encuentra **double** en el flujo de telemetría, los datos de telemetría relativos a la humedad no se muestran en el gráfico. Sin embargo, los valores de **Humidity** (Humedad) siguen almacenados y están disponibles para cualquier procesamiento de back-end.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ya sabe cómo utilizar la telemetría dinámica, puede obtener más información sobre cómo las soluciones preconfiguradas emplean la información de los dispositivos: [Metadatos de información de dispositivo en la solución preconfigurada de supervisión remota][lnk-devinfo].

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[imagen1]: media/iot-suite-dynamic-telemetry/image1.png
[Imagen 2]: media/iot-suite-dynamic-telemetry/image2.png
[Imagen 3]: media/iot-suite-dynamic-telemetry/image3.png
[Imagen 4]: media/iot-suite-dynamic-telemetry/image4.png
[Imagen 5]: media/iot-suite-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdks



<!--HONumber=Nov16_HO3-->


