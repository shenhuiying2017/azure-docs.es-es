<properties
 pageTitle="Guía de soluciones del Centro de IoT | Microsoft Azure"
 description="Temas de guía sobre las puertas de enlace, el aprovisionamiento de dispositivos y la autenticación para desarrollar soluciones de IoT con el Centro de IoT de Azure."
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="07/19/2016"
 ms.author="dobett"/>

# Diseño de la solución

Este artículo le ofrece una guía para diseñar las siguientes capacidades en la solución de Internet de las cosas (IoT):

- Aprovisionamiento de dispositivos
- Puertas de enlace de campo
- Autenticación de dispositivos
- Latido de dispositivo

## Aprovisionamiento de dispositivos

Las soluciones de IoT almacenan datos acerca de los dispositivos individuales, como:

- Claves de autenticación e identidad de dispositivo
- Versión y tipo de dispositivo de hardware
- Estado del dispositivo
- Capacidades y versiones del software
- Historial de comandos de dispositivo

Los datos del dispositivo que almacena una solución de IoT determinada dependen de los requisitos específicos de la solución. Sin embargo, como mínimo, una solución debe almacenar identidades del dispositivo y claves de autenticación. El Centro de IoT incluye un [registro de identidades][lnk-devguide-identityregistry] que puede almacenar valores para cada dispositivo como identificadores, claves de autenticación y códigos de estado. Una posible solución consiste en usar otros servicios de Azure como tablas, blobs o DocumentDB de Azure para almacenar datos de dispositivos adicionales.

*Aprovisionamiento de dispositivos* es el proceso de agregar los datos iniciales del dispositivo a los almacenes de la solución. Para habilitar un nuevo dispositivo para conectarse al centro, debe agregar un nuevo identificador de dispositivo y claves al [registro de identidades del Centro de IoT][lnk-devguide-identityregistry]. Como parte del proceso de aprovisionamiento, es posible que tenga que inicializar datos específicos del dispositivo en otros almacenes de la solución.

Las [API de registro de identidad del Centro de IoT][lnk-devguide-identityregistry] le permiten integrarlo en el proceso de aprovisionamiento.

## Puertas de enlace de campo

En una solución de IoT, un *puerta de enlace de campo* se encuentra entre los dispositivos y el Centro de IoT. Suele encontrarse cerca de los dispositivos. Los dispositivos se comunican directamente con la puerta de enlace de campo mediante un protocolo compatible con los dispositivos. La puerta de enlace de campo se comunica con el Centro de IoT mediante un protocolo que es compatible con el Centro de IoT. Una puerta de enlace de campo puede ser hardware muy especializado o un equipo de bajo consumo que ejecuta software que lleva a cabo el escenario de extremo a extremo al que va destinada la puerta de enlace.

Una puerta de enlace de campo es diferente de un dispositivo de enrutamiento de tráfico simple (como un firewall o un dispositivo de traducción de direcciones de red (NAT)) porque normalmente desempeña un rol activo en la administración del acceso y del flujo de la información en su solución. Por ejemplo, una puerta de enlace de campo puede:

- **Agregar compatibilidad con dispositivos nuevos y heredados**: existen millones de sensores y accionadores nuevos y heredados que no pueden enviar datos directamente a la nube. Estos dispositivos utilizan un protocolo que no es adecuado para Internet, no implementan cifrado o no pueden almacenar certificados de identidad. El uso de una puerta de enlace reduce la carga y el costo de conectar estos dispositivos.
- **Ejecutar análisis de extremos**: hay muchas operaciones que se pueden realizar localmente para reducir la cantidad de datos que se intercambian con la nube. Por ejemplo, el filtrado de datos, el procesamiento por lotes y la compresión. También puede ser deseable realizar algunos cálculos, como limpieza de datos o puntuación de un modelo de aprendizaje automático con datos en tiempo real en el entorno local.
- **Minimizar la latencia**: los milisegundos importan cuando está intentando impedir que la línea de fabricación se apague o restaurar un servicio eléctrico. Analizar los datos cerca del dispositivo que los recopila puede marcar la diferencia entre evitar un desastre y un error del sistema en cascada.
- **Conservar ancho de banda de red**: una plataforma petrolera en alta mar genera habitualmente entre 1 y 2 TB de datos cada día. Un Boeing 787 crea medio terabyte de datos por vuelo. No resulta práctico transportar enormes cantidades de datos procedentes de miles o cientos de miles de dispositivos perimetrales a la nube. Ni es necesario, ya que muchos análisis importantes no requieren procesamiento y almacenamiento a escala de nube.
- **Funcionar de manera confiable**: los datos IoT se usan cada vez más en la toma de decisiones que afectan a la seguridad de los ciudadanos y a infraestructura de vital importancia. La integridad y la disponibilidad de la infraestructura y de los datos no se pueden ver comprometidas por conexiones intermitentes a la nube. El uso de funcionalidades, como almacenar y reenviar para recopilar datos y actuar sobre ellos localmente para luego enviarlos a la nube cuando sea adecuado, ayuda a crear soluciones confiables.
- **Solucionar problemas de privacidad y seguridad**: los dispositivos IoT y los datos que producen han de estar protegidos. Las puertas de enlace pueden proporcionar servicios, por ejemplo, aislar dispositivos de la red Internet abierta, proporcionar cifrado y servicios de identidad para dispositivos que no son capaces de proporcionar estos servicios por sí mismos. Los datos almacenados o almacenados localmente en búfer se protegen y se elimina la información de identificación personal antes de enviarlos a través de Internet.

### Otras consideraciones

Puede usar el [SDK de puerta de enlace de IoT de Azure][lnk-gateway-sdk] para implementar una puerta de enlace de campo. Este SDK ofrece funcionalidad específica, como la posibilidad de multiplexar la comunicación desde varios dispositivos en la misma conexión al Centro de IoT.

## Personalización de la autenticación de dispositivos

Puede usar el [registro de identidades de dispositivo][lnk-devguide-identityregistry] del Centro de IoT para configurar las credenciales de seguridad de cada dispositivo y el control de acceso mediante el uso de [tokens][lnk-sas-token]. Sin embargo, si una solución IoT ya realizó una inversión importante en un registro personalizado de identidades de dispositivo o en un esquema de autenticación, puede integrar esta infraestructura ya existente con el Centro de IoT mediante la creación de un *servicio de token*. De este modo, puede usar otras características de IoT en la solución.

Un servicio de token es un servicio en la nube personalizado. Usa una *directiva de acceso compartido* del Centro de IoT con permisos **DeviceConnect** para crear tokens *centrados en el dispositivo*. Estos tokens permiten que un dispositivo se conecte al Centro de IoT.

  ![Pasos del modelo de servicio de tokens][img-tokenservice]

Estos son los pasos principales del modelo de servicio de tokens:

1. Cree una [directiva de acceso compartido del Centro de IoT][lnk-devguide-security] con permisos **DeviceConnect** para el Centro de IoT. Puede crear esta directiva en el [portal de Azure][lnk-portal] o mediante programación. El servicio de tokens usará esta directiva para firmar los tokens que crea.
2. Cuando un dispositivo necesita tener acceso al Centro de IoT, solicita un token firmado al servicio de tokens. El dispositivo se puede autenticar mediante el esquema de autenticación o registro de identidades de dispositivos personalizado para determinar la identidad del dispositivo que el servicio de token usa para crear el token.
3. El servicio de token devuelve un token. El token se crea según la [sección de seguridad de la Guía del desarrollador del Centro de IoT][lnk-devguide-security] utilizando `/devices/{deviceId}` como `resourceURI`, con `deviceId` como el dispositivo que se autentica. El servicio de token usa la directiva de acceso compartido para construir el token.
4. El dispositivo usa el token directamente con el Centro de IoT.

> [AZURE.NOTE] Puede usar la clase .NET [SharedAccessSignatureBuilder][lnk-dotnet-sas] o la clase de Java [IotHubServiceSasToken][lnk-java-sas] para crear un token en el servicio correspondiente.

El servicio de token puede establecer la caducidad de los tokens como desee. Cuando expira el token, el Centro de IoT interrumpe la conexión de dispositivo. A continuación, el dispositivo debe solicitar un nuevo token al servicio de token. Si usa un tiempo de expiración corto, aumentará la carga tanto en el dispositivo como en el servicio de token.

Para que un dispositivo se conecte al centro, deberá agregarlo al registro de identidades del dispositivo del Centro de IoT aunque ya use un token y no una clave de dispositivo para conectarse. Por tanto, puede continuar usando el control de acceso por dispositivo habilitando o deshabilitando las identidades del dispositivo en el [registro de identidades de dispositivo del Centro de IoT][lnk-devguide-identityregistry] en aquellos casos en los que el dispositivo se autentique con un token. Esto mitiga los riesgos de usar tokens con tiempos de expiración largos.

### Comparación con una puerta de enlace personalizada

El modelo de servicio de token es el método recomendado para implementar un esquema de autenticación o registro de identidades personalizado en el Centro de IoT. Se recomienda porque el Centro de IoT sigue controlando la mayoría del tráfico de la solución. Hay casos, sin embargo, en los que el esquema de autenticación personalizado está tan imbricado con el protocolo que se necesita un servicio que procese todo el tráfico (*puerta de enlace personalizada*). Un ejemplo de esto es la [seguridad de la capa de transporte (TLS) y las claves previamente compartidas (PSK)][lnk-tls-psk]. Consulte el tema [Puerta de enlace de protocolos][lnk-protocols] para más información.

## Latido de dispositivo <a id="heartbeat"></a>

El [registro de identidad del Centro de IoT][lnk-devguide-identityregistry] contiene un campo llamado "**connectionState**". Solo debe utilizar el campo **connectionState** durante el desarrollo y la depuración, las soluciones de IoT no deben consultar el campo en tiempo de ejecución (por ejemplo, para comprobar si un dispositivo está conectado con el fin de decidir si enviar un mensaje desde la nube al dispositivo o un SMS). Si la solución de IoT necesita saber si un dispositivo está conectado (ya sea en tiempo de ejecución o con una precisión superior a la que ofrece la propiedad **connectionState**), debe implementar el *patrón de latido*.

En el patrón de latido, el dispositivo envía mensajes de dispositivo a la nube al menos una vez en un período de tiempo predeterminado (por ejemplo, al menos una vez cada hora). Esto significa que incluso si un dispositivo no tiene ningún dato que enviar, seguirá enviando un mensaje de dispositivo a la nube vacío (normalmente con una propiedad que lo identifica como un latido). En el lado del servicio, la solución mantiene un mapa con el último latido recibido para cada dispositivo, y supone que hay un problema con un dispositivo si no recibe un mensaje de latido en el tiempo esperado.

Una implementación más compleja podría incluir la información de [supervisión de operaciones][lnk-devguide-opmon] para identificar los dispositivos que están intentando conectarse o comunicarse sin éxito. Al implementar el patrón de latidos, asegúrese de echar un vistazo a [las cuotas y limitaciones del Centro de IoT][].

> [AZURE.NOTE] Si una solución de IoT requiere el estado de conexión de dispositivos únicamente para determinar si enviar mensajes de la nube a dispositivos y los mensajes no se difunden a grandes conjuntos de dispositivos, un patrón mucho más sencillo a tener en cuenta es usar un breve tiempo de expiración. Así se consigue el mismo resultado que con el mantenimiento de un registro del estado de la conexión de los dispositivos con el patrón de latido, a la vez que resulta mucho más eficiente. También es posible hacer que el Centro de IoT le notifique, solicitando acuses de recibo de mensajes, de qué dispositivos pueden recibir mensajes y cuáles no se encuentran conectados o presentan errores. Consulte la [Guía del desarrollador del Centro de IoT de Azure][lnk-devguide-messaging] para obtener más información sobre los mensajes C2D.

## Pasos siguientes

Para más información acerca de planificación de la implementación del Centro de IoT, consulte:

- [Soporte para MQTT][lnk-mqtt]
- [Dispositivos compatibles][lnk-devices]
- [Compatibilidad con protocolos adicionales para Centro de IoT][lnk-protocols]
- [Comparación del Centro de IoT y los Centros de eventos][lnk-compare]
- [Escalado del Centro de IoT][lnk-scaling]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

- [Guía del desarrollador][lnk-devguide]
- [Exploración de la administración de dispositivos desde Centro de IoT de Azure con la IU de ejemplo][lnk-dmui]
- [SDK de puerta de enlace de IoT (beta): envío de mensajes del dispositivo a la nube con un dispositivo simulado usando Linux][lnk-gateway]
- [Administración de Centros de IoT a través del portal de Azure][lnk-portal-manage]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279

[lnk-portal]: https://portal.azure.com
[lnk-devguide-messaging]: iot-hub-devguide.md#messaging
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[las cuotas y limitaciones del Centro de IoT]: iot-hub-devguide.md#throttling
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[lnk-mqtt]: iot-hub-mqtt-support.md
[lnk-devices]: iot-hub-tested-configurations.md
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal-manage]: iot-hub-manage-through-portal.md
[lnk-sas-token]: iot-hub-sas-tokens.md

<!---HONumber=AcomDC_0720_2016-->