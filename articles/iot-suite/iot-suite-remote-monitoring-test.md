---
title: "Simulación de dispositivos en la solución de supervisión remota: Azure | Microsoft Docs"
description: "En este tutorial se muestra cómo usar el simulador de dispositivos con la solución preconfigurada de la supervisión remota."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/16/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 732ec45003481b0e2f2eca03b6ae13772d325ef1
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="test-your-solution-with-simulated-devices"></a>Prueba de la solución con dispositivos simulados

En este tutorial se muestra cómo usar y personalizar el microservicio del simulador de dispositivos con la solución preconfigurada de la supervisión remota. Para mostrar las funcionalidades del simulador de dispositivos, en este tutorial se usan dos escenarios en la aplicación IoT de Contoso.

En el primer escenario, Contoso desea probar un nuevo dispositivo de bombilla inteligente. Para realizar las pruebas, crea un dispositivo simulado nuevo con las características siguientes:

*Propiedades*

| Nombre                     | Valores                      |
| ------------------------ | --------------------------- |
| Color                    | Blanco, rojo, azul            |
| Brillo               | De 0 a 100                    |
| Vida restante estimada | Cuenta atrás desde 10 000 horas |

*Telemetría*

La tabla siguiente muestra los datos que la bombilla informe a la nube como flujo de datos:

| Nombre   | Valores      |
| ------ | ----------- |
| Estado | "on", "off" |

*Métodos*

La tabla siguiente muestra las acciones que admite el dispositivo nuevo:

| Nombre        |
| ----------- |
| Encender   |
| Apagar  |

*Estado inicial*

La tabla siguiente muestra el estado inicial del dispositivo:

| Nombre                     | Valores |
| ------------------------ | -------|
| Color inicial            | Blanco  |
| Brillo inicial       | 75     |
| Vida restante inicial   | 10.000 |
| Estado de telemetría inicial | "on"   |

En el segundo escenario, agrega un tipo de telemetría nuevo al dispositivo **Refrigerador** existente de Contoso.

En este tutorial se muestra cómo usar el simulador de dispositivos con la solución preconfigurada de la supervisión remota:

En este tutorial, aprenderá a:

>[!div class="checklist"]
> * Crear un tipo de dispositivo nuevo
> * Simular el comportamiento de un dispositivo personalizado
> * Agregar un tipo de dispositivo nuevo al panel
> * Enviar telemetría personalizada desde un tipo de dispositivo existente

## <a name="prerequisites"></a>Requisitos previos

Para seguir este tutorial, necesitará una instancia implementada de la solución de supervisión remota en la suscripción de Azure.

Si aún no ha implementado la solución de supervisión remota, debe completar el tutorial [Implementación de la solución preconfigurada de supervisión remota](iot-suite-remote-monitoring-deploy.md).

<!-- Dominic please this use as your reference https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models -->

## <a name="the-device-simulation-service"></a>El servicio de simulación de dispositivo

El servicio de simulación de dispositivo de la solución preconfigurada permite hacer cambios en los tipos de dispositivo simulados integrados y crear tipos de dispositivos simulados nuevos. Puede usar tipos de dispositivos personalizados para probar el comportamiento de la solución de supervisión remota antes de conectar los dispositivos físicos a la solución.

## <a name="create-a-simulated-device-type"></a>Creación de un tipo de dispositivo simulado

La forma más sencilla de crear un tipo de dispositivo nuevo en el microservicio de simulación es copiar y modificar un tipo existente. Los pasos siguientes muestran cómo copiar el dispositivo **Refrigerador** integrado para crear un dispositivo **Bombilla** nuevo:

1. Use el comando siguiente para clonar el repositorio **device-simulation** de GitHub en la máquina local:

    ```cmd/sh
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

1. Cada tipo de dispositivo tiene un archivo de modelo JSON y los scripts asociados en la carpeta `Services/data/devicemodels`. Copie los archivos de **Refrigerador** para crear los archivos de **Bombilla** como se muestra en la tabla siguiente:

    | Origen                      | Destino                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>Definición de las características del tipo de dispositivo nuevo

El archivo `lightbulb-01.json` define las características del tipo, como la telemetría que genera y los métodos que admite. Los pasos siguientes actualizan el archivo `lightbulb-01.json` para definir el dispositivo **Bombilla**:

1. En el archivo `lightbulb-01.json`, actualice los metadatos de dispositivo como se muestra en el fragmento de código siguiente:

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. En el archivo `lightbulb-01.json`, actualice la definición de simulación como se muestra en el fragmento de código siguiente:

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "status": "on"
      },
      "Script": {
        "Type": "javascript",
        "Path": "lightbulb-01-state.js",
        "Interval": "00:00:20"
      }
    },
    ```

1. En el archivo `lightbulb-01.json`, actualice las propiedades del tipo de dispositivo como se muestra en el fragmento de código siguiente:

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. En el archivo `lightbulb-01.json`, actualice las definiciones de telemetría del tipo de dispositivo como se muestra en el fragmento de código siguiente:

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "status": "text"
          }
        }
      }
    ],
    ```

1. En el archivo `lightbulb-01.json`, actualice los métodos del tipo de dispositivo como se muestra en el fragmento de código siguiente:

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      },
    }
    ```

1. Guarde el archivo `lightbulb-01.json`.

### <a name="simulate-custom-device-behavior"></a>Simulación del comportamiento de un dispositivo personalizado

El archivo `scripts/lightbulb-01-state.js` define el comportamiento de la simulación del tipo **Bombilla**. Los pasos siguientes actualizan el archivo `scripts/lightbulb-01-state.js` para definir el comportamiento del dispositivo **Bombilla**:

1. Edite la definición de estado en el archivo `scripts/lightbulb-01-state.js`, tal como se muestra en el fragmento de código siguiente:

    ```js
    // Default state
    var state = {
      online: true,
      status: "on"
    };
    ```

1. Reemplace la función **vary** por la función **flip** siguiente:

    ```js
    /**
    * Simple formula that sometimes flips the status of the lightbulb
    */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }
    ```

1. Edite la función **main** para implementar el comportamiento, tal como se muestra en el fragmento de código siguiente:

    ```js
    function main(context, previousState) {

      // Restore the global state before generating the new telemetry, so that
      // the telemetry can apply changes using the previous function state.
      restoreState(previousState);

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. Guarde el archivo `scripts/lightbulb-01-state.js`.

El archivo `scripts/SwitchOn-method.js` implementa el método **Encender** en un dispositivo **Bombilla**. Los próximos pasos actualizan el archivo `scripts/SwitchOn-method.js`:

1. Edite la definición de estado en el archivo `scripts/SwitchOn-method.js`, tal como se muestra en el fragmento de código siguiente:

    ```js
    var state = {
       status: "on"
    };
    ```

1. Para encender la bombilla, edite la función **main** de la siguiente manera:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. Guarde el archivo `scripts/SwitchOn-method.js`.

1. Haga una copia del archivo `scripts/SwitchOn-method.js` con el nombre `scripts/SwitchOff-method.js`.

1. Para apagar la bombilla, edite la función **main** en el archivo `scripts/SwitchOff-method.js` de la siguiente manera:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. Guarde el archivo `scripts/SwitchOff-method.js`.

### <a name="test-the-lightbulb-device-type"></a>Prueba del tipo de dispositivo Bombilla

Para probar el tipo de dispositivo **Bombilla**, puede probar primero si el tipo de dispositivo se comporta según lo esperado mediante la ejecución de una copia local del servicio **device-simulation**. Cuando haya probado y depurado localmente el tipo de dispositivo nuevo, puede recompilar el contenedor y volver a implementar el servicio **device-simulation** en Azure.

Para probar y depurar localmente los cambios, consulte la [información general sobre la simulación de dispositivos](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md).

Configure el proyecto para copiar los nuevos archivos del dispositivo **Bombilla** en el directorio de salida.

Para probar el dispositivo nuevo en una solución implementada, consulte uno de los siguientes recursos:

* [Implementación de contenedores desde una cuenta personalizada de docker-hub](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account)
* [Actualización de un contenedor implementado mediante una copia manual](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy)

En la página **Dispositivos**, puede aprovisionar instancias del tipo nuevo:

![Vista de la lista de simulaciones disponibles](media/iot-suite-remote-monitoring-test/devicesmodellist.png)

Puede ver la telemetría desde el dispositivo simulado:

![Vista de la telemetría de bombilla](media/iot-suite-remote-monitoring-test/devicestelemetry.png)

Puede llamar a los métodos **SwitchOn** y **SwitchOff** en el dispositivo:

![Llamada a los métodos de bombilla](media/iot-suite-remote-monitoring-test/devicesmethods.png)

Para crear una imagen de Docker con el tipo de dispositivo nuevo para su implementación en Azure, consulte la sección sobre la [creación de una imagen de Docker personalizada](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image).

## <a name="add-a-new-telemetry-type"></a>Incorporación de un tipo de telemetría nuevo

En esta sección se describe cómo modificar un tipo de dispositivo simulado existente para admitir un tipo de telemetría nuevo.

### <a name="locate-the-chiller-device-type-files"></a>Ubicación de los archivos del tipo de dispositivo Refrigerador

En los pasos siguientes se muestra cómo encontrar los archivos que definen el dispositivo **Refrigerador** integrado:

1. Si todavía no lo ha hecho, use el comando siguiente para clonar el repositorio **device-simulation** de GitHub en la máquina local:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. Cada tipo de dispositivo tiene un archivo de modelo JSON y los scripts asociados en la carpeta `data/devicemodels`. Los archivos que define el tipo de dispositivo **Refrigerador** simulado son:
    * `data/devicemodels/chiller-01.json`
    * `data/devicemodels/scripts/chiller-01-state.js`

### <a name="specify-the-new-telemetry-type"></a>Especificación de un tipo de telemetría nuevo

Los pasos siguientes muestran cómo agregar un tipo nuevo de **Temperatura interna** al tipo de dispositivo **Refrigerador**:

1. Abra el archivo `chiller-01.json` .

1. Actualice el valor **SchemaVersion** de la siguiente manera:

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. En la sección **InitialState**, agregue las dos definiciones siguientes:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. En la matriz **Telemetría**, agregue la definición siguiente:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Guarde el archivo `chiller-01.json`.

1. Abra el archivo `scripts/chiller-01-state.js` .

1. Agregue los campos siguientes a la variable **state**:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Agregue la línea siguiente a la función **main**:

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. Guarde el archivo `scripts/chiller-01-state.js`.

### <a name="test-the-chiller-device-type"></a>Prueba del tipo de dispositivo Refrigerador

Para probar el tipo de dispositivo **Refrigerador** actualizado, puede probar primero si el tipo de dispositivo se comporta según lo esperado mediante la ejecución de una copia local del servicio **device-simulation**. Cuando haya probado y depurado localmente el tipo de dispositivo actualizado, puede recompilar el contenedor y volver a implementar el servicio **device-simulation** en Azure.

Cuando ejecuta localmente el servicio **device-simulation**, este envía la telemetría a la solución de supervisión remota. En la página **Dispositivos**, puede aprovisionar instancias del tipo actualizado.

Para probar y depurar localmente los cambios, consulte la sección sobre la [ejecución del servicio con Visual Studio](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#running-the-service-with-visual-studio) o sobre la [compilación y ejecución desde la línea de comandos](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#build-and-run-from-the-command-line).

Para probar el dispositivo nuevo en una solución implementada, consulte uno de los siguientes recursos:

* [Implementación de contenedores desde una cuenta personalizada de docker-hub](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account)
* [Actualización de un contenedor implementado mediante una copia manual](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy)

En la página **Dispositivos**, puede aprovisionar instancias del tipo actualizado:

![Incorporación del refrigerador actualizado](media/iot-suite-remote-monitoring-test/devicesupdatedchiller.png)

Puede ver la telemetría **Temperatura interna** nueva desde el dispositivo simulado.

Para crear una imagen de Docker con el tipo de dispositivo nuevo para su implementación en Azure, consulte la sección sobre la [creación de una imagen de Docker personalizada](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image).

## <a name="next-steps"></a>Pasos siguientes

Este tutorial le ha mostrado cómo:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Crear un tipo de dispositivo nuevo
> * Simular el comportamiento de un dispositivo personalizado
> * Agregar un tipo de dispositivo nuevo al panel
> * Enviar telemetría personalizada desde un tipo de dispositivo existente

Ahora que aprendió a usar el servicio de simulación de dispositivos, el paso siguiente que se sugiere es aprender a [conectar un dispositivo físico a la solución de supervisión remota](iot-suite-connecting-devices-node.md).

Para más información sobre la solución de supervisión remota para los desarrolladores, consulte:

* [Guía de referencia para desarrolladores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guía de solución de problemas para desarrolladores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->