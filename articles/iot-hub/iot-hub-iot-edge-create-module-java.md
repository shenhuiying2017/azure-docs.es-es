---
title: "Creación de un módulo de Azure IoT Edge con Java | Microsoft Docs"
description: "En este tutorial se muestra cómo escribir un módulo de conversión de datos BLE con los paquetes Maven de Azure IoT Edge más recientes."
services: iot-hub
author: junyi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 06/28/2017
ms.author: junyi
ms.openlocfilehash: 0c430272225d79737baec2be15ed7c93991cdeac
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="create-an-azure-iot-edge-module-with-java"></a>Creación de un módulo de Azure IoT Edge con Java

En este tutorial se muestra cómo compilar un módulo para Azure IoT Edge en Java.

En este tutorial se abordará la configuración de entorno y cómo escribir un módulo de conversión de datos [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) con los paquetes Maven de Azure IoT Edge más recientes.

## <a name="prerequisites"></a>Requisitos previos

En esta sección, configurará el entorno para el desarrollo del módulo IoT Edge. Se aplica a sistemas operativos *Windows de 64 bits* y *Linux de 64 bits (Ubuntu/Debian 8)*.

Se requiere el software siguiente:

* [Cliente Git](https://git-scm.com/downloads).
* [**x64** JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* [Maven](https://maven.apache.org/install.html).

Abra una ventana de terminal de línea de comandos y clone el repositorio siguiente:

1. `git clone https://github.com/Azure-Samples/iot-edge-samples.git`.
2. `cd iot-edge-samples/java/simulated_ble`

## <a name="overall-architecture"></a>Arquitectura global

La plataforma Azure IoT Edge adopta en gran medida la [arquitectura Von Neumann](https://en.wikipedia.org/wiki/Von_Neumann_architecture). Esto significa que toda la arquitectura de Azure IoT Edge es un sistema que procesa entradas y genera salidas, y que cada módulo individual también es un pequeño subsistema de entrada-salida. En este tutorial, se presentarán los dos módulos siguientes:

1. Un módulo que recibe una señal [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) simulada y la convierte en un mensaje con formato [JSON](https://en.wikipedia.org/wiki/JSON).
2. Un módulo que imprime el mensaje [JSON](https://en.wikipedia.org/wiki/JSON) recibido.

La imagen siguiente muestra el flujo de datos de un extremo a otro típico para este proyecto:

![Flujo de datos entre tres módulos](media/iot-hub-iot-edge-create-module/dataflow.png "Entrada: módulo BLE simulado; Procesador: Módulo de conversión; Salida: módulo de impresión")

## <a name="understanding-the-code"></a>Descripción del código

### <a name="maven-project-structure"></a>Estructura del proyecto Maven

Como los paquetes de Azure IoT Edge se basan en Maven, es necesario crear una estructura de proyecto Maven típica, que contiene un archivo `pom.xml`.

El archivo POM se hereda del paquete `com.microsoft.azure.gateway.gateway-module-base`, que declara todas las dependencias que un proyecto de módulo necesita, que incluye los archivos binarios en tiempo de ejecución, la ruta de acceso al archivo de configuración de puerta de enlace y el comportamiento de ejecución. Esto permite ahorrar mucho tiempo y elimina la necesidad de escribir y reescribir cientos de líneas de código una y otra vez.

Es necesario actualizar el archivo pom.xml mediante la declaración de las dependencias/complementos requeridos y el nombre del archivo de configuración que el módulo usará, como se muestra en el siguiente fragmento de código.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!-- Inherit from parent -->
  <parent>
    <groupId>com.microsoft.azure.gateway</groupId>
    <artifactId>gateway-module-base</artifactId>
    <version>1.0.1</version>
  </parent>
  
  <groupId>com.microsoft.azure.gateway</groupId>
  <artifactId>ble-converter</artifactId>
  <version>1.0</version>
  <packaging>jar</packaging>

  <!-- Set the filename of the Azure IoT Edge configuration located
       under ./src/main/resources/gateway/ which is used in parent -->
  <properties>
    <gw.config.fileName>gw-config.json</gw.config.fileName>
  </properties>

  <!-- Re-declare dependencies used in parent -->
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.gateway</groupId>
      <artifactId>gateway-java-binding</artifactId>
    </dependency>
    <dependency>
      <groupId>${dependency.runtime.group}</groupId>
      <artifactId>${dependency.runtime.name}</artifactId>
    </dependency>
  </dependencies>

  <!-- Re-declare plugins used in parent -->
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-dependency-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-resources-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

### <a name="basic-understanding-of-an-azure-iot-edge-module"></a>Descripción básica de un módulo de Azure IoT Edge

Puede considerar un módulo de Azure IoT Edge como un procesador de datos cuyo trabajo es recibir una entrada, procesarla y generar una salida.

La entrada pueden ser datos del hardware (como un detector de movimiento), un mensaje de otros módulos o cualquier otro elemento (como un número aleatorio que un temporizador genera periódicamente).

La salida es similar a la entrada, podría desencadenar un comportamiento de hardware (como un LED parpadeante), un mensaje a otros módulos o cualquier otro elemento (como imprimir en la consola).

Los módulos se comunican entre sí mediante la clase `com.microsoft.azure.gateway.messaging.Message`. El **contenido** de un objeto `Message` es una matriz de bytes capaz de representar cualquier tipo de datos que desee. Las **propiedades** también están disponibles en el objeto `Message` y son simplemente una asignación de cadena a cadena. Puede considerar las **propiedades** como los encabezados de una solicitud HTTP o los metadatos de un archivo.

Para desarrollar un módulo de Azure IoT Edge en Java, debe crear una nueva clase de módulo que se hereda de `com.microsoft.azure.gateway.core.GatewayModule` e implementar los métodos abstractos `receive()` y `destroy()` requeridos. En este momento, también puede elegir implementar los métodos `start()` o `create()` opcionales. El fragmento de código siguiente muestra cómo comenzar a crear un módulo de Azure IoT Edge.

```java
import com.microsoft.azure.gateway.core.Broker;
import com.microsoft.azure.gateway.core.GatewayModule;
import com.microsoft.azure.gateway.messaging.Message;

public class MyEdgeModule extends GatewayModule {
  public MyEdgeModule(long address, Broker broker, String configuration) {
    /* Let the GatewayModule do the dirty work of initialization. It's also
       a good time to parse your own configuration defined in Azure IoT Edge
       configuration file (typically ./src/main/resources/gateway/gw-config.json) */
    super(address, broker, configuration);
  }

  @Override
  public void start() {
    /* Acquire the resources you need. If you don't
       need any resources, you may omit this method. */
  }

  @Override
  public void destroy() {
    /* It's time to release all resources. This method is required. */
  }

  @Override
  public void receive(Message message) {
    /* Logic to process the input message. This method is required. */
    // ...
    /* Use publish() method to do the output. You are
       allowed to publish your new Message instance. */
    this.publish(message);
  }
}
```

### <a name="converter-module"></a>Módulo de conversión

| Entrada                    | Procesador                              | Salida                 | Archivo de origen            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| Mensaje de datos de temperatura | Análisis y construcción de un nuevo mensaje JSON | Mensaje JSON de estructura | `ConverterModule.java` |

Este módulo es un módulo Azure IoT Edge típico. Acepta mensajes de temperatura de otros módulos (un módulo de hardware o, en este caso, nuestro módulo BLE simulado) y, luego, normaliza el mensaje de temperatura en un mensaje JSON estructurado (incluido adjuntar el identificador de mensaje, establecer la propiedad que indica si es necesario desencadenar la alerta de temperatura, etc.).

```java
@Override
public void receive(Message message) {
  try {
    JSONObject messageFromBle = new JSONObject(new String(message.getContent()));
    double temperature = messageFromBle.getDouble("temperature");
    Map<String, String> inputProperties = message.getProperties();

    HashMap<String, String> properties = new HashMap<>();
    properties.put("source", inputProperties.get("source"));
    properties.put("macAddress", inputProperties.get("macAddress"));
    properties.put("temperatureAlert", temperature > 30 ? "true" : "false");

    String content = String.format(
        "{ \"deviceId\": \"Intel NUC Gateway\", \"messageId\": %d, \"temperature\": %f }",
        ++this.messageCount, temperature);

    this.publish(new Message(content.getBytes(), properties));
  } catch (Exception ex) {
    ex.printStackTrace();
  }
}
```

### <a name="printer-module"></a>Módulo de impresión

| Entrada                          | Procesador | Salida                     | Archivo de origen          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| Cualquier mensaje de otros módulos | N/D       | Registro del mensaje en la consola | `PrinterModule.java` |

Este módulo es simple y fácil de entender, y genera una salida de los mensajes recibidos en la ventana de terminal.

```java
@Override
public void receive(Message message) {
  System.out.println(message.toString());
}
```

### <a name="azure-iot-edge-configuration"></a>Configuración de Azure IoT Edge

El paso final antes de ejecutar los módulos es configurar Azure IoT Edge y establecer las conexiones entre los módulos.

Primero es necesario declarar el cargador de Java (debido a que Azure IoT Edge admite cargadores de distintos lenguajes) al que se podría hacer referencia mediante su `name` en las secciones más adelante.

```json
"loaders": [{
  "type": "java",
  "name": "java",
  "configuration": {
    "jvm.options": {
      "library.path": "./"
    }
  }
}]
```

Una vez que declaremos los cargadores, también será necesario declarar los módulos. De manera similar a declarar los cargadores, también su atributo `name` podría hacer referencia a ellos. Cuando se declara un módulo, es necesario especificar el cargador que debe usar (que debe ser el que se definió anteriormente) y el punto de entrada (que debe ser el nombre de clase normalizado de nuestro módulo) de cada módulo. El módulo `simulated_device` es un módulo nativo que se incluye en el paquete en tiempo de ejecución principal de Azure IoT Edge. Siempre debe incluir `args` en el archivo JSON incluso si es `null`.

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
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/ConverterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  },
  {
    "name": "print",
    "loader": {
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/PrinterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  }
]
```

Establecemos las conexiones al final de la configuración. Cada conexión se expresa mediante `source` y `sink`. Ambos elementos deben hacer referencia a un módulo predefinido. El mensaje de salida del módulo `source` se desviará a la entrada del módulo `sink`.

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "print"
  }
]
```

## <a name="running-the-modules"></a>Ejecución de los módulos

Use `mvn package` para compilar todo en la carpeta `target/`. También se recomienda `mvn clean package` para realizar una compilación limpia.

Use `mvn exec:exec` para ejecutar Azure IoT Edge y debería ver que los datos de temperatura y todas las propiedades se imprimen en la consola a una tarifa fija.

Si desea finalizar la aplicación, presione la tecla `<Enter>`.

> [!IMPORTANT]
> No se recomienda usar Ctrl + C para finalizar la aplicación de puerta de enlace de IoT Edge. Esto podría provocar que el proceso se finalice de forma anómala.

