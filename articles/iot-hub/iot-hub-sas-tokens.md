<properties
 pageTitle="Generación de tokens de seguridad del Centro de IoT | Microsoft Azure"
 description="Descripción de los diferentes tipos de tokens de seguridad (como los certificados X.509 y SAS) que utiliza el Centro de IoT y cómo generarlos."
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="06/07/2016"
 ms.author="elioda"/>

# Uso de tokens de seguridad del Centro de IoT y certificados X.509

El Centro de IoT usa tokens de seguridad para autenticar dispositivos y servicios para evitar el envío de claves en la conexión. Además, los tokens de seguridad están limitados en cuanto al ámbito y el período de validez. El [SDK del Centro de IoT de Azure][lnk-apis-sdks] genera automáticamente tokens sin requerir ninguna configuración especial. Algunos escenarios, sin embargo, requieren que el usuario genere y utilice directamente los tokens de seguridad. Estos incluyen el uso directo de las superficies AMQP, MQTT o HTTP o la implementación del patrón de servicio de token, como se explica en [Orientación del Centro de IoT][lnk-guidance-security].

El Centro de IoT también permite a los dispositivos autenticarse con esta plataforma utilizando certificados X.509. El Centro de IoT admite la autenticación basada en X.509 de dispositivos a través de los protocolos AMQP, AMQP por WebSockets y HTTP.

En este artículo se describe:

* El formato de los tokens de seguridad y cómo generarlos.
* Los principales casos de uso para la utilización de tokens de seguridad para autenticar servicios back-end y dispositivos.
* Los certificados X.509 compatibles con la autenticación de dispositivos.
* El proceso de registro de un certificado de cliente X.509 ligado a un dispositivo específico.
* El flujo en runtime entre el dispositivo y el Centro de IoT mediante un certificado de cliente X.509 para realizar autenticación.


## Estructura del token de seguridad
Utilice tokens de seguridad para conceder acceso limitado en tiempo a los dispositivos y servicios en la funcionalidad específica del Centro de IoT. Para asegurarse de que pueden conectarse únicamente los servicios y dispositivos autorizados, los tokens de seguridad deben estar firmados con una clave de directiva de acceso compartido o una clave simétrica que se almacena con una identidad de dispositivo en el registro de identidades.

Un token firmado con una clave de directiva de acceso compartido concede acceso a toda la funcionalidad asociada con los permisos de la directiva de acceso compartido. Consulte la [sección de seguridad de la Guía del desarrollador del Centro de IoT][lnk-devguide-security]. Por otro lado, un token firmado con una clave simétrica de la identidad del dispositivo solo concede el permiso **DeviceConnect** para la identidad del dispositivo asociado.

El token de seguridad tiene el formato siguiente:

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

Estos son los valores esperados:

| Valor | Descripción |
| ----- | ----------- |
| {signature} | Una cadena de firma HMAC-SHA256 con el formato: `{URL-encoded-resourceURI} + "\n" + expiry`. **Importante**: La clave se descodifica en base64 y se utiliza para realizar el cálculo de HMAC-SHA256. |
| {resourceURI} | Prefijo del identificador URI (por segmento) de los puntos de conexión a los que se puede obtener acceso con este token, que comienza por un nombre de host del Centro de IoT (sin protocolo) Por ejemplo, `myHub.azure-devices.net/devices/device1` |
| {expiry} | Cadenas UTF8 para el número de segundos transcurridos desde el tiempo 00:00:00 UTC el 1 de enero de 1970. |
| {URL-encoded-resourceURI} | Codificación de dirección URL en minúsculas del URI del recurso en minúsculas |
| {policyName} | El nombre de la directiva de acceso compartido a la que hace referencia este token. Ausente en el caso de los token que hacen referencia a las credenciales del registro de dispositivos. |

**Nota sobre el prefijo**: el prefijo URI se calcula por segmento y no por carácter. Por ejemplo `/a/b` es un prefijo de `/a/b/c` pero `/a/bc`.

Se trata de una función de nodo que calcula el token a partir de las entradas `resourceUri, signingKey, policyName, expiresInMins`. Las secciones siguientes detallan cómo inicializar las entradas diferentes para los distintos casos de uso de token.

    var crypto = require('crypto');

    var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
        resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();

        // Set expiration in seconds
        var expires = (Date.now() / 1000) + expiresInMins * 60;
        expires = Math.ceil(expires);
        var toSign = resourceUri + '\n' + expires;

        // using crypto
        var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
        const hmac = crypto.createHmac('sha256', decodedPassword);
        hmac.update(toSign);
        var base64signature = hmac.digest('base64');
        var base64UriEncoded = encodeURIComponent(base64signature);

        // construct autorization string
        var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
        + base64UriEncoded + "&se=" + expires;
        if (policyName) token += "&skn="+policyName;
        // console.log("signature:" + token);
        return token;
    };

> [AZURE.NOTE] Puesto que el período de validez del token se valida en equipos del Centro de IoT, es importante que el desfase del reloj del equipo que genera el token sea mínimo.

## Uso de tokens de SAS como dispositivo

Hay dos maneras de obtener permisos **DeviceConnect** con el Centro de IoT con tokens de seguridad: mediante una clave de identidad de dispositivo o una clave de directiva de acceso compartido.

Además, es importante tener en cuenta que puede acceder a toda la funcionalidad desde los dispositivos expuestos por diseño en los puntos de conexión con el prefijo `/devices/{deviceId}`.

> [AZURE.IMPORTANT] La única manera de que el Centro de IoT autentique un dispositivo específico es usando la clave simétrica de identidad de dispositivo. En los casos en los que se usa una directiva de acceso compartido para tener acceso a la funcionalidad del dispositivo, la solución debe tener en cuenta el componente que emite el token de seguridad como un subcomponente de confianza.

Los puntos de conexión del dispositivo son (con independencia del protocolo):

| Extremo | Funcionalidad |
| ----- | ----------- |
| `{iot hub host name}/devices/{deviceId}/messages/events` | Envío de mensajes de dispositivo a nube. |
| `{iot hub host name}/devices/{deviceId}/devicebound` | Recepción de mensajes de nube a dispositivo |

### Uso de una clave simétrica en el registro de identidades

Cuando se utiliza la clave simétrica de la identidad de un dispositivo para generar un token, el elemento policyName (`skn`) del token se omite.

Por ejemplo, un token creado para tener acceso a toda la funcionalidad de dispositivo debe tener los siguientes parámetros:

* URI de recurso: `{IoT hub name}.azure-devices.net/devices/{device id}`
* Clave de firma: cualquier clave simétrica de la identidad `{device id}`
* ningún nombre de directiva,
* cualquier fecha de expiración.

Un ejemplo de uso de la función del nodo anterior sería:

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var deviceKey ="...";

    var token = generateSasToken(endpoint, deviceKey, null, 60);

El resultado, que concede acceso a todas las funcionalidades del dispositivo1, sería:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697

> [AZURE.NOTE] Es posible generar un token seguro mediante la herramienta [Explorador de dispositivos][lnk-device-explorer] de la herramienta .NET.

### Uso de una directiva de acceso compartido

Al crear un token desde una directiva de acceso compartido, el campo de nombre de la directiva `skn` debe establecerse en el nombre de la directiva utilizada. También es necesario que la directiva conceda el permiso **DeviceConnect**.

Los dos escenarios principales para utilizar directivas de acceso compartido para tener acceso a la funcionalidad del dispositivo son:

* [Puertas de enlace del protocolo en la nube][lnk-azure-protocol-gateway]
* [Servicios de tokens][lnk-devguide-security] usados para implementar esquemas de autenticación personalizados

Dado que la directiva de acceso compartido potencialmente puede conceder acceso para conectarse a cualquier dispositivo, es importante usar el identificador URI de recurso correcto al crear tokens de seguridad. Esto es especialmente importante para los servicios de token, que tienen que definir el ámbito de token para un dispositivo específico mediante el identificador URI del recurso. Este punto es menos relevante para puertas de enlace de protocolo, puesto que ya están mediando el tráfico para todos los dispositivos.

Por ejemplo, un servicio de token que usa acceso compartido creado previamente denominado "**dispositivo**" crearía un token con los parámetros siguientes:

* URI de recurso: `{IoT hub name}.azure-devices.net/devices/{device id}`
* Clave de firma: una de las claves de la directiva `device`
* Nombre de la directiva: `device`
* cualquier fecha de expiración.

Un ejemplo de uso de la función del nodo anterior sería:

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var policyName = 'device';
    var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

El resultado, que concede acceso a todas las funcionalidades del dispositivo1, sería:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device

Una puerta de enlace de protocolo podría utilizar el mismo token para todos los dispositivos que simplemente establecen el URI de recurso en `myhub.azure-devices.net/devices`.

## Uso de tokens de seguridad de los componentes de servicio

Los componentes de servicio solo pueden generar tokens de seguridad mediante directivas de acceso compartido que conceden los permisos apropiados, como se explica en la [sección de seguridad de la Guía del desarrollador del Centro de IoT][lnk-devguide-security].

Estas son las funciones de servicio expuestas en los puntos de conexión:

| Extremo | Funcionalidad |
| ----- | ----------- |
| `{iot hub host name}/devices` | Creación, actualización, recuperación y eliminación de las identidades del dispositivo. |
| `{iot hub host name}/messages/events` | Recepción de mensajes de dispositivo a nube. |
| `{iot hub host name}/servicebound/feedback` | Recepción de comentarios para los mensajes de nube a dispositivo. |
| `{iot hub host name}/devicebound` | Envío de mensajes de nube a dispositivo. |

Por ejemplo, un servicio que genera el uso de la directiva de acceso compartido creada previamente denominada "**registryRead**" crearía un token con los parámetros siguientes:

* URI de recurso: `{IoT hub name}.azure-devices.net/devices`
* Clave de firma: una de las claves de la directiva `registryRead`
* Nombre de la directiva: `registryRead`
* cualquier fecha de expiración.

    var endpoint ="myhub.azure-devices.net/devices"; var policyName = 'device'; var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

El resultado, que concedería acceso para leer todas las identidades del dispositivo, sería:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead

## Certificados X.509 compatibles

Puede usar cualquier certificado X.509 para autenticar dispositivos con el Centro de IoT. En ella se incluye:

-   **Un certificado X.509 existente**. Puede que un dispositivo ya tenga un certificado X.509 asociado. El dispositivo puede usar este certificado para autenticarse con el Centro de IoT.

-   **Un certificado X-509 autofirmado y generado automáticamente**. Un fabricante de dispositivos o implementador interno pueden generar estos certificados y almacenar la clave privada correspondiente (y el certificado) en el dispositivo. Puede usar herramientas como [OpenSSL] y la utilidad [Windows SelfSignedCertificate] para este propósito.

-   **Certificado X.509 firmado por una CA**. También puede utilizar un certificado X.509 generado y firmado por una entidad de certificación (CA) con el objetivo de identificar un dispositivo y autenticarlo con el Centro de IoT.

Un dispositivo puede usar un token de seguridad o un certificado X.509 para realizar la autenticación, pero no ambos.

## Registro de certificados de cliente X.509 en un dispositivo

El [SDK de servicio de IoT de Azure para C#][lnk-service-sdk] \(versión 1.0.8 o posterior) permite registrar un dispositivo que utilice un certificado de cliente X.509 para realizar la autenticación. Otras API como la importación y exportación de dispositivos también admiten este tipo de certificados.

### Compatibilidad con C#

La clase **RegistryManager** permite registrar dispositivos mediante programación. En concreto, con los métodos **AddDeviceAsync** y **UpdateDeviceAsync**, los usuarios pueden registrar y actualizar un dispositivo en el registro de identidad de dispositivos del Centro de IoT. Estos dos métodos toman una instancia **Device** como entrada. La clase **Device** incluye una propiedad **Authentication** que permite al usuario especificar huellas digitales de certificados X.509 principales y secundarias. La huella digital representa un hash SHA-1 del certificado X.509 (que se almacena mediante codificación DER binaria). Los usuarios tienen la opción de especificar una huella digital principal o secundaria, o ambas. Las huellas digitales principales y secundarias se admiten para poder controlar los escenarios de sustitución de certificados.

> [AZURE.NOTE] El Centro de IoT no necesita el certificado de cliente X.509 completo ni tampoco lo almacena, solo requiere la huella digital.

A continuación, veremos un fragmento de código C# de ejemplo para registrar un dispositivo mediante un certificado X.509 de cliente:

```
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

## Uso de certificados de cliente X.509 durante las operaciones en runtime

El [SDK de dispositivo IoT de Azure para .NET][lnk-client-sdk] \(versión 1.0.11 o posterior) permite utilizar certificados de cliente X.509.

### Compatibilidad con C#

La clase **DeviceAuthenticationWithX509Certificate** permite crear instancias **DeviceClient** mediante un certificado de cliente X.509.

A continuación, veremos un fragmento de código de ejemplo:

```
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

[OpenSSL]: https://www.openssl.org/
[Windows SelfSignedCertificate]: https://technet.microsoft.com/library/hh848633
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device

<!---HONumber=AcomDC_0608_2016-->