---
title: "Creación de una regla personalizada en Conjunto de aplicaciones de IoT de Azure | Microsoft Docs"
description: "Cómo crear una regla personalizada en una solución preconfigurada de Conjunto de aplicaciones de IoT."
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
ms.openlocfilehash: 9bf2a13035de141766fd935966ce18459dccdaab
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="create-a-custom-rule-in-the-remote-monitoring-preconfigured-solution"></a>Creación de una regla personalizada en la solución preconfigurada de supervisión remota

## <a name="introduction"></a>Introducción

En las soluciones preconfiguradas, puede configurar [reglas que se desencadenan cuando un valor de telemetría de un dispositivo alcanza un umbral específico][lnk-builtin-rule]. [Uso de telemetría dinámica con la solución de supervisión remota preconfigurada][lnk-dynamic-telemetry] describe cómo se pueden agregar a la solución valores de telemetría personalizados, como *ExternalTemperature*. En este artículo se muestra cómo crear una regla personalizada para los tipos de telemetría dinámica en la solución.

En este tutorial se usa un simple dispositivo simulado de Node.js para generar telemetría dinámica y enviarla al back-end de la solución preconfigurada. Luego agrega reglas personalizadas en la solución **RemoteMonitoring** de Visual Studio e implementa este back-end personalizado a su suscripción de Azure.

Para completar este tutorial, necesita:

* Una suscripción de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte la [evaluación gratuita de Azure][lnk_free_trial].
* [Node.js][lnk-node] versión 0.12.x o posterior para crear un dispositivo simulado.
* Microsoft Visual Studio 2015 o Visual Studio 2017 para modificar el back-end de la solución preconfigurada con las reglas nuevas.

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

Anote el nombre de la solución que eligió para su implementación. Necesitará el nombre de esta solución más adelante en el tutorial.

[!INCLUDE [iot-suite-v1-send-external-temperature](../../includes/iot-suite-v1-send-external-temperature.md)]

Puede detener la aplicación de consola de Node.js una vez que compruebe que envía la telemetría **ExternalTemperature** a la solución preconfigurada. Mantenga abierta la ventana de la consola porque volverá a ejecutar esta aplicación de consola de Node.js una vez que agregue la regla personalizada a la solución.

## <a name="rule-storage-locations"></a>Ubicaciones de almacenamiento de las reglas

La información sobre las reglas se mantiene en dos ubicaciones:

* Tabla **DeviceRulesNormalizedTable**. Esta tabla almacena una referencia normalizada de las reglas definidas por el portal de soluciones. Cuando el portal de soluciones muestra las reglas del dispositivo, consulta las definiciones de las reglas en esta tabla.
* Blob **DeviceRules**. Este blob almacena todas las reglas definidas para todos los dispositivos registrados y se define con una entrada de referencia a los trabajos de Azure Stream Analytics.
 
Cuando analiza una regla existente o define una nueva en el portal de soluciones, tanto la tabla como el blob se actualizan para reflejar los cambios. La definición de regla que aparece en el portal proviene del almacén de tablas y la definición de regla a la que hacen referencia los trabajos de Stream Analytics proviene del blob. 

## <a name="update-the-remotemonitoring-visual-studio-solution"></a>Actualización de la solución RemoteMonitoring de Visual Studio

Los pasos siguientes indican cómo modificar la solución RemoteMonitoring de Visual Studio para incluir una regla nueva que usa la telemetría **ExternalTemperature** enviada desde el dispositivo simulado:

1. Si todavía no lo ha hecho, clone el repositorio **azure-iot-remote-monitoring** en una ubicación adecuada de la máquina local mediante el siguiente comando Git:

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. En Visual Studio, abra el archivo RemoteMonitoring.sln desde la copia local del repositorio **azure-iot-remote-monitoring**.

3. Abra el archivo Infrastructure\Models\DeviceRuleBlobEntity.cs y agregue una propiedad **ExternalTemperature** de la siguiente manera:

    ```csharp
    public double? Temperature { get; set; }
    public double? Humidity { get; set; }
    public double? ExternalTemperature { get; set; }
    ```

4. En el mismo archivo, agregue una propiedad **ExternalTemperatureRuleOutput** como se indica a continuación:

    ```csharp
    public string TemperatureRuleOutput { get; set; }
    public string HumidityRuleOutput { get; set; }
    public string ExternalTemperatureRuleOutput { get; set; }
    ```

5. Abra el archivo Infrastructure\Models\DeviceRuleDataFields.cs y agregue la siguiente propiedad **ExternalTemperature** después de la propiedad **Humidity** existente:

    ```csharp
    public static string ExternalTemperature
    {
        get { return "ExternalTemperature"; }
    }
    ```

6. En el mismo archivo, actualice el método **_availableDataFields** para incluir **ExternalTemperature** de la manera siguiente:

    ```csharp
    private static List<string> _availableDataFields = new List<string>
    {                    
        Temperature, Humidity, ExternalTemperature
    };
    ```

7. Abra el archivo Infrastructure\Repository\DeviceRulesRepository.cs y modifique el método **BuildBlobEntityListFromTableRows** como se indica a continuación:

    ```csharp
    else if (rule.DataField == DeviceRuleDataFields.Humidity)
    {
        entity.Humidity = rule.Threshold;
        entity.HumidityRuleOutput = rule.RuleOutput;
    }
    else if (rule.DataField == DeviceRuleDataFields.ExternalTemperature)
    {
      entity.ExternalTemperature = rule.Threshold;
      entity.ExternalTemperatureRuleOutput = rule.RuleOutput;
    }
    ```

## <a name="rebuild-and-redeploy-the-solution"></a>Recompile y vuelva a implementar la solución.

Ahora puede implementar la solución actualizada en su suscripción de Azure.

1. Abra un símbolo del sistema con privilegios elevados y vaya a la raíz de la copia local del repositorio azure-iot-remote-monitoring.

2. Para implementar la solución actualizada, ejecute el comando siguiente para sustituir **{nombre de implementación}** por el nombre de implementación de la solución preconfigurada que anotó anteriormente:

    ```
    build.cmd cloud release {deployment name}
    ```

## <a name="update-the-stream-analytics-job"></a>Actualización del trabajo de Stream Analytics

Cuando se complete la implementación, podrá actualizar el trabajo de Stream Analytics para usar las nuevas definiciones de regla.

1. En Azure Portal, vaya al grupo de recursos que contiene los recursos de la solución preconfigurada. Este grupo de recursos tiene el mismo nombre que especificó para la solución durante la implementación.

2. Navegue al trabajo {nombre de implementación}-Rules de Stream Analytics. 

3. Haga clic en **Detener** para detener la ejecución del trabajo de Stream Analytics. (Debe esperar a que el trabajo de transmisión se detenga antes de editar la consulta).

4. Haga clic en **Consulta**. Edite la consulta para incluir la instrucción **SELECT** para **ExternalTemperature**. En el ejemplo siguiente se muestra la consulta completa con la nueva instrucción **SELECT**:

    ```
    WITH AlarmsData AS 
    (
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Temperature' as ReadingType,
         Stream.Temperature as Reading,
         Ref.Temperature as Threshold,
         Ref.TemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Humidity' as ReadingType,
         Stream.Humidity as Reading,
         Ref.Humidity as Threshold,
         Ref.HumidityRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'ExternalTemperature' as ReadingType,
         Stream.ExternalTemperature as Reading,
         Ref.ExternalTemperature as Threshold,
         Ref.ExternalTemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.ExternalTemperature IS NOT null AND Stream.ExternalTemperature > Ref.ExternalTemperature
    )
     
    SELECT *
    INTO DeviceRulesMonitoring
    FROM AlarmsData
     
    SELECT *
    INTO DeviceRulesHub
    FROM AlarmsData
    ```

5. Haga clic en **Guardar** para cambiar la consulta de regla actualizada.

6. Haga clic en **Iniciar** que la ejecución del trabajo de Stream Analytics se inicie nuevamente.

## <a name="add-your-new-rule-in-the-dashboard"></a>Incorporación de la regla nueva en el panel

Ahora puede agregar la regla **ExternalTemperature** a un dispositivo en el panel de la solución.

1. Vaya al portal de soluciones.

2. Navegue al panel **Dispositivos**.

3. Busque el dispositivo personalizado que creó que envía la telemetría **ExternalTemperature** y en el panel **Detalles del dispositivo**, haga clic en **Agregar regla**.

4. Seleccione **ExternalTemperature** en **Campo de datos**.

5. Establezca **Umbral** en 56. Luego, haga clic en **Guardar y ver reglas**.

6. Vuelva al panel para ver el historial de alarmas.

7. En la ventana de la consola que dejó abierto, inicie la aplicación de consola de Node.js para comenzar a enviar los datos de telemetría **ExternalTemperature**.

8. Observe que la tabla **Historial de alarmas** muestra alarmas nuevas cuando se desencadena la regla nueva.
 
## <a name="additional-information"></a>Información adicional

Cambiar el operador **>** es más complejo y va más allá de los pasos descritos en este tutorial. A pesar de que puede cambiar el trabajo de Stream Analytics para usar el operador que prefiera, reflejar ese operador en el portal de soluciones es una tarea más compleja. 

## <a name="next-steps"></a>Pasos siguientes
Ahora que sabe cómo crear reglas personalizadas, puede obtener más información sobre las soluciones preconfiguradas:

- [Conexión de una aplicación lógica a la solución preconfigurada de supervisión remota de Conjunto de aplicaciones de IoT de Azure][lnk-logic-app]
- [Metadatos de información de dispositivo en la solución preconfigurada de supervisión remota][lnk-devinfo].

[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[lnk-builtin-rule]: iot-suite-v1-getstarted-preconfigured-solutions.md#view-alarms
[lnk-dynamic-telemetry]: iot-suite-v1-dynamic-telemetry.md
[lnk-logic-app]: iot-suite-v1-logic-apps-tutorial.md