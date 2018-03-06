---
title: "Uso de OPC Publisher de factoría conectada del Conjunto de aplicaciones de IoT de Azure | Microsoft Docs"
description: "Compilación e implementación de la referencia de OPC Publisher de factoría conectada."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 934f4deb3139c136c871ab0125ba45267e1d1b05
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2017
---
# <a name="opc-publisher-for-azure-iot-edge"></a>OPC Publisher para Azure IoT Edge

En este artículo se explica el uso de la implementación de referencia de OPC Publisher. La implementación de referencia muestra cómo se puede usar Azure IoT Edge para:

- Conectarse a los servidores de OPC UA existentes.
- Publicar datos de telemetría JSON codificados desde estos servidores de OPC UA con formato *Pub/Sub*, mediante una carga JSON, en Azure IoT Hub. Puede usar cualquiera de los protocolos de transporte que admite Azure IoT Edge.

Esta aplicación de referencia incluye:

- Un *cliente* de OPC UA para conectarse a los servidores de OPC UA existentes en la red.
- Un *servidor* OPC UA que escuche en el puerto 62222 que puede usar para administrar lo que se publica.

La aplicación se implementa mediante .NET Core y se puede ejecutar en las plataformas compatibles con .NET Core.

El editor implementa la lógica de reintento al establecer conexiones a puntos de conexión. El editor espera que los puntos de conexión respondan en un número especificado de solicitudes de conexión persistente. Esta lógica de reintento permite al editor detectar condiciones como, por ejemplo, un corte del suministro eléctrico de un servidor de OPC UA.

Para cada intervalo de publicación diferente de un servidor de OPC UA, este crea una suscripción independiente en la que se actualizan todos los nodos con este intervalo de publicación.

Para reducir la carga de red, el editor admite el procesamiento por lotes de los datos que se envían a IoT Hub. Un lote se envía a IoT Hub solo cuando se alcanza el tamaño de paquete configurado.

Esta aplicación usa la pila de referencia de OPC UA de OPC Foundations y, por tanto, se aplican restricciones de licencia. Visite http://opcfoundation.github.io/UA-.NETStandardLibrary/ para consultar la documentación de OPC UA y los términos de las licencias.

Puede encontrar el código fuente de OPC Publisher en el repositorio de GitHub [OPC Publisher para Azure IoT Edge](https://github.com/Azure/iot-edge-opc-publisher).

## <a name="prerequisites"></a>Requisitos previos

Para compilar la aplicación, necesita el [SDK 1.1 de .NET Core](https://docs.microsoft.com/dotnet/core/sdk) para su sistema operativo.

## <a name="build-the-application"></a>Compilar la aplicación

### <a name="as-native-windows-application"></a>Como aplicación nativa de Windows

Abra el proyecto `OpcPublisher.sln` con Visual Studio 2017 y compile la solución presionando F7.

### <a name="as-docker-container"></a>Como contenedor de Docker

Para compilar la aplicación como un contenedor de Docker de Windows, use el archivo de configuración `Dockerfile.Windows`.

Para compilar la aplicación como un contenedor de Docker de Linux, use el archivo de configuración `Dockerfile`.

En la raíz del repositorio de la máquina de desarrollo, escriba el siguiente comando en una ventana de consola:

`docker build -f <docker-configfile-to-use> -t <your-container-name> .`

La opción `-f` de `docker build` es opcional y el valor predeterminado consiste en utilizar el archivo de configuración `Dockerfile`.

Docker también permite compilar directamente desde un repositorio de Git. Puede compilar un contenedor de Docker de Linux con el siguiente comando:

`docker build -t <your-container-name> .https://github.com/Azure/iot-edge-opc-publisher`

## <a name="configure-the-opc-ua-nodes-to-publish"></a>Configuración de los nodos de OPC UA para su publicación

Para configurar qué nodos de OPC UA deben tener sus valores publicados en Azure IoT Hub, cree un archivo de configuración con formato JSON. El nombre predeterminado para este archivo de configuración es `publishednodes.json`. La aplicación actualiza y guarda el archivo de configuración cuando este usa los métodos del servidor de OPC UA **PublishNode** o **UnpublishNode**.

La sintaxis del archivo de configuración es la que se indica a continuación:

```json
[
    {
        // example for an EnpointUrl is: opc.tcp://win10iot:51210/UA/SampleServer
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "OpcNodes": [
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised publishing interval).
            {
                // The identifier specifies the NamespaceUri and the node identifier in XML notation as specified in Part 6 of the OPC UA specification in the XML Mapping section.
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258"
            },
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with an OPC publishing interval of 4 seconds.
            // Publisher will use for each dinstinct publishing interval (of nodes on the same EndpointUrl) a separate subscription. All nodes without a publishing interval setting,
            // will be on the same subscription and the OPC UA stack will publish with the lowest sampling interval of a node.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                "OpcPublishingInterval": 4000
            },
            // Publisher will request the server at EndpointUrl to sample the node with the given sampling interval of 1 second
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised interval).
            // If the OPC publishing interval is set to a lower value, Publisher will adjust the OPC publishing interval of the subscription to the OPC sampling interval value.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                // the OPC sampling interval to use for this node.
                "OpcSamplingInterval": 1000
            }
        ]
    },

    // the format below is only supported for backward compatibility. you need to ensure that the
    // OPC UA server on the configured EndpointUrl has the namespaceindex you expect with your configuration.
    // please use the ExpandedNodeId syntax instead.
    {
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "NodeId": {
            "Identifier": "ns=0;i=2258"
        }
    }
    // please consult the OPC UA specification for details on how OPC monitored node sampling interval and OPC subscription publishing interval settings are handled by the OPC UA stack.
    // the publishing interval of the data to Azure IoT Hub is controlled by the command line settings (or the default: publish data to IoT Hub at least each 1 second).
]
```

## <a name="run-the-application"></a>Ejecución de la aplicación

### <a name="command-line-options"></a>Opciones de línea de comandos

Para ver el uso completo de la aplicación, use la opción de la línea de comandos `--help`. En el ejemplo siguiente se muestra la estructura de un comando:

```cmd/sh
OpcPublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]
```

`applicationname` es el nombre de la aplicación de OPC UA que se va a usar. Este parámetro es obligatorio. El nombre de la aplicación también se usa para registrar el editor en el registro de dispositivos de IoT Hub.

`IoT Hubconnectionstring` es la cadena de conexión del propietario de IoT Hub. Este parámetro es opcional.

Se admiten las siguientes opciones:

```cmd/sh
--pf, --publishfile=VALUE
  the filename to configure the nodes to publish.
    Default: './publishednodes.json'
--sd, --shopfloordomain=VALUE
  the domain of the shopfloor. if specified this
    domain is appended (delimited by a ':' to the '
    ApplicationURI' property when telemetry is
    sent to IoT Hub.
    The value must follow the syntactical rules of a
    DNS hostname.
    Default: not set
--sw, --sessionconnectwait=VALUE
  specify the wait time in seconds publisher is
    trying to connect to disconnected endpoints and
    starts monitoring unmonitored items
    Min: 10
    Default: 10
--vc, --verboseconsole=VALUE
  the output of publisher is shown on the console.
    Default: False
--ih, --IoT Hubprotocol=VALUE
  the protocol to use for communication with Azure
    IoT Hub (allowed values: Amqp, Http1, Amqp_
    WebSocket_Only, Amqp_Tcp_Only, Mqtt, Mqtt_
    WebSocket_Only, Mqtt_Tcp_Only).
    Default: Mqtt
--ms, --IoT Hubmessagesize=VALUE
  the max size of a message which can be send to
    IoT Hub. when telemetry of this size is available
    it will be sent.
    0 will enforce immediate send when telemetry is
    available
    Min: 0
    Max: 256 * 1024
    Default: 4096
--si, --IoT Hubsendinterval=VALUE
  the interval in seconds when telemetry should be
    send to IoT Hub. If 0, then only the
    IoT Hubmessagesize parameter controls when
    telemetry is sent.
    Default: '1'
--lf, --logfile=VALUE
  the filename of the logfile to use.
    Default: './Logs/<applicationname>.log.txt'
--pn, --portnum=VALUE
  the server port of the publisher OPC server
    endpoint.
    Default: 62222
--pa, --path=VALUE
  the endpoint URL path part of the publisher OPC
    server endpoint.
    Default: '/UA/Publisher'
--lr, --ldsreginterval=VALUE
  the LDS(-ME) registration interval in ms. If 0,
    then the registration is disabled.
    Default: 0
--ot, --operationtimeout=VALUE
  the operation timeout of the publisher OPC UA
    client in ms.
    Default: 120000
--oi, --opcsamplinginterval=VALUE
  the publisher is using this as default value in
    milliseconds to request the servers to sample
    the nodes with this interval
    this value might be revised by the OPC UA
    servers to a supported sampling interval.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a negative value will set the sampling interval
    to the publishing interval of the subscription
    this node is on.
    0 will configure the OPC UA server to sample in
    the highest possible resolution and should be
    taken with care.
    Default: 1000
--op, --opcpublishinginterval=VALUE
  the publisher is using this as default value in
    milliseconds for the publishing interval setting
    of the subscriptions established to the OPC UA
    servers.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a value less than or equal zero will let the
    server revise the publishing interval.
    Default: 0
--ct, --createsessiontimeout=VALUE
  specify the timeout in seconds used when creating
    a session to an endpoint. On unsuccessful
    connection attemps a backoff up to 5 times the
    specified timeout value is used.
    Min: 1
    Default: 10
--ki, --keepaliveinterval=VALUE
  specify the interval in seconds the publisher is
    sending keep alive messages to the OPC servers
    on the endpoints it is connected to.
    Min: 2
    Default: 2
--kt, --keepalivethreshold=VALUE
  specify the number of keep alive packets a server
    can miss, before the session is disconneced
    Min: 1
    Default: 5
--st, --opcstacktracemask=VALUE
  the trace mask for the OPC stack. See github OPC .
    NET stack for definitions.
    To enable IoT Hub telemetry tracing set it to 711.
    Default: 285  (645)
--as, --autotrustservercerts=VALUE
  the publisher trusts all servers it is
    establishing a connection to.
    Default: False
--tm, --trustmyself=VALUE
  the publisher certificate is put into the trusted
    certificate store automatically.
    Default: True
--fd, --fetchdisplayname=VALUE
  enable to read the display name of a published
    node from the server. this will increase the
    runtime.
    Default: False
--at, --appcertstoretype=VALUE
  the own application cert store type.
    (allowed values: Directory, X509Store)
    Default: 'X509Store'
--ap, --appcertstorepath=VALUE
  the path where the own application cert should be
    stored
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/own'
--tt, --trustedcertstoretype=VALUE
  the trusted cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--tp, --trustedcertstorepath=VALUE
  the path of the trusted cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/trusted'
--rt, --rejectedcertstoretype=VALUE
  the rejected cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--rp, --rejectedcertstorepath=VALUE
  the path of the rejected cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/rejected'
--it, --issuercertstoretype=VALUE
  the trusted issuer cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--ip, --issuercertstorepath=VALUE
  the path of the trusted issuer cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/issuers'
--dt, --devicecertstoretype=VALUE
  the IoT Hub device cert store type.
    (allowed values: Directory, X509Store)
    Default: X509Store
--dp, --devicecertstorepath=VALUE
  the path of the iot device cert store
    Default Default (depends on store type):
    X509Store: 'My'
    Directory: 'CertificateStores/IoT Hub'
-h, --help
  show this message and exit
```

Puede usar las siguientes variables de entorno para controlar la aplicación:
- `_HUB_CS`: establece la cadena de conexión del propietario de IoT Hub
- `_GW_LOGP`: establece el nombre de archivo del archivo de registro que se va a usar
- `_TPC_SP`: establece la ruta de acceso para almacenar los certificados de las estaciones de confianza
- `_GW_PNFP`: establece el nombre de archivo del archivo de configuración de la publicación

Los argumentos de la línea de comandos prevalecen sobre los valores de las variables de entorno.

Normalmente, la cadena de conexión del propietario de IoT Hub solo se especifica durante el primer inicio de la aplicación. La cadena de conexión se cifra y se almacena en el almacén de certificados de la plataforma.

En las llamadas posteriores, la cadena de conexión se lee y se vuelve a usar desde el almacén de certificados de la plataforma. Si especifica la cadena de conexión en cada inicio, el dispositivo se eliminará y se volverá a crear en el registro de dispositivos de IoT Hub cada vez.

### <a name="native-on-windows"></a>Nativa en Windows

Para ejecutar la aplicación de forma nativa en Windows, abra el proyecto `OpcPublisher.sln` con Visual Studio 2017, compile la solución y publíquela. Puede iniciar la aplicación en el **directorio de destino** que ha publicado con:

```cmd
dotnet OpcPublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-self-built-container"></a>Uso de un contenedor compilado automáticamente

Para ejecutar la aplicación en un contenedor generado automáticamente, compile y, a continuación, inicie su propio contenedor:

```cmd/sh
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-container-from-hubdockercom"></a>Uso de un contenedor de hub.docker.com

En DockerHub hay disponible un contenedor compilado previamente. Para iniciarlo, ejecute el siguiente comando:

```cmd/sh
docker run microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="important-when-using-a-container"></a>Aviso importante cuando se utiliza un contenedor

#### <a name="access-to-the-publisher-opc-ua-server"></a>Acceso al servidor de Publisher OPC UA

El servidor de Publisher OPC UA escucha de forma predeterminada en el puerto 62222. Para exponer este puerto de entrada en un contenedor, debe usar la opción `-p` de `docker run`:

```cmd/sh
docker run -p 62222:62222 microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="enable-intercontainer-name-resolution"></a>Habilitar la resolución de nombres entre contenedores

Para habilitar la resolución de nombres desde dentro del contenedor a otros contenedores, debe:

- Crear un puente de red de Docker definido por el usuario.
- Conectar el contenedor a la red mediante la opción `--network`.
- Asignar un nombre al contenedor mediante la opción `--name`.

En el ejemplo siguiente se muestran estas opciones de configuración:

```cmd/sh
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Otros contenedores pueden ya acceder al contenedor a través de la red mediante el nombre `publisher`.

#### <a name="assign-a-hostname"></a>Asignación de un nombre de host

Publisher usará el nombre de host de la máquina en la que se está ejecutando para la generación del certificado y el punto de conexión. Docker elegirá un nombre de host aleatorio a menos que establezca uno con la opción `-h`. Aquí se muestra un ejemplo de cómo establecer el nombre de host interno del contenedor en `publisher`:

```cmd/sh
docker run -h publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="using-bind-mounts-shared-filesystem"></a>Mediante montajes de enlaces (sistema de archivos compartido)

En algunos escenarios, puede que desee leer la información de configuración desde ubicaciones del host, o escribir archivos de registro en estas ubicaciones, en lugar de usar el sistema de archivos del contenedor. Para configurar este comportamiento, use la opción `-v` de `docker run` en el modo de montaje de enlaces. Por ejemplo:

```cmd/sh
-v //D/docker:/build/out/Logs
-v //D/docker:/build/out/CertificateStores
-v //D/docker:/shared
-v //D/docker:/root/.dotnet/corefx/cryptography/x509stores
```

#### <a name="store-for-x509-certificates"></a>Almacenamiento de certificados X509

El almacenamiento de los certificados X509 no funciona con montajes de enlace, porque los permisos de la ruta de acceso al almacén deben ser `rw` para el propietario. En su lugar, debe usar la opción `-v` de `docker run` en el modo de volumen.

## <a name="debug-the-application"></a>Depuración de la aplicación

### <a name="native-on-windows"></a>Nativa en Windows

Abra el proyecto `OpcPublisher.sln` con Visual Studio 2017 y empiece a depurar la aplicación presionando F5.

### <a name="in-a-docker-container"></a>En un contenedor de Docker

Visual Studio 2017 admite la depuración de aplicaciones en un contenedor de Docker mediante `docker-compose`. Sin embargo, este método no permite pasar parámetros de línea de comandos.

Una opción de depuración alternativa que admite Visual Studio 2017 consiste en depurar con `ssh`. Puede usar el archivo de configuración de compilación de Docker `Dockerfile.ssh` en la raíz del repositorio para crear un contenedor habilitado para SSH:

```cmd/sh
docker build -f .\Dockerfile.ssh -t publisherssh .
```

Ahora ya puede iniciar el contenedor para depurar el editor:

```cmd/sh
docker run -it publisherssh
```

En el contenedor, debe iniciar el demonio **ssh** manualmente:

```cmd/sh
service ssh start
```

En este punto, puede crear una sesión **ssh** como el usuario `root` con la contraseña `Passw0rd`.

Para prepararse para depurar la aplicación en el contenedor, complete los siguientes pasos adicionales:

1. En el lado del host, cree un archivo `launch.json`:

    ```json
    {
      "version": "0.2.0",
      "adapter": "<path>\\plink.exe",
      "adapterArgs": "root@localhost -pw Passw0rd -batch -T ~/vsdbg/vsdbg --interpreter=vscode",
      "languageMappings": {
        "C#": {
          "languageId": "3F5162F8-07C6-11D3-9053-00C04FA302A1",
          "extensions": [ "*" ]
        }
      },
      "exceptionCategoryMappings": {
        "CLR": "449EC4CC-30D2-4032-9256-EE18EB41B62B",
        "MDA": "6ECE07A9-0EDE-45C4-8296-818D8FC401D4"
      },
      "configurations": [
        {
          "name": ".NET Core Launch",
          "type": "coreclr",
          "cwd": "~/publisher",
          "program": "Opc.Ua.Publisher.dll",
          "args": "<put-the-publisher-command-line-options-here>",

          "request": "launch"
        }
      ]
    }
    ```

1. Compile el proyecto y publíquelo en un directorio de su elección.

1. Use una herramienta como `WinSCP` para copiar los archivos publicados en el directorio `/root/publisher` del contenedor. Si decide usar un directorio diferente, actualice la propiedad `cdw` en el archivo `launch.json`.

Ahora ya puede iniciar la depuración mediante el comando siguiente en la ventana de comandos de Visual Studio:

```cmd
DebugAdapterHost.Launch /LaunchJson:"<path-to-the-launch.json-file-you-saved>"
```

## <a name="next-steps"></a>Pasos siguientes

Se recomienda el siguiente paso para aprender a [implementar una puerta de enlace en Windows o Linux para la solución preconfigurada de factoría conectada](iot-suite-connected-factory-gateway-deployment.md).