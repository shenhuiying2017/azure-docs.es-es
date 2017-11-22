---
title: "Creación de un dispositivo de puerta de enlace con Azure IoT Edge | Microsoft Docs"
description: "Utilice Azure IoT Edge para crear un dispositivo de puerta de enlace que pueda procesar información para varios dispositivos."
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: e1337ddf5ed84a06a62e2faa198f3e8fb49bc3bd
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="create-an-iot-edge-gateway-device-to-process-data-from-other-iot-devices---preview"></a>Creación de un dispositivo de puerta de enlace de IoT Edge para procesar datos de otros dispositivos de IoT (versión preliminar)

Hay dos maneras de utilizar dispositivos de IoT Edge como puertas de enlace:

* Conectar los dispositivos de nivel inferior que usan el [SDK de dispositivo IoT de Azure][lnk-devicesdk];
* Conectar los dispositivos que no utilizan el protocolo de IoT Hub.

Cuando se usa un dispositivo de IoT Edge como puerta de enlace, se obtienen las siguientes ventajas:

* **Multiplexación de conexiones**. Todos los dispositivos que se conectan a IoT Hub a través de un dispositivo IoT Edge utilizarán la misma conexión subyacente.
* **Suavizado de tráfico**. El dispositivo de IoT Edge implementará automáticamente un retroceso exponencial en caso de limitación de IoT Hub, mientras se conservan los mensajes localmente. Esto hará que la solución sea resistente a los picos de tráfico.
* **Compatibilidad sin conexión limitada**. El dispositivo de puerta de enlace almacenará de manera local los mensajes que no se pueden entregar a IoT Hub.

En este artículo se llamará *puerta de enlace de IoT Edge* a cualquier dispositivo de IoT Edge utilizado como puerta de enlace.

>[!NOTE]
>Actualmente:
> * Los dispositivos de nivel inferior no son capaces de autenticarse con la puerta de enlace si esta se desconecta de IoT Hub.
> * Los dispositivos de IoT Edge no se pueden conectar a las puertas de enlace de IoT Edge.

## <a name="use-the-azure-iot-device-sdk"></a>Uso del SDK de dispositivo IoT de Azure

El centro de Edge que se instala en todos los dispositivos de IoT Edge expone las primitivas siguientes a los dispositivos de nivel inferior:

* mensajes de dispositivo a nube y de nube a dispositivo,
* métodos directos y
* operaciones de dispositivos gemelos.

>[!NOTE]
>Actualmente, los dispositivos de nivel inferior no pueden usar la carga de archivos cuando se conectan a través de una puerta de enlace de IoT Edge.

Cuando se conectan los dispositivos a una puerta de enlace de IoT Edge con el SDK de dispositivo IoT de Azure, debe:

* Configurar el dispositivo de nivel inferior con una cadena de conexión que hace referencia al nombre de host del dispositivo de puerta de enlace.
* Asegurarse de que el dispositivo de nivel inferior confíe en el certificado usado para aceptar la conexión con el dispositivo de puerta de enlace.

Cuando se instala el runtime de Azure IoT Edge mediante el script de control, se crea un certificado para el centro de Edge, como se hizo en el tutorial que explica la instalación de IoT Edge en un dispositivo simulado en [Windows][lnk-tutorial1-win] y [Linux][lnk-tutorial1-lin]. Este certificado lo usa el centro de Edge para aceptar conexiones TLS entrantes y debe ser confiable para el dispositivo de nivel inferior cuando se conecta al dispositivo de puerta de enlace.

Puede crear cualquier infraestructura de certificados que permita la confianza necesaria para la topología de la puerta de enlace de dispositivo. En este artículo, se asume la misma configuración de certificado que le gustaría usar para habilitar la [seguridad de entidad de certificación X.509][lnk-iothub-x509] en IoT Hub, lo que implica un certificado de entidad de certificación X.509 asociado a una instancia de IoT Hub específica (la *entidad de certificación del propietario de la instancia de IoT Hub*) y una serie de certificados, firmados con esta entidad de certificación, instalados en los dispositivos de IoT Edge.

>[!IMPORTANT]
>Actualmente, los dispositivos de IoT Edge y los de nivel inferior pueden usar [tokens SAS][lnk-iothub-tokens] para autenticarse con IoT Hub. Los certificados se usarán únicamente para validar la conexión TLS entre el nodo hoja y el dispositivo de puerta de enlace.

Nuestra configuración utiliza la **entidad de certificación del propietario de la instancia de IoT Hub** como:
* un certificado de firma para la configuración del runtime de IoT Edge en todos los dispositivos de IoT Edge y como
* un certificado de clave pública que se instala en los dispositivos de nivel inferior.

El resultado será una solución que permite a todos los dispositivos usar cualquier dispositivo de IoT Edge como puerta de enlace, siempre y cuando estén conectados al mismo centro de IoT.

### <a name="create-the-certificates-for-test-scenarios"></a>Creación de los certificados para escenarios de prueba

Puede usar los scripts de Powershell y Bash de ejemplo descritos en [ejemplo de administración de un certificado de entidad de certificación][lnk-ca-scripts] para generar una **entidad de certificación del propietario de la instancia de IoT Hub** autofirmado y certificados de dispositivo firmados con él.

1. Siga el paso 1 del [ejemplo de administración de un certificado de entidad de certificación][lnk-ca-scripts] para instalar los scripts.
2. Siga el paso 2 para generar la **entidad de certificación del propietario de la instancia de IoT Hub**; este archivo lo usarán los dispositivos de nivel inferior para validar la conexión.

Utilice las siguientes instrucciones para generar un certificado para el dispositivo de puerta de enlace.

#### <a name="bash"></a>Bash

* Ejecute `./certGen.sh create_edge_device_certificate myGateway` para crear el nuevo certificado de dispositivo.  
  Esto creará el los archivos .\certs\new-edge-device.* que contienen la clave pública y PFX y .\private\new-edge-device.key.pem que contiene la clave privada del dispositivo.  
* `cat new-edge-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-edge-device-full-chain.cert.pem` para obtener la clave pública.
* `./private/new-edge-device.cert.pem` contiene la clave privada del dispositivo.

#### <a name="powershell"></a>PowerShell

* Ejecute `New-CACertsEdgeDevice myGateway` para crear el nuevo certificado de dispositivo.
  Esto creará archivos myEdgeDevice* que contienen la clave pública, la clave privada y el PFX del certificado.  Cuando se le pida una contraseña durante el proceso de firma, escriba "1234".


>[!IMPORTANT]
>Este ejemplo solo está pensado para fines de prueba. Para escenarios de producción, consulte [Protección de su implementación de IoT][lnk-iothub-secure-deployment] para obtener instrucciones de Azure IoT sobre cómo proteger la solución de IoT y aprovisionar el certificado en consecuencia.

### <a name="configure-an-iot-edge-device-as-a-gateway"></a>Configuración de un dispositivo de IoT Edge como puerta de enlace

Para configurar el dispositivo de IoT Edge como puerta de enlace, solo hay que configurar el uso del certificado de dispositivo creado en la sección anterior.

Asumimos los nombres de archivo siguientes de los scripts de ejemplo anteriores:

| Salida | Script de Bash | PowerShell |
| ------ | ----------- | ---------- |
| Certificado de dispositivo | `certs/new-edge-device.cert.pem` | `certs/new-edge-device.cert.pem` |
| Clave privada de dispositivo | `private/new-edge-device.cert.pem` | `private/new-edge-device.cert.pem` |
| Cadena de certificados de dispositivo | `certs/new-edge-device-full-chain.cert.pem` | `certs/new-edge-device-full-chain.cert.pem` |
| Entidad de certificación del propietario de la instancia de IoT Hub | `certs/azure-iot-test-only.root.ca.cert.pem` | `RootCA.pem` |

 De forma análoga a la instalación descrita en el artículo sobre la implementación de Azure IoT Edge en un dispositivo simulado en [Windows][lnk-tutorial1-win] o [Linux][lnk-tutorial1-lin], tendrá que proporcionar la información anterior al entorno en tiempo de ejecución de IoT Edge. 
 
 En Linux:

        sudo iotedgectl setup --connection-string {device connection string}
            --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
            --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
            --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
            --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
            --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem

En Windows:

        iotedgectl setup --connection-string {device connection string}
            --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
            --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
            --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
            --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
            --owner-ca-cert-file {full path}/RootCA.pem

De forma predeterminada los scripts de ejemplo no establecen una frase de contraseña para la clave privada de dispositivo. Si establece una frase de contraseña, agregue el siguiente parámetro:

        --device-ca-passphrase {passphrase}

El script le solicitará que establezca una frase de contraseña para el certificado del agente de Edge.
Tendrá que reiniciar el runtime de Azure IoT Edge después de la ejecución de este comando.

### <a name="configure-an-iot-hub-device-application-as-a-downstream-device"></a>Configuración de una aplicación de dispositivo de IoT Hub como un dispositivo de nivel inferior

Un dispositivo de nivel inferior puede ser cualquier aplicación que usa el [SDK de dispositivo IoT de Azure][lnk-devicesdk], como la aplicación sencilla describa en [Conexión del dispositivo en IoT Hub con .NET][lnk-iothub-getstarted].

En primer lugar, una aplicación de dispositivo de nivel inferior tiene que confiar en la **entidad de certificación del propietario de la instancia de IoT Hub** para validar las conexiones TLS en los dispositivos de puerta de enlace. Normalmente, este paso puede realizarse de dos maneras: en el nivel del sistema operativo o, para determinados idiomas, en el nivel de aplicación.

Por ejemplo, para aplicaciones. NET, puede agregar el siguiente fragmento de código para confiar en un certificado en formato PEM almacenado en la ruta de acceso `certPath`.

        using System.Security.Cryptography.X509Certificates;
        
        ...

        X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
        store.Open(OpenFlags.ReadWrite);
        store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
        store.Close();

Tenga en cuenta que los scripts de ejemplo mencionados anteriormente generan la clave pública en el archivo `certs/azure-iot-test-only.root.ca.cert.pem` (Bash) o `RootCA.pem` (PowerShell).

La realización de este paso en el nivel de sistema operativo es diferente en Windows y por las distribuciones de Linux.

El segundo paso consiste en inicializar el SDK de dispositivo de IoT Hub con una cadena de conexión que hace referencia al nombre de host del dispositivo de puerta de enlace.
Esto se hace anexando la propiedad `GatewayHostName` a la cadena de conexión del dispositivo. Por ejemplo, esto es una cadena de conexión de dispositivo de ejemplo para un dispositivo, al que se anexa la propiedad `GatewayHostName`:

        HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com

Estos dos pasos permitirán que la aplicación de dispositivo se conecte con el dispositivo de puerta de enlace.

## <a name="use-other-protocols"></a>Uso de otros protocolos

Una de las funciones principales de un dispositivo de puerta de enlace consiste en interpretar los protocolos de los dispositivos de nivel inferior. Para conectar los dispositivos que no utilizan el protocolo de IoT Hub, puede desarrollar un módulo de IoT Edge que realiza esta conversión y se conecta en nombre del dispositivo de nivel inferior a la instancia de IoT Hub.

Puede decidir crear una puerta de enlace *transparente* u *opaca*:

| &nbsp; | Puerta de enlace transparente | Puerta de enlace opaca|
|--------|-------------|--------|
| Identidades que se almacenan en el registro de la identidad de IoT Hub | Identidades de todos los dispositivos conectados | Solo la identidad del dispositivo de puerta de enlace |
| Dispositivo gemelo | Cada dispositivo conectado tiene su propio dispositivo gemelo | Solo la puerta de enlace tiene dispositivo y un módulo gemelos |
| Métodos directos y mensajes de nube a dispositivo | La nube puede tratar individualmente cada dispositivo conectado | La nube solo puede tratar el dispositivo de puerta de enlace |
| [Limitaciones y cuotas de IoT Hub][lnk-iothub-throttles-quotas] | Aplicación a cada dispositivo | Aplicación al dispositivo de puerta de enlace |

Cuando se usa un patrón de puerta de enlace opaca, todos los dispositivos que se conectan a través de esa puerta de enlace comparten la misma cola de nube a dispositivo, que puede contener 50 mensajes como máximo. De ello se desprende que el patrón de puerta de enlace opaca debe usarse solo cuando muy pocos dispositivos están conectados a través de cada puerta de enlace de campo y su tráfico de nube a dispositivo sea bajo.

Al implementar una puerta de enlace opaca, el módulo de conversión del protocolo utiliza la cadena de conexión de módulos.

Al implementar una puerta de enlace transparente, el módulo crea varias instancias del cliente de dispositivo de IoT Hub, mediante las cadenas de conexión para los dispositivos de nivel inferior.

## <a name="next-steps"></a>Pasos siguientes

- [Descripción de los requisitos y las herramientas para desarrollar módulos de IoT Edge][lnk-module-dev].

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens 
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/CACertToolEdge/tools/CACertificates/CACertificateOverview.md