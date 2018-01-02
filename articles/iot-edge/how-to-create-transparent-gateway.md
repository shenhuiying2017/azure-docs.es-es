---
title: "Creación de un dispositivo de puerta de enlace transparente con Azure IoT Edge | Microsoft Docs"
description: "Uso de Azure IoT Edge para crear un dispositivo de puerta de enlace transparente que pueda procesar información para varios dispositivos."
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/04/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 25f4cea1908a0f9bdf387ddfed5f29e6d19bdd20
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="create-an-iot-edge-device-that-acts-as-a-transparent-gateway---preview"></a>Creación de un dispositivo de IoT Edge que actúa como una puerta de enlace transparente - versión preliminar

En este artículo se proporcionan instrucciones detalladas para usar un dispositivo de IoT Edge como una puerta de enlace transparente. Durante el resto de este artículo, el término *puerta de enlace de IoT Edge* hace referencia a un dispositivo de IoT Edge que se usa como una puerta de enlace transparente. Para información más detallada, consulte [Uso de un dispositivo IoT Edge como puerta de enlace][lnk-edge-as-gateway], que brinda información general conceptual. 

>[!NOTE]
>Actualmente:
> * Si la puerta de enlace se desconecta desde IoT Hub, los dispositivos de nivel inferior no se pueden autenticar con la puerta de enlace.
> * Los dispositivos de IoT Edge no se pueden conectar a las puertas de enlace de IoT Edge.

## <a name="understand-the-azure-iot-device-sdk"></a>Descripción del SDK de dispositivo IoT de Azure


El centro de Edge que se instala en todos los dispositivos de IoT Edge expone las primitivas siguientes a los dispositivos de nivel inferior:

* mensajes de dispositivo a nube y de nube a dispositivo
* métodos directos
* operaciones de dispositivos gemelos

Actualmente, los dispositivos de nivel inferior no pueden usar la carga de archivos cuando se conectan a través de una puerta de enlace de IoT Edge.

Cuando se conectan los dispositivos a una puerta de enlace de IoT Edge con el SDK de dispositivo IoT de Azure, debe hacer lo siguiente:

* Configurar el dispositivo de nivel inferior con una cadena de conexión que hace referencia al nombre de host del dispositivo de puerta de enlace.
* Asegurarse de que el dispositivo de nivel inferior confíe en el certificado usado para aceptar la conexión con el dispositivo de puerta de enlace.

Cuando se instala el runtime de Azure IoT Edge mediante el script de control, se crea un certificado para el centro de Edge, como se hizo en el tutorial que explica la [instalación de IoT Edge en un dispositivo simulado en Windows][lnk-tutorial1-win] y [Linux][lnk-tutorial1-lin]. Este certificado lo usa el centro de Edge para aceptar conexiones TLS entrantes y debe ser confiable para el dispositivo de nivel inferior cuando se conecta al dispositivo de puerta de enlace.

Puede crear cualquier infraestructura de certificados que permita la confianza necesaria para la topología de la puerta de enlace de dispositivo. En este artículo, se asume la misma configuración de certificado que le gustaría usar para habilitar la [seguridad de entidad de certificación X.509][lnk-iothub-x509] en IoT Hub, lo que implica un certificado de entidad de certificación X.509 asociado a una instancia de IoT Hub específica (la *entidad de certificación del propietario de la instancia de IoT Hub*) y una serie de certificados, firmados con esta entidad de certificación, instalados en los dispositivos de IoT Edge.

>[!IMPORTANT]
>Actualmente, los dispositivos de IoT Edge y los de nivel inferior pueden usar [tokens SAS][lnk-iothub-tokens] para autenticarse con IoT Hub. Los certificados se usarán únicamente para validar la conexión TLS entre el nodo hoja y el dispositivo de puerta de enlace.

Nuestra configuración usa la **entidad de certificación del propietario de la instancia de IoT Hub** como:
* un certificado de firma para la configuración del runtime de IoT Edge en todos los dispositivos de IoT Edge y como
* un certificado de clave pública que se instala en los dispositivos de nivel inferior.

El resultado es una solución que permite a todos los dispositivos usar cualquier dispositivo de IoT Edge como puerta de enlace, siempre y cuando estén conectados al mismo centro de IoT.

## <a name="create-the-certificates-for-test-scenarios"></a>Creación de los certificados para escenarios de prueba

Puede usar los scripts de Powershell y Bash de ejemplo descritos en [ejemplo de administración de un certificado de entidad de certificación][lnk-ca-scripts] para generar una **entidad de certificación del propietario de la instancia de IoT Hub** autofirmado y certificados de dispositivo firmados con él.

>[!IMPORTANT]
>Este ejemplo solo está pensado para fines de prueba. Para escenarios de producción, consulte [Protección de su implementación de IoT][lnk-iothub-secure-deployment] para obtener instrucciones de Azure IoT sobre cómo proteger la solución de IoT y aprovisionar el certificado en consecuencia.


1. Clone los [SDK y bibliotecas de Microsoft Azure IoT para C] de GitHub:

   ```
   git clone -b modules-preview https://github.com/Azure/azure-iot-sdk-c.git 
   ```

2. Para instalar los scripts de certificados, siga las instrucciones que aparecen en el **Paso 1: Configuración inicial** de [Administración de un certificado de entidad de certificación][lnk-ca-scripts]. 
3. Para generar la **entidad de certificación del propietario de la instancia de IoT Hub**, siga las instrucciones que aparecen en el **Paso 2: Creación de la cadena de certificados**. Los dispositivos de nivel inferior usan este archivo para validar la conexión.
4. Use las instrucciones de Bash o de PowerShell para generar un certificado para el dispositivo de puerta de enlace:

### <a name="bash"></a>Bash

Cree el certificado de dispositivo nuevo:

   ```
   ./certGen.sh create_edge_device_certificate myGateway
   ```

Se crean archivos nuevos: .\certs\new-edge-device.* contiene la clave pública y PFX, y .\private\new-edge-device.key.pem contiene la clave privada del dispositivo.
 
En el directorio `certs`, ejecute el comando siguiente para obtener la cadena completa de la clave pública del dispositivo:

   ```
   cat ./new-edge-device.cert.pem ./azure-iot-test-only.intermediate.cert.pem ./azure-iot-test-only.root.ca.cert.pem > ./new-edge-device-full-chain.cert.pem
   ```

### <a name="powershell"></a>PowerShell

Cree el certificado de dispositivo nuevo: 
   ```
   New-CACertsEdgeDevice myGateway
   ```

Se crean nuevos archivos myEdgeDevice\* , que contienen la clave pública, la clave privada y el archivo PFX de este certificado. 

Cuando se le pida una contraseña durante el proceso de firma, escriba "1234".

## <a name="configure-a-gateway-device"></a>Configuración de un dispositivo de puerta de enlace

Para configurar el dispositivo de IoT Edge como puerta de enlace, solo hay que configurar el uso del certificado de dispositivo creado en la sección anterior.

Asumimos los nombres de archivo siguientes de los scripts de ejemplo anteriores:

| Salida | Script de Bash | PowerShell |
| ------ | ----------- | ---------- |
| Certificado de dispositivo | `certs/new-edge-device.cert.pem` | `certs/new-edge-device.cert.pem` |
| Clave privada de dispositivo | `private/new-edge-device.cert.pem` | `private/new-edge-device.cert.pem` |
| Cadena de certificados de dispositivo | `certs/new-edge-device-full-chain.cert.pem` | `certs/new-edge-device-full-chain.cert.pem` |
| Entidad de certificación del propietario de la instancia de IoT Hub | `certs/azure-iot-test-only.root.ca.cert.pem` | `RootCA.pem` |

Proporcione la información de dispositivo y certificado al runtime de IoT Edge. 
 
En Linux, con la salida de Bash:

   ```
   sudo iotedgectl setup --connection-string {device connection string}
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
        --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem
   ```

En Windows, con la salida de PowerShell:

   ```
   iotedgectl setup --connection-string {device connection string}
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
        --owner-ca-cert-file {full path}/RootCA.pem
   ```

De forma predeterminada los scripts de ejemplo no establecen una frase de contraseña para la clave privada de dispositivo. Si establece una frase de contraseña, agregue el siguiente parámetro:

   ```
   --device-ca-passphrase {passphrase}
   ```

El script le solicita que establezca una frase de contraseña para el certificado del agente de Edge. Reinicie el runtime de IoT Edge después de la ejecución de este comando:

   ```
   iotedgectl restart
   ```

## <a name="configure-a-downstream-device"></a>Configuración de un dispositivo de nivel inferior

Un dispositivo de nivel inferior puede ser cualquier aplicación que usa el [SDK de dispositivo IoT de Azure][lnk-devicesdk], como la aplicación sencilla describa en [Conexión del dispositivo en IoT Hub con .NET][lnk-iothub-getstarted].

En primer lugar, una aplicación de dispositivo de nivel inferior tiene que confiar en la **entidad de certificación del propietario de la instancia de IoT Hub** para validar las conexiones TLS en los dispositivos de puerta de enlace. Normalmente, este paso puede realizarse de dos maneras: en el nivel del sistema operativo o, para determinados idiomas, en el nivel de aplicación.

Por ejemplo, para aplicaciones. NET, puede agregar el siguiente fragmento de código para confiar en un certificado en formato PEM almacenado en la ruta de acceso `certPath`. Según la versión del script que usó la ruta de acceso hace referencia a `certs/azure-iot-test-only.root.ca.cert.pem` (Bash) o a `RootCA.pem` (Powershell).

   ```
   using System.Security.Cryptography.X509Certificates;
   
   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

La realización de este paso en el nivel de sistema operativo es diferente en Windows y por las distribuciones de Linux.

El segundo paso consiste en inicializar el SDK de dispositivo de IoT Hub con una cadena de conexión que hace referencia al nombre de host del dispositivo de puerta de enlace.
Esto se hace anexando la propiedad `GatewayHostName` a la cadena de conexión del dispositivo. Por ejemplo, esto es una cadena de conexión de dispositivo de ejemplo para un dispositivo, al que se anexa la propiedad `GatewayHostName`:

   ```
   HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com
   ```

Estos dos pasos permiten que la aplicación de dispositivo se conecte con el dispositivo de puerta de enlace.

## <a name="next-steps"></a>Pasos siguientes
[Descripción de los requisitos y las herramientas para desarrollar módulos de IoT Edge][lnk-module-dev].

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens 
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/modules-preview/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus