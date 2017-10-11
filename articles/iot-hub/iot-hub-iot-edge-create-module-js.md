---
title: "Creación de un módulo de Azure IoT Edge con Node.js | Microsoft Docs"
description: "En este tutorial se muestra cómo escribir un módulo de conversión de datos BLE con el generador Yeoman y los paquetes NPM de Azure IoT Edge más recientes."
services: iot-hub
author: sushi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: article
ms.date: 06/28/2017
ms.author: sushi
ms.openlocfilehash: ba466f47e157d805600c41fa3d84ed5a0363969c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="create-an-azure-iot-edge-module-with-nodejs"></a>Creación de un módulo de Azure IoT Edge con Node.js

En este tutorial se muestra cómo crear un módulo para Azure IoT Edge en JS.

En este tutorial se aborda la configuración de entorno y cómo escribir un módulo de conversión de datos [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) con los paquetes NPM de Azure IoT Edge más recientes.

## <a name="prerequisites"></a>Requisitos previos

En esta sección, configura el entorno para el desarrollo del módulo IoT Edge. Se aplica a sistemas operativos *Windows de 64 bits* y *Linux de 64 bits (Ubuntu 14+)*.

Se requiere el software siguiente:
* [Cliente Git](https://git-scm.com/downloads).
* [Node LTS](https://nodejs.org).
* `npm install -g yo`.
* `npm install -g generator-az-iot-gw-module`

## <a name="architecture"></a>Arquitectura

La plataforma Azure IoT Edge adopta en gran medida la [arquitectura Von Neumann](https://en.wikipedia.org/wiki/Von_Neumann_architecture). Esto significa que toda la arquitectura de Azure IoT Edge es un sistema que procesa entradas y genera salidas, y que cada módulo individual también es un pequeño subsistema de entrada-salida. En este tutorial, se presentan los dos módulos siguientes:

1. Un módulo que recibe una señal [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) simulada y la convierte en un mensaje con formato [JSON](https://en.wikipedia.org/wiki/JSON).
2. Un módulo que imprime el mensaje [JSON](https://en.wikipedia.org/wiki/JSON) recibido.

La imagen siguiente muestra el flujo de datos de extremo a extremo típico para este proyecto:

![Flujo de datos entre tres módulos](media/iot-hub-iot-edge-create-module/dataflow.png "Entrada: módulo BLE simulado; Procesador: Módulo de conversión; Salida: módulo de impresión")

## <a name="set-up-the-environment"></a>Configuración del entorno
A continuación se muestra cómo configura rápidamente el entorno para comenzar a escribir el primer módulo de conversión BLE con JS.

### <a name="create-module-project"></a>Creación del proyecto de módulo
1. Abra una ventana de línea de comandos y ejecute `yo az-iot-gw-module`.
2. Siga los pasos que aparecen en la pantalla para finalizar la inicialización del proyecto de módulo.

### <a name="project-structure"></a>Estructura del proyecto
Un proyecto de módulo JS consta de los componentes siguientes:

`modules`: los archivos de origen del módulo JS personalizados. Reemplace los archivos `sensor.js` y `printer.js` predeterminados por sus propios archivos de módulo.

`app.js`: el archivo de entrada para iniciar la instancia de Edge.

`gw.config.json`: el archivo de configuración para personalizar los módulos para que los cargue Edge.

`package.json`: la información de metadatos para el proyecto de módulo.

`README.md`: la documentación básica para el proyecto de módulo.


### <a name="package-file"></a>Archivo de paquete

Este archivo `package.json` declara toda la información de metadatos necesaria para un proyecto de módulo que incluye el nombre, la versión, la entrada, los scripts, el entorno en tiempo de ejecución y las dependencias de desarrollo.

El fragmento de código siguiente muestra cómo configurar para el proyecto de ejemplo de conversión BLE.
```json
{
  "name": "converter",
  "version": "1.0.0",
  "description": "BLE data converter sample for Azure IoT Edge.",
  "repository": {
    "type": "git",
    "url": "https://github.com/Azure-Samples/iot-edge-samples"
  },
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "author": "Microsoft Corporation",
  "license": "MIT",
  "dependencies": {
  },
  "devDependencies": {
    "azure-iot-gateway": "~1.1.3"
  }
}
```


### <a name="entry-file"></a>Archivo de entrada
El archivo `app.js` define la forma de inicializar la instancia de Edge. Aquí no es necesario hacer ningún cambio.

```javascript
(function() {
  'use strict';

  const Gateway = require('azure-iot-gateway');
  let config_path = './gw.config.json';

  // node app.js
  if (process.argv.length < 2) {
    throw 'Calling pattern should be node app.js.';
  }

  const gw = new Gateway(config_path);
  gw.run();
})();
```

### <a name="interface-of-module"></a>Interfaz de módulo
Puede considerar un módulo de Azure IoT Edge como un procesador de datos cuyo trabajo es recibir una entrada, procesarla y generar una salida.

La entrada pueden ser datos del hardware (como un detector de movimiento), un mensaje de otros módulos o cualquier otro elemento (como un número aleatorio que un temporizador genera periódicamente).

La salida es similar a la entrada, podría desencadenar un comportamiento de hardware (como un LED parpadeante), un mensaje a otros módulos o cualquier otro elemento (como imprimir en la consola).

Los módulos se comunican entre sí mediante el objeto `message`. El **contenido** de un objeto `message` es una matriz de bytes capaz de representar cualquier tipo de datos que desee. Las **propiedades** también están disponibles en el objeto `message` y son simplemente una asignación de cadena a cadena. Puede considerar las **propiedades** como los encabezados de una solicitud HTTP o los metadatos de un archivo.

Para desarrollar un módulo de Azure IoT Edge en JS, debe crear un objeto de módulo nuevo que implemente los métodos requeridos `receive()`. En este momento, también puede elegir implementar los métodos `create()`, `start()` o `destroy()` opcionales. El fragmento de código siguiente muestra el scaffolding del objeto del módulo JS.

```javascript
'use strict';

module.exports = {
  broker: null,
  configuration: null,

  create: function (broker, configuration) {
    // Default implementation.
    this.broker = broker;
    this.configuration = configuration;

    return true;
  },

  start: function () {
    // Produce
  },

  receive: function (message) {
    // Consume
  },

  destroy: function () {
  }
};
```

### <a name="converter-module"></a>Módulo de conversión
| Entrada                    | Procesador                              | Salida                 | Archivo de origen            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| Mensaje de datos de temperatura | Análisis y construcción de un nuevo mensaje JSON | Mensaje JSON de estructura | `converter.js` |

Este módulo es un módulo Azure IoT Edge típico. Acepta mensajes de temperatura de otros módulos (un módulo de hardware o, en este caso, nuestro módulo BLE simulado) y, luego, normaliza el mensaje de temperatura en un mensaje JSON estructurado (incluido adjuntar el identificador de mensaje, establecer la propiedad que indica si es necesario desencadenar la alerta de temperatura, etc.).

```javascript
receive: function (message) {
  // Initialize the messageCount in global object at first time.
  if (!global.messageCount) {
    global.messageCount = 0;
  }

  // Read the content and properties objects from message.
  let rawContent = JSON.parse(Buffer.from(message.content).toString('utf8'));
  let rawProperties = message.properties;

  // Generate new properties object.
  let newProperties = {
    source: rawProperties.source,
    macAddress: rawProperties.macAddress,
    temperatureAlert: rawContent.temperature > 30 ? 'true' : 'false'
  };

  // Generate new content object.
  let newContent = {
    deviceId: 'Intel NUC Gateway',
    messageId: ++global.messageCount,
    temperature: rawContent.temperature
  };

  // Publish the new message to broker.
  this.broker.publish(
    {
      properties: newProperties,
      content: new Uint8Array(Buffer.from(JSON.stringify(newContent), 'utf8'))
    }
  );
},
```

### <a name="printer-module"></a>Módulo de impresión
| Entrada                          | Procesador | Salida                     | Archivo de origen          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| Cualquier mensaje de otros módulos | N/D       | Registro del mensaje en la consola | `printer.js` |

Este módulo es simple y fácil de entender, y genera una salida de los mensajes recibidos (propiedad, contenido) en la ventana de terminal.

```javascript
receive: function (message) {
  let properties = JSON.stringify(message.properties);
  let content = Buffer.from(message.content).toString('utf8');

  console.log(`printer.receive.properties - ${properties}`);
  console.log(`printer.receive.content - ${content}\n`);
}
```

### <a name="configuration"></a>Configuración
El paso final antes de ejecutar los módulos es configurar Azure IoT Edge y establecer las conexiones entre los módulos.

Primero es necesario declarar el cargador `node` (debido a que Azure IoT Edge admite cargadores de distintos lenguajes) al que se podría hacer referencia mediante su `name` en las secciones más adelante.

```json
"loaders": [
  {
    "type": "node",
    "name": "node"
  }
]
```

Una vez que declaremos los cargadores, también es necesario declarar los módulos. De manera similar a declarar los cargadores, también su atributo `name` podría hacer referencia a ellos. Cuando se declara un módulo, es necesario especificar el cargador que debe usar (que debe ser el que se definió anteriormente) y el punto de entrada (que debe ser el nombre de clase normalizado de nuestro módulo) de cada módulo. El módulo `simulated_device` es un módulo nativo que se incluye en el paquete en tiempo de ejecución principal de Azure IoT Edge. Incluya `args` en el archivo JSON incluso si es `null`.

```json
"modules": [
  {
    "name": "simulated_device",
    "loader": {
      "name": "native",
      "entrypoint": {
        "module.path": "simulated_device"
      }
    },
    "args": {
      "macAddress": "01:02:03:03:02:01",
      "messagePeriod": 500
    }
  },
  {
    "name": "converter",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/converter.js"
      }
    },
    "args": null
  },
  {
    "name": "printer",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/printer.js"
      }
    },
    "args": null
  }
]
```

Establecemos las conexiones al final de la configuración. Cada conexión se expresa mediante `source` y `sink`. Ambos elementos deben hacer referencia a un módulo predefinido. El mensaje de salida del módulo `source` se desvía a la entrada del módulo `sink`.

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "printer"
  }
]
```

## <a name="running-the-modules"></a>Ejecución de los módulos
1. `npm install`
2. `npm start`

Si desea finalizar la aplicación, presione la tecla `<Enter>`.

> [!IMPORTANT]
> No se recomienda usar Ctrl + C para finalizar la aplicación IoT Edge. Hacerlo podría provocar que el proceso se finaliza de forma anómala.
