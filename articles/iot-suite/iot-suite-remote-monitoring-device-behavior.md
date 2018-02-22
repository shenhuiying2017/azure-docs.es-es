---
title: "Comportamiento de un dispositivo simulado en la solución de supervisión remota: Azure | Microsoft Docs"
description: "Este artículo describe cómo usar JavaScript para definir el comportamiento de un dispositivo simulado en la solución de supervisión remota."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/29/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: e5846893166c3e65b75e84d02849c2b8ab78e079
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="implement-the-device-model-behavior"></a>Implementación del comportamiento de modelo del dispositivo

El artículo [Descripción del esquema de modelo del dispositivo](iot-suite-remote-monitoring-device-schema.md) describe el esquema que define un modelo de dispositivo simulado. Ese artículo hace referencia a dos tipos de archivo de JavaScript que implementan el comportamiento de un dispositivo simulado:

- Archivos JavaScript de **estado** que se ejecutan a intervalos fijos para actualizar el estado interno del dispositivo.
- Archivos JavaScript de **Método** que se ejecutan cuando la solución invoca un método en el dispositivo.

En este artículo, aprenderá a:

>[!div class="checklist"]
> * Controlar el estado de un dispositivo simulado
> * Definir cómo responde un dispositivo simulado a una llamada de método de la solución de supervisión remota
> * Depurar los scripts

## <a name="state-behavior"></a>Comportamiento de estado

La sección [Simulation](iot-suite-remote-monitoring-device-schema.md#simulation) del esquema de modelo de dispositivo define el estado interno de un dispositivo simulado:

- `InitialState` define los valores iniciales de todas las propiedades del objeto de estado del dispositivo.
- `Script` identifica un archivo de JavaScript que se ejecuta según una programación para actualizar el estado del dispositivo.

En el ejemplo siguiente se muestra la definición del objeto de estado del dispositivo en un dispositivo refrigerador simulado:

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Script": {
    "Type": "javascript",
    "Path": "chiller-01-state.js",
    "Interval": "00:00:05"
  }
}
```

El servicio de simulación mantiene en memoria el estado del dispositivo simulado, tal como se define en la sección `InitialState`. La información de estado se pasa como entrada para la función `main` definida en **chiller-01-state.js**. En este ejemplo el servicio de simulación ejecuta el archivo **chiller-01-state.js** cada cinco segundos. El script puede modificar el estado del dispositivo simulado.

Lo siguiente muestra el esquema de una función `main` típica:

```javascript
function main(context, previousState) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

El parámetro `context` tiene las siguientes propiedades:

- `currentTime` como una cadena con formato `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, por ejemplo `Simulated.Chiller.123`
- `deviceModel`, por ejemplo `Chiller`

El parámetro `state` contiene el estado del dispositivo como mantenido por el servicio de simulación del dispositivo. Este valor es el objeto `state` devuelto por la llamada anterior a `main`.

El ejemplo siguiente muestra una implementación típica del método `main` para controlar el estado del dispositivo mantenido por el servicio de simulación:

```javascript
// Default state
var state = {
  online: true,
  temperature: 75.0,
  temperature_unit: "F",
  humidity: 70.0,
  humidity_unit: "%",
  pressure: 150.0,
  pressure_unit: "psig",
  simulation_state: "normal_pressure"
};

function restoreState(previousState) {
  // If the previous state is null, force a default state
  if (previousState !== undefined && previousState !== null) {
      state = previousState;
  } else {
      log("Using default state");
  }
}

function main(context, previousState) {

  restoreState(previousState);

  // Update state

  return state;
}
```

El siguiente ejemplo se muestra cómo el método `main` puede simular los valores de telemetría que varían con el tiempo:

```javascript
/**
 * Simple formula generating a random value
 * around the average and between min and max
 */
function vary(avg, percentage, min, max) {
  var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
  value = Math.max(value, min);
  value = Math.min(value, max);
  return value;
}


function main(context, previousState) {

    restoreState(previousState);

    // 75F +/- 5%,  Min 25F, Max 100F
    state.temperature = vary(75, 5, 25, 100);

    // 70% +/- 5%,  Min 2%, Max 99%
    state.humidity = vary(70, 5, 2, 99);

    log("Simulation state: " + state.simulation_state);
    if (state.simulation_state === "high_pressure") {
        // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
        state.pressure = vary(250, 25, 50, 300);
    } else {
        // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
        state.pressure = vary(150, 10, 50, 300);
    }

    return state;
}
```

Puede ver el archivo [refrigerador-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) completo en Github.

## <a name="method-behavior"></a>Comportamiento de método

La sección [CloudToDeviceMethods](iot-suite-remote-monitoring-device-schema.md#cloudtodevicemethods) del esquema de modelo de dispositivo define los métodos a los que responde un dispositivo simulado.

En el ejemplo siguiente se muestra la lista de métodos admitidos por un dispositivo refrigerador simulado:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "FirmwareUpdate": {
    "Type": "javascript",
    "Path": "FirmwareUpdate-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

Cada método tiene un archivo JavaScript asociado que implementa el comportamiento del método.

El servicio de simulación mantiene en memoria el estado del dispositivo simulado, tal como se define en la sección `InitialState` del esquema. La información de estado se pasa como entrada a la función `main` definida en el archivo JavaScript cuando se llama al método. El script puede modificar el estado del dispositivo simulado.

Lo siguiente muestra el esquema de una función `main` típica:

```javascript
function main(context, previousState) {

}
```

El parámetro `context` tiene las siguientes propiedades:

- `currentTime` como una cadena con formato `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, por ejemplo `Simulated.Chiller.123`
- `deviceModel`, por ejemplo `Chiller`

El parámetro `state` contiene el estado del dispositivo como mantenido por el servicio de simulación del dispositivo.

Hay dos funciones globales que se puede usar para ayudar a implementar el comportamiento del método:

- `updateState` para actualizar el estado mantenido por el servicio de simulación.
- `sleep` para detener la ejecución para simular una tarea de ejecución prolongada.

En el ejemplo siguiente se muestra una versión abreviada del script **IncreasePressure method.js** utilizado por los dispositivos de refrigerador simulados:

```javascript
function main(context, previousState) {

    log("Starting 'Increase Pressure' method simulation (5 seconds)");

    // Pause the simulation and change the simulation mode so that the
    // temperature will fluctuate at ~250 when it resumes
    var state = {
        simulation_state: "high_pressure",
        CalculateRandomizedTelemetry: false
    };
    updateState(state);

    // Increase
    state.pressure = 210;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Increase
    state.pressure = 250;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Resume the simulation
    state.CalculateRandomizedTelemetry = true;
    updateState(state);

    log("'Increase Pressure' method simulation completed");
}
```

## <a name="debugging-script-files"></a>Depuración de archivos de script

No es posible adjuntar un depurador al intérprete de Javascript usado por el servicio de simulación de dispositivo para ejecutar scripts de estado y de método. De todas formas, puede registrar información en el registro del servicio. La función `log()` integrada le permite guardar información para realizar un seguimiento y depurar la ejecución de la función.

Si se produce un error de sintaxis, el intérprete no funciona y escribe una entrada `Jint.Runtime.JavaScriptException` en el registro de servicio.

El artículo sobre la [creación de un dispositivo simulado](iot-suite-remote-monitoring-test.md) muestra cómo se ejecuta el servicio de simulación del dispositivo de forma local. La ejecución local del servicio facilita la depuración de los dispositivos simulados antes de implementarlos en la nube.

## <a name="next-steps"></a>pasos siguientes

En este artículo se describe cómo definir el comportamiento de su propio modelo de dispositivo simulado personalizado. En este artículo le mostramos cómo:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Controlar el estado de un dispositivo simulado
> * Definir cómo responde un dispositivo simulado a una llamada de método de la solución de supervisión remota
> * Depurar los scripts

Ahora que ha aprendido cómo especificar el comportamiento de un dispositivo simulado, el siguiente paso que se sugiere es aprender cómo [crear un dispositivo simulado](iot-suite-remote-monitoring-test.md).

Para más información sobre la solución de supervisión remota para los desarrolladores, consulte:

* [Guía de referencia para desarrolladores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guía de solución de problemas para desarrolladores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->