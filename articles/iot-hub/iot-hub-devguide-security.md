---
title: Conceptos de seguridad de IoT Hub de Azure | Microsoft Docs
description: "Guía del desarrollador: se explica cómo controlar el acceso a IoT Hub para aplicaciones de dispositivo y de back-end. Además, incluye información sobre los tokens de seguridad y la compatibilidad con certificados X.509."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 45631e70-865b-4e06-bb1d-aae1175a52ba
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: e4fe5400ffcf4446392015aada031dd4dfbf238a
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="control-access-to-iot-hub"></a>Control del acceso a IoT Hub

En esta sección se describen las opciones para proteger su instancia de IoT Hub. IoT Hub usa *permisos* para conceder acceso a cada uno de los puntos de conexión de IoT Hub. Los permisos limitan el acceso a un centro de IoT basado en la funcionalidad.

En este artículo se describe:

* Los distintos permisos que puede conceder a un dispositivo o una aplicación de back-end para acceder a IoT Hub.
* El proceso de autenticación y los tokens que utiliza para comprobar los permisos.
* Cómo definir el ámbito de las credenciales para limitar el acceso a recursos específicos.
* IoT Hub admite los certificados X.509.
* Los mecanismos de autenticación de dispositivo personalizado que utilizan los registros de identidad de dispositivo o los esquemas de autenticación.

### <a name="when-to-use"></a>Cuándo se deben usar

Debe tener los permisos adecuados para acceder a cualquiera de los puntos de conexión de IoT Hub. Por ejemplo, un dispositivo debe incluir un token que contiene las credenciales de seguridad junto con cada mensaje que se envía a IoT Hub.

## <a name="access-control-and-permissions"></a>Permisos y control del acceso

Puede conceder los [permisos](#iot-hub-permissions) de las maneras siguientes:

* **Directivas de acceso compartido de nivel de centro de IoT**. Las directivas de acceso compartido pueden conceder cualquier combinación de los [permisos](#iot-hub-permissions). Puede definir las directivas en [Azure Portal][lnk-management-portal] o mediante programación usando las [API de REST del proveedor de recursos de IoT Hub][lnk-resource-provider-apis]. Un Centro de IoT recién creado tiene las siguientes directivas predeterminadas:

  * **iothubowner**: directiva con todos los permisos.
  * **service**: directiva con el permiso **ServiceConnect**.
  * **device**: directiva con el permiso **DeviceConnect**.
  * **registryRead**: directiva con el permiso **RegistryRead**.
  * **registryReadWrite**: directiva con los permisos **RegistryRead** y RegistryWrite.
  * **Credenciales de seguridad de cada dispositivo**. Cada centro de IoT contiene un [registro de identidades][lnk-identity-registry]. Para cada dispositivo de este registro de identidades, puede configurar credenciales de seguridad que concedan permisos **DeviceConnect** orientados a los puntos de conexión del dispositivo correspondiente.

Por ejemplo en una solución típica de IoT:

* El componente de administración de dispositivos usa la directiva *registryReadWrite* .
* El componente de procesador de eventos usa la directiva *service* .
* El componente de lógica empresarial de dispositivos en tiempo de ejecución usa la directiva *service* .
* Los dispositivos individuales se conectan usando las credenciales almacenadas en el registro de identidades del centro de IoT.

> [!NOTE]
> Para más detalles, vea [Permisos](#iot-hub-permissions).

## <a name="authentication"></a>Autenticación

Azure IoT Hub concede acceso a los puntos de conexión mediante la comprobación de un token con las directivas de acceso compartido y las credenciales de seguridad del registro de identidad.

Las credenciales de seguridad, como las claves simétricas, nunca se envían en la conexión.

> [!NOTE]
> El proveedor de recursos de Azure IoT Hub se protege mediante la suscripción de Azure, igual que todos los proveedores en [Azure Resource Manager][lnk-azure-resource-manager].

Consulte el artículo [Tokens de seguridad][lnk-sas-tokens] para más información sobre cómo crear y utilizar tokens de seguridad.

### <a name="protocol-specifics"></a>Detalles específicos de protocolo

Cada protocolo admitido, como MQTT, AMQP y HTTP, transporta tokens de diferentes maneras.

Al utilizar MQTT, el paquete CONNECT tiene deviceId como ClientId, {iothubhostname}/{deviceId} en el campo Nombre de usuario y un token SAS en el campo Contraseña. {iothubhostname} debe ser el CName completo del centro de IoT (por ejemplo, contoso.azure-devices.net).

Al usar [AMQP][lnk-amqp], IoT Hub admite [SASL PLAIN][lnk-sasl-plain] y [seguridad basada en notificaciones AMQP][lnk-cbs].

En el caso de la seguridad basada en notificaciones AMQP, la norma especifica cómo transmitir estos tokens.

Para SASL PLAIN, el **nombre de usuario** puede ser:

* `{policyName}@sas.root.{iothubName}` si usa tokens de nivel de centro de IoT.
* `{deviceId}@sas.{iothubname}` si usa tokens de ámbito por el dispositivo.

En ambos casos, el campo de contraseña contiene el token, como se describe en el artículo [Tokens de seguridad][lnk-sas-tokens].

HTTP implementa la autenticación mediante la inclusión de un token válido en el encabezado de solicitud **Authorization** .

#### <a name="example"></a>Ejemplo

Nombre de usuario (DeviceId distingue entre mayúsculas y minúsculas): `iothubname.azure-devices.net/DeviceId`

Contraseña (Generar token de SAS con el [Explorador de dispositivos][lnk-device-explorer]): `SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> Los [SDK IoT de Azure][lnk-sdks] generan tokens automáticamente cuando se conectan al servicio. En algunos casos, los SDK IoT de Azure no admiten todos los protocolos o todos los métodos de autenticación.

### <a name="special-considerations-for-sasl-plain"></a>Consideraciones especiales para SASL PLAIN

Cuando se usa SASL PLAIN con AMQP, un cliente que se conecta a una instancia de IoT Hub puede usar un token único para cada conexión TCP. Cuando el token expira, la conexión TCP se desconecta del servicio y desencadena una reconexión. Este comportamiento, aunque no resulta problemático para una aplicación de back-end, es perjudicial para una aplicación de dispositivo por las razones siguientes:

* Las puertas de enlace normalmente se conectan en nombre de muchos dispositivos. Cuando se usa SASL PLAIN, tienen que crear una conexión TCP distintiva para cada dispositivo que se conecta a un centro de IoT. Este escenario aumenta considerablemente el consumo de energía y de recursos de red, y aumenta la latencia de cada conexión de dispositivo.
* Los dispositivos con recursos restringidos se ven afectados negativamente por el aumento del uso de recursos para volver a conectarse después de cada expiración del token.

## <a name="scope-iot-hub-level-credentials"></a>Restricción de las credenciales de nivel de centro de IoT

Puede restringir el ámbito de las directivas de seguridad de nivel de centro de IoT mediante la creación de tokens con un URI de recurso restringido. Por ejemplo, el punto de conexión para enviar mensajes de dispositivo a nube desde un dispositivo es **/devices/{deviceId}/messages/events**. También puede usar una directiva de acceso compartido de nivel de centro de IoT con permisos **DeviceConnect** para firmar un token cuyo resourceURI sea **/devices/{deviceId}**. Este enfoque crea un token que solo se puede usar para enviar mensajes en nombre del **deviceId**del dispositivo.

Se trata de un mecanismo similar a la [directiva de edición de Event Hubs][lnk-event-hubs-publisher-policy], que permite que se implementen métodos de autenticación personalizados.

## <a name="security-tokens"></a>Tokens de seguridad

IoT Hub usa tokens de seguridad para autenticar dispositivos y servicios para evitar el envío de claves en la conexión. Además, los tokens de seguridad están limitados en cuanto al ámbito y el período de validez. Los [SDK IoT de Azure][lnk-sdks] generan automáticamente tokens sin necesidad de ninguna configuración especial. Algunos escenarios, requieren que el usuario genere y utilice directamente los tokens de seguridad. Entre los escenarios se incluyen los siguientes:

* El uso directo de las superficies MQTT, AMQP o HTTP.
* La implementación del modelo de servicio de tokens, como se explica en [Personalización de la autenticación de dispositivos][lnk-custom-auth].

IoT Hub también permite a los dispositivos autenticarse con esta plataforma utilizando [certificados X.509][lnk-x509].

### <a name="security-token-structure"></a>Estructura del token de seguridad

Utilice tokens de seguridad para conceder acceso limitado en tiempo a los dispositivos y servicios en la funcionalidad específica de IoT Hub. Para obtener autorización para conectarse a IoT Hub, los dispositivos y servicios deben enviar tokens de seguridad firmados con un acceso compartido o una clave simétrica. Dichas claves se almacenan con una identidad de dispositivo en el registro de identidad.

Un token firmado con una clave de acceso compartido concede acceso a toda la funcionalidad asociada con los permisos de la directiva de acceso compartido. Un token firmado con una clave simétrica de identidad del dispositivo solo concede el permiso **DeviceConnect** para la identidad del dispositivo asociado.

El token de seguridad tiene el formato siguiente:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Estos son los valores esperados:

| Valor | Description |
| --- | --- |
| {signature} |Una cadena de firma HMAC-SHA256 con el formato: `{URL-encoded-resourceURI} + "\n" + expiry`. **Importante**: La clave se descodifica en base64 y se utiliza para realizar el cálculo de HMAC-SHA256. |
| {resourceURI} |Prefijo del identificador URI (por segmento) de los puntos de conexión a los que se puede obtener acceso con este token, que comienza por un nombre de host de IoT Hub (sin protocolo) Por ejemplo: `myHub.azure-devices.net/devices/device1` |
| {expiry} |Cadenas UTF8 para el número de segundos transcurridos desde el tiempo 00:00:00 UTC el 1 de enero de 1970. |
| {URL-encoded-resourceURI} |Codificación de dirección URL en minúsculas del URI del recurso en minúsculas |
| {policyName} |El nombre de la directiva de acceso compartido a la que hace referencia este token. Ausente en caso de que el token haga referencia a las credenciales del registro de dispositivos. |

**Nota sobre el prefijo**: el prefijo URI se calcula por segmento y no por carácter. Por ejemplo `/a/b` es un prefijo de `/a/b/c` pero `/a/bc`.

El siguiente fragmento de Node.js muestra una función denominada **generateSasToken** que calcula el token de las entradas `resourceUri, signingKey, policyName, expiresInMins`. Las secciones siguientes detallan cómo inicializar las entradas diferentes para los distintos casos de uso de token.

```nodejs
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct autorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

Como comparación, el código de Python equivalente para generar un token de seguridad es:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Puesto que el período de validez del token se valida en equipos del IoT Hub, el desfase del reloj del equipo que genera el token debe ser mínimo.

### <a name="use-sas-tokens-in-a-device-app"></a>Uso de tokens de SAS en una aplicación de dispositivo

Existen dos maneras de obtener permisos **DeviceConnect** con IoT Hub mediante tokens de seguridad: con una [clave de dispositivo simétrica del registro de identidad](#use-a-symmetric-key-in-the-identity-registry) o una [clave de acceso compartido](#use-a-shared-access-policy).

Recuerde que puede acceder a toda la funcionalidad desde los dispositivos expuestos por diseño en los puntos de conexión con el prefijo `/devices/{deviceId}`.

> [!IMPORTANT]
> La única manera de que IoT Hub autentique un dispositivo específico es usando la clave simétrica de identidad de dispositivo. En los casos en los que se usa una directiva de acceso compartido para tener acceso a la funcionalidad del dispositivo, la solución debe tener en cuenta el componente que emite el token de seguridad como un subcomponente de confianza.

Los puntos de conexión del dispositivo son (con independencia del protocolo):

| Extremo | Funcionalidad |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Envío de mensajes de dispositivo a nube. |
| `{iot hub host name}/devices/{deviceId}/devicebound` |Recepción de mensajes de nube a dispositivo |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Uso de una clave simétrica en el registro de identidades

Cuando se utiliza la clave simétrica de la identidad de un dispositivo para generar un token, el elemento policyName (`skn`) del token se omite.

Por ejemplo, un token creado para tener acceso a toda la funcionalidad de dispositivo debe tener los siguientes parámetros:

* URI de recurso: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* clave de firma: cualquier clave simétrica de la identidad `{device id}` ,
* ningún nombre de directiva,
* cualquier fecha de expiración.

Un ejemplo de cómo utilizar la función anterior de Node.js sería:

```nodejs
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

El resultado, que concede acceso a todas las funcionalidades del dispositivo1, sería:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Se puede generar un token de SAS mediante la herramienta [Explorador de dispositivos][lnk-device-explorer] de .NET o la utilidad de la línea de comandos [iothub-explorer][lnk-iothub-explorer] basada en nodos y varias plataformas.

### <a name="use-a-shared-access-policy"></a>Uso de una directiva de acceso compartido

Cuando cree un token a partir de una directiva de acceso compartido, establezca el campo `skn` en el nombre de la directiva. Esta directiva debe conceder el permiso **DeviceConnect**.

Los dos escenarios principales para utilizar directivas de acceso compartido para tener acceso a la funcionalidad del dispositivo son:

* [puertas de enlace del protocolo en la nube][lnk-endpoints],
* [servicios de token][lnk-custom-auth] usados para implementar esquemas de autenticación personalizados.

Dado que la directiva de acceso compartido potencialmente puede conceder acceso para conectarse a cualquier dispositivo, es importante usar el identificador URI de recurso correcto al crear tokens de seguridad. Esto es especialmente importante para los servicios de token, que tienen que definir el ámbito de token para un dispositivo específico mediante el identificador URI del recurso. Este punto es menos relevante para puertas de enlace de protocolo, puesto que ya están mediando el tráfico para todos los dispositivos.

Por ejemplo, un servicio de token que usa acceso compartido creado previamente denominado **dispositivo** crearía un token con los parámetros siguientes:

* URI de recurso: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* clave de firma: una de las claves de la directiva `device` ,
* nombre de la directiva: `device`,
* cualquier fecha de expiración.

Un ejemplo de cómo utilizar la función anterior de Node.js sería:

```nodejs
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

El resultado, que concede acceso a todas las funcionalidades del dispositivo1, sería:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Una puerta de enlace de protocolo podría utilizar el mismo token para todos los dispositivos que simplemente establecen el URI de recurso en `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Uso de tokens de seguridad de los componentes de servicio

Los componentes de servicio solo pueden generar tokens de seguridad mediante directivas de acceso compartido que conceden los permisos apropiados, tal y como se explicó anteriormente.

Estas son las funciones de servicio expuestas en los puntos de conexión:

| Extremo | Funcionalidad |
| --- | --- |
| `{iot hub host name}/devices` |Creación, actualización, recuperación y eliminación de las identidades del dispositivo. |
| `{iot hub host name}/messages/events` |Recepción de mensajes de dispositivo a nube. |
| `{iot hub host name}/servicebound/feedback` |Recepción de comentarios para los mensajes de nube a dispositivo. |
| `{iot hub host name}/devicebound` |Envío de mensajes de nube a dispositivo. |

Por ejemplo, un servicio que genera el uso de la directiva de acceso compartido creada previamente denominada **registryRead** crearía un token con los parámetros siguientes:

* URI de recurso: `{IoT hub name}.azure-devices.net/devices`,
* clave de firma: una de las claves de la directiva `registryRead` ,
* nombre de la directiva: `registryRead`,
* cualquier fecha de expiración.

```nodejs
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

El resultado, que concedería acceso para leer todas las identidades del dispositivo, sería:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Certificados X.509 compatibles

Puede usar cualquier certificado X.509 para autenticar dispositivos con IoT Hub. Los certificados incluyen:

* **Un certificado X.509 existente**. Puede que un dispositivo ya tenga un certificado X.509 asociado. El dispositivo puede usar este certificado para autenticarse con IoT Hub.
* **Un certificado X-509 autofirmado y generado automáticamente**. Un fabricante de dispositivos o implementador interno pueden generar estos certificados y almacenar la clave privada correspondiente (y el certificado) en el dispositivo. Puede usar herramientas como [OpenSSL][lnk-openssl] y la utilidad [Windows SelfSignedCertificate][lnk-selfsigned] para este propósito.
* **Certificado X.509 firmado por una CA**. Puede utilizar un certificado X.509 generado y firmado por una entidad de certificación (CA) con el objetivo de identificar un dispositivo y autenticarlo con IoT Hub. IoT Hub solo comprueba que la huella digital presentada coincide con la configurada. IoT Hub no valida la cadena de certificados.

Un dispositivo puede usar un token de seguridad o un certificado X.509 para realizar la autenticación, pero no ambos.

### <a name="register-an-x509-certificate-for-a-device"></a>Registro de certificados X.509 en un dispositivo

El [SDK de servicios IoT de Azure para C#][lnk-service-sdk] (versión 1.0.8 o posterior) permite registrar un dispositivo que utilice un certificado X.509 para realizar la autenticación. Otras API como la importación y exportación de dispositivos también admiten este tipo de certificados.

### <a name="c-support"></a>Compatibilidad con C\#

La clase **RegistryManager** permite registrar dispositivos mediante programación. En concreto, los métodos **AddDeviceAsync** y **UpdateDeviceAsync** le permiten registrar y actualizar un dispositivo en el registro de identidad de IoT Hub. Estos dos métodos toman una instancia **Device** como entrada. La clase **Device** incluye una propiedad **Authentication** que permite al usuario especificar huellas digitales de certificados X.509 principales y secundarias. La huella digital representa un hash SHA-1 del certificado X.509 (que se almacena mediante codificación DER binaria). Los usuarios tienen la opción de especificar una huella digital principal o secundaria, o ambas. Las huellas digitales principales y secundarias se admiten para poder controlar los escenarios de sustitución de certificados.

> [!NOTE]
> IoT Hub no necesita el certificado X.509 completo ni tampoco lo almacena, solo requiere la huella digital.

A continuación, veremos un fragmento de código C\# de ejemplo para registrar un dispositivo mediante un certificado X.509:

```csharp
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

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Uso de certificados X.509 durante las operaciones en entorno de ejecución

El [SDK de dispositivos IoT de Azure para .NET][lnk-client-sdk] (versión 1.0.11 o posterior) permite utilizar certificados X.509.

### <a name="c-support"></a>Compatibilidad con C\#

La clase **DeviceAuthenticationWithX509Certificate** permite crear instancias **DeviceClient** mediante un certificado X.509. El certificado X.509 debe tener el formato PFX (también llamado PKCS #12), que incluye la clave privada.

A continuación, veremos un fragmento de código de ejemplo:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-authentication"></a>Personalización de la autenticación de dispositivos

Puede usar el [registro de identidad][lnk-identity-registry] de IoT Hub para configurar las credenciales de seguridad de cada dispositivo y el control de acceso mediante [tokens][lnk-sas-tokens]. Si una solución IoT ya tiene un registro de identidad personalizado y un esquema de autenticación, considere la posibilidad de crear un *servicio de token* para integrar esta infraestructura con IoT Hub. De este modo, puede usar otras características de IoT en la solución.

Un servicio de token es un servicio en la nube personalizado. Usa una *directiva de acceso compartido* de IoT Hub con permisos **DeviceConnect** para crear *tokens centrados en el dispositivo*. Estos tokens permiten que un dispositivo se conecte al centro de IoT.

![Pasos del modelo de servicio de tokens][img-tokenservice]

Estos son los pasos principales del modelo de servicio de tokens:

1. Cree una directiva de acceso compartido de IoT Hub con permisos **DeviceConnect** para IoT Hub. Puede crear esta directiva en [Azure Portal][lnk-management-portal] o mediante programación. El servicio de tokens usará esta directiva para firmar los tokens que crea.
1. Cuando un dispositivo necesita tener acceso al centro de IoT, solicita un token firmado al servicio de tokens. El dispositivo se puede autenticar mediante el esquema de autenticación o registro de identidad personalizado para determinar la identidad del dispositivo que el servicio de token usa para crear el token.
1. El servicio de token devuelve un token. El token se crea utilizando `/devices/{deviceId}` como `resourceURI`, con `deviceId` como el dispositivo que se autentica. El servicio de token usa la directiva de acceso compartido para construir el token.
1. El dispositivo usa el token directamente con el centro de IoT.

> [!NOTE]
> Puede usar la clase .NET [SharedAccessSignatureBuilder][lnk-dotnet-sas] o la clase de Java [IotHubServiceSasToken][lnk-java-sas] para crear un token en el servicio correspondiente.

El servicio de token puede establecer la caducidad de los tokens como desee. Cuando expira el token, el centro de IoT interrumpe la conexión de dispositivo. A continuación, el dispositivo debe solicitar un nuevo token al servicio de token. Un tiempo de expiración corto aumenta la carga tanto en el dispositivo como en el servicio de token.

Para que un dispositivo se conecte al centro, deberá agregarlo al registro de identidades del centro de IoT aunque ya use un token y no una clave de dispositivo para conectarse. Por tanto, puede continuar usando el control de acceso por dispositivo habilitando o deshabilitando las identidades del dispositivo en el [registro de identidad][lnk-identity-registry]. Esto mitiga los riesgos de usar tokens con tiempos de expiración largos.

### <a name="comparison-with-a-custom-gateway"></a>Comparación con una puerta de enlace personalizada

El modelo de servicio de token es el método recomendado para implementar un esquema de autenticación o registro de identidades personalizado en IoT Hub. Este modelo se recomienda porque IoT Hub sigue controlando la mayoría del tráfico de la solución. Hay casos, sin embargo, en los que el esquema de autenticación personalizado está tan imbricado con el protocolo que se necesita una *puerta de enlace personalizada* que procese todo el tráfico. Un ejemplo de escenario de este tipo es la [seguridad de la capa de transporte (TLS) y las claves previamente compartidas (PSK)][lnk-tls-psk]. Consulte el tema [Puerta de enlace de protocolos][lnk-protocols] para más información.

## <a name="reference-topics"></a>Temas de referencia:

Los siguientes temas de referencia proporcionan más información sobre el control de acceso a su IoT Hub.

## <a name="iot-hub-permissions"></a>Permisos de IoT Hub

La tabla siguiente enumera los permisos que se puede utilizar para controlar el acceso a su centro de IoT.

| Permiso | Notas |
| --- | --- |
| **RegistryRead**. |Concede acceso de lectura al Registro de identidad. Para más información, consulte [Registro de identidad][lnk-identity-registry]. <br/>Este permiso se usa en servicios de back-end en la nube. |
| **RegistryReadWrite**. |Concede acceso de lectura y escritura al Registro de identidad. Para más información, consulte [Registro de identidad][lnk-identity-registry]. <br/>Este permiso se usa en servicios de back-end en la nube. |
| **ServiceConnect**. |Concede acceso a los puntos de conexión de comunicación y supervisión accesibles desde el servicio en la nube. <br/>Concede permiso para recibir mensajes de dispositivo a nube, enviar mensajes de nube a dispositivo y recuperar las confirmaciones de entrega correspondientes. <br/>Concede permiso para recuperar las confirmaciones de entrega para las cargas de archivos. <br/>Concede permiso para tener acceso a los dispositivos gemelos para actualizar las etiquetas y las propiedades deseadas, recuperar las propiedades notificadas y ejecutar consultas. <br/>Este permiso se usa en servicios de back-end en la nube. |
| **DeviceConnect**. |Concede acceso a los puntos de conexión accesibles desde los dispositivos. <br/>Concede permiso para enviar mensajes de dispositivo a nube y recibir mensajes de nube a dispositivo. <br/>Concede permiso para realizar la carga de archivos desde un dispositivo. <br/>Concede permiso para recibir notificaciones sobre las propiedades de los dispositivos gemelos que desee y actualizar las propiedades notificadas de esos dispositivos gemelos. <br/>Concede permiso para realizar cargas de archivos. <br/>Este permiso lo usan los dispositivos. |

## <a name="additional-reference-material"></a>Material de referencia adicional

Otros temas de referencia en la guía del desarrollador de IoT Hub son los siguientes:

* En [Puntos de conexión de IoT Hub][lnk-endpoints], se describen los diferentes puntos de conexión que expone cada centro de IoT Hub para las operaciones en tiempo de ejecución y de administración.
* En [Cuotas y limitación][lnk-quotas], se describen las cuotas y el comportamiento de limitación que se aplican al servicio IoT Hub.
* En [SDK de dispositivo y servicio IoT de Azure][lnk-sdks] se muestran los diversos SDK de lenguaje que puede usar para desarrollar aplicaciones para dispositivo y de servicio que interactúen con IoT Hub.
* En [Lenguaje de consulta de IoT Hub][lnk-query], se describe el lenguaje de consulta que se puede usar para recuperar información de IoT Hub sobre los trabajos y dispositivos gemelos.
* En [Compatibilidad con MQTT de IoT Hub][lnk-devguide-mqtt], se proporciona más información sobre la compatibilidad de IoT Hub con el protocolo MQTT.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a controlar el acceso a IoT Hub, puede interesarle los siguientes temas de la Guía del desarrollador de IoT Hub:

* [Uso de dispositivos gemelos para sincronizar el estado y las configuraciones][lnk-devguide-device-twins]
* [Invocación de un método directo en un dispositivo][lnk-devguide-directmethods]
* [Programación de trabajos en varios dispositivos][lnk-devguide-jobs]

Si desea probar algunos de los conceptos descritos en este artículo, puede interesarle los siguientes tutoriales de IoT Hub:

* [Introducción a Azure IoT Hub][lnk-getstarted-tutorial]
* [Cómo enviar mensajes de la nube a un dispositivo con IoT Hub][lnk-c2d-tutorial]
* [Procesamiento de mensajes del dispositivo a la nube de IoT Hub][lnk-d2c-tutorial]

<!-- links and images -->

[img-tokenservice]: ./media/iot-hub-devguide-security/tokenservice.png
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-openssl]: https://www.openssl.org/
[lnk-selfsigned]: https://technet.microsoft.com/library/hh848633

[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-sas-tokens]: iot-hub-devguide-security.md#security-tokens
[lnk-amqp]: https://www.amqp.org/
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-management-portal]: https://portal.azure.com
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.auth._iot_hub_service_sas_token
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-custom-auth]: iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: iot-hub-devguide-security.md#supported-x509-certificates
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
