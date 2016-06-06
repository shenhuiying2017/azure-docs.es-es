<properties
 pageTitle="Temas de la guía del desarrollador para el centro de IoT | Microsoft Azure"
 description="Guía de desarrollador del Centro de IoT de Azure que incluye los puntos de conexión del Centro de IoT, seguridad, registro de identidad del dispositivo y mensajería"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="dobett"/>

# Guía del desarrollador del Centro de IoT de Azure

El centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y una aplicación back-end.

El Centro de IoT de Azure permite:

* Comunicaciones seguras con las credenciales de seguridad de cada dispositivo y el control de acceso.
* Una mensajería confiable de gran escala de dispositivo a la nube y de nube a dispositivo.
* Conectividad fácil del dispositivo con las bibliotecas de dispositivos para las plataformas y lenguajes más populares.

En este artículo se tratan los temas siguientes:

- [Puntos de conexión](#endpoints). En esta sección se describen los diferentes puntos de conexión que expone cada Centro de IoT para las operaciones en tiempo de ejecución y de administración.
- [Registro de identidad del dispositivo](#device-identity-registry). En esta sección se describe qué información se almacena en cada registro de identidad del dispositivo de Centro de IoT y cómo se puede acceder a ella y modificarla.
- [Seguridad](#security). En esta sección se describe el modelo de seguridad que se usa para conceder acceso a la funcionalidad de Centro de IoT tanto para los dispositivos como para los componentes de nube.
- [Mensajería](#messaging). En esta sección se describen las características de mensajería (dispositivo a la nube y nube a dispositivo) que muestra Centro de IoT.
- [Cuotas y limitación](#throttling). En esta sección se resumen las cuotas que se aplican al uso del Centro de IoT.

## Puntos de conexión <a id="endpoints"></a>

Centro de IoT de Azure es un servicio multiempresa, que muestra funcionalidades a diversos actores. En el siguiente diagrama se muestran los diferentes puntos de conexión expuestos por Centro de IoT.

![Puntos de conexión del Centro de IoT][img-endpoints]

Esta es una descripción de los puntos de conexión:

* **Proveedor de recursos**: el proveedor de recursos de Centro de IoT muestra una interfaz del [Administrador de recursos de Azure][lnk-arm] que permite a los propietarios de la suscripción de Azure crear Centros de IoT, actualizar las propiedades del Centro de IoT y eliminar Centros de IoT. Las propiedades del Centro de IoT controlan las directivas de seguridad de nivel de centro a diferencia del control de acceso de nivel de dispositivo (consulte [Control de acceso](#accesscontrol) a continuación) y las opciones funcionales para la mensajería de nube a dispositivo y de dispositivo a la nube. El proveedor de recursos también permite [exportar identidades de dispositivo](#importexport).
* **Administración de identidades de dispositivo**: cada Centro de IoT muestra un conjunto de puntos de conexión HTTP REST para administrar identidades del dispositivo (crear, recuperar, actualizar y eliminar). Las identidades del dispositivo se usan para autenticación de dispositivos y control de acceso. Consulte [Registro de identidad del dispositivo](#device-identity-registry) para obtener más información.
* **Puntos de conexión de dispositivos**: para cada dispositivo aprovisionado en el registro de identidad del dispositivo, Centro de IoT muestra un conjunto de puntos de conexión que puede usar el dispositivo para enviar y recibir mensajes.
    - *Envío de mensajes de dispositivo a nube*. Use este punto de conexión para enviar mensajes de dispositivo a la nube. Para obtener más información, consulte [Mensajería de dispositivo a nube](#d2c).
    - *Recepción de mensajes de nube a dispositivo*. El dispositivo usa ese punto de conexión para recibir mensajes de nube a dispositivos dirigidos. Para obtener más información, consulte [Mensajería de nube a dispositivo](#c2d).

    Estos puntos de conexión se exponen mediante los protocolos HTTP 1.1, [MQTT v3.1.1][lnk-mqtt] y [AMQP 1.0][lnk-amqp] Tenga en cuenta que AMQP también está disponible sobre [WebSockets][lnk-websockets] en el puerto 443.
* **Puntos de conexión de servicio**: cada Centro de IoT muestra un conjunto de puntos de conexión que el back-end de aplicaciones puede usar para comunicarse con los dispositivos. Estos extremos se exponen actualmente usando solo el protocolo [AMQP][lnk-amqp].
    - *Recepción de mensajes de dispositivo a nube*. Este punto de conexión es compatible con los [Centros de eventos de Azure][lnk-event-hubs] y puede usarse para leer todos los mensajes de dispositivo a la nube enviados por los dispositivos. Para obtener más información, consulte [Mensajería de dispositivo a nube](#d2c).
    - *Envío de mensajes de nube a dispositivo y recepción de confirmaciones de entrega*. Estos puntos de conexión permiten al back-end de aplicaciones enviar mensajes confiables de nube a dispositivo y recibir las confirmaciones de entrega o expiración correspondientes. Para obtener más información, consulte [Mensajería de nube a dispositivo](#c2d).

El artículo [SDK de Centro de IoT][lnk-apis-sdks] describe las distintas formas con las que se puede acceder a estos puntos de conexión.

Finalmente, es importante tener en cuenta que todos los puntos de conexión del Centro de IoT usan el protocolo [TLS][lnk-tls] y ningún punto de conexión se expone en canales sin cifrar o no seguros.

### Cómo interpretar los extremos compatibles con los centros de eventos. <a id="eventhubcompatible"></a>

Cuando se usa el [SDK de Bus de servicio de Azure para .NET](https://www.nuget.org/packages/WindowsAzure.ServiceBus) o el [host del procesador de eventos de Centros de eventos][], puede usar cualquier cadena de conexión de Centro de IoT con los permisos correctos y después usar **messages/events** como nombre del Centro de eventos.

Cuando se usan SDK (o integraciones de productos) que no detectan el Centro de IoT, tiene que recuperar un punto de conexión y un nombre de centro de eventos compatibles con los centros de eventos en la configuración del Centro de IoT en el [portal de Azure][]\:

1. En la hoja del Centro de IoT, haga clic en **Configuración** y después e **Mensajería**,
2. En la sección **Configuración de dispositivo a la nube**, encontrará los valores **Punto de conexión compatible con Centro de eventos**, **Nombre compatible con Centro de eventos** y **Particiones**.

    ![][img-eventhubcompatible]

> [AZURE.NOTE] A veces, cuando el SDK requiera un valor de **nombre de host** o un **espacio de nombres**, quite el esquema del **punto de conexión compatible con Centro de eventos**. Por ejemplo, si el punto de conexión compatible con el Centro de eventos es ****sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, el **nombre de host** sería **iothub-ns-myiothub-1234.servicebus.windows.net** y el **espacio de nombres** sería **iothub-ns-myiothub-1234**.

Luego, puede usar cualquier directiva de seguridad de acceso compartido que tenga permisos **ServiceConnect** para conectarse al Centro de eventos especificado.

En caso de que tenga que crear una cadena de conexión del Centro de eventos con la información anterior, use el modelo siguiente:

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

La siguiente es una lista de los SDK e integraciones que puede usar con los puntos de conexión compatibles con el Centro de eventos que muestra Centro de IoT:

* [Cliente de Centros de eventos de Java](https://github.com/hdinsight/eventhubs-client)
* [Spout de Apache Storm](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). Puede ver el [origen de spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) en GitHub.
* [Integración de Apache Spark](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## Registro de identidad del dispositivo

Cada Centro de IoT tiene un registro de identidad del dispositivo que se usa para crear recursos de cada dispositivo en el servicio, como una cola que contiene los mensajes de nube a dispositivo en curso, y para permitir el acceso a los puntos de conexión accesibles desde el dispositivo, tal como se explica en la sección [Control de acceso](#accesscontrol).

En un nivel superior, el registro de identidad del dispositivo es una colección de recursos de identidad de dispositivos compatible con REST. Las secciones siguientes detallan las propiedades de los recursos de identidad del dispositivo y las operaciones que el registro permite en las identidades.

> [AZURE.NOTE] Consulte [SDK de Centro de IoT][lnk-apis-sdks] para más información sobre el protocolo HTTP y los SDK que puede usar para interactuar con el registro de identidad de dispositivo.

### Propiedades de identidad del dispositivo <a id="deviceproperties"></a>

Las identidades de dispositivos se representan como documentos JSON con las propiedades siguientes.

| Propiedad | Opciones | Descripción |
| -------- | ------- | ----------- |
| deviceId | necesarias, de solo lectura en actualizaciones | Una cadena que distingue mayúsculas y minúsculas (de hasta 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| generationId | requerido, de solo lectura | Una cadena que distingue mayúsculas y minúsculas generada por el centro de hasta 128 caracteres. Se usa para distinguir dispositivos con el mismo **deviceId** cuando se eliminaron y se volvieron a crear. |
| ETag | requerido, de solo lectura | Una cadena que representa un valor de etag débil para la identidad del dispositivo, como por [RFC7232][lnk-rfc7232].|
| auth | opcional | Un objeto compuesto que contiene material de seguridad e información de autenticación. |
| auth.symkey | opcional | Un objeto compuesto que contiene una clave principal y una secundaria, almacenadas en formato base64. |
| status | requerido | Puede ser **Habilitado** o **Deshabilitado**. Si está **habilitado**, el dispositivo se puede conectar. Si está **deshabilitado**, este dispositivo no puede acceder a ningún extremo accesible desde el dispositivo. |
| statusReason | opcional | Una cadena de 128 caracteres que almacena el motivo del estado de la identidad del dispositivo. Se permiten todos los caracteres UTF-8. |
| statusUpdateTime | solo lectura | Fecha y hora de la última actualización del estado. |
| connectionState | solo lectura | **Conectado** o **Desconectado**, representa la vista del Centro de IoT del estado de conexión del dispositivo. **Importante**: Este campo debe usarse solo para fines de desarrollo o depuración. El estado de conexión se actualiza solo para dispositivos que usen AMQP o MQTT. Además, se basa en pings de nivel de protocolo (pings MQTT o pings AMQP) y puede tener un retraso de 5 minutos como máximo. Por estos motivos es posible que haya falsos positivos, como el caso de los dispositivos que se notifican como conectados pero que en realidad están desconectados. |
| connectionStateUpdatedTime | solo lectura | Fecha y hora de la última vez que se actualizó el estado de conexión. |
| lastActivityTime | solo lectura | Fecha y hora de la última vez que el dispositivo se conectó, recibió o envió un mensaje. |

> [AZURE.NOTE] El estado de la conexión solo puede representar la vista del Centro de IoT del estado de la conexión. Se pueden retrasar las actualizaciones de este estado según las configuraciones y las condiciones de red.

### Operaciones de identidad de dispositivos

El registro de identidad del dispositivo del Centro de IoT muestra las operaciones siguientes:

* Crear la identidad del dispositivo
* Actualizar la identidad del dispositivo
* Recuperar la identidad del dispositivo mediante el identificador
* Eliminar la identidad del dispositivo
* Enumerar hasta 1.000 identidades
* Exportar todas las identidades al almacenamiento de blobs
* Importar las identidades desde el almacenamiento de blobs

Todas estas operaciones permiten usar la simultaneidad optimista, tal como se especifica en [RFC7232][lnk-rfc7232].

> [AZURE.IMPORTANT] La única manera de recuperar todas las identidades en un registro de identidades de un centro es usar la funcionalidad [Exportar](#importexport).

Un registro de identidad de dispositivo de Centro de IoT:

- No contiene los metadatos de la aplicación.
- Es accesible como un diccionario utilizando **deviceId** como clave.
- No admite consultas expresivas.

Una solución de IoT normalmente tiene un almacén independiente específico de la solución que contiene los metadatos específicos de la aplicación. Por ejemplo, el almacén específico de la solución en una solución de un edificio inteligente puede registrar la sala en la que se implementa un sensor de temperatura.

> [AZURE.IMPORTANT] Solo debe utilizar el registro de identidad del dispositivo para las operaciones de aprovisionamiento y administración de dispositivos. Las operaciones de alto rendimiento en tiempo de ejecución no deben depender de la realización de operaciones en el registro de identidad del dispositivo. Por ejemplo, comprobar el estado de conexión de un dispositivo antes de enviar un comando no es un modelo compatible. Asegúrese de comprobar las [tasas de limitación](#throttling) para el registro de identidad del dispositivo y el patrón del [latido de dispositivo][lnk-guidance-heartbeat].

### Deshabilitación de dispositivos

Puede deshabilitar los dispositivos actualizando la propiedad **status** de una identidad en el registro. Normalmente se usa en dos escenarios:

- Durante el proceso de orquestación de aprovisionamiento. Para obtener más información, consulte [Diseño de la solución - Aprovisionamiento de dispositivos][lnk-guidance-provisioning].
- Si por cualquier motivo considera que un dispositivo está en peligro o no está autorizado.

### Importación y exportación de identidades de dispositivo <a id="importexport"></a>

Puede exportar identidades de dispositivo de forma masiva desde el registro de identidad de un Centro de IoT mediante operaciones asincrónicas en el [punto de conexión de proveedor de recursos del Centro de IoT](#endpoints). Las exportaciones son trabajos de ejecución prolongada que usan un contenedor de blobs proporcionado por el cliente para guardar los datos de identidad del dispositivo que se leen en el registro de identidad.

- Para obtener información detallada sobre la importación y exportación de API, consulte [Centro de IoT de Azure: API de proveedor de recursos][lnk-resource-provider-apis].
- Para más información sobre la ejecución de trabajos de importación y exportación, consulte [Administración de identidades de dispositivos de Centro de IoT de forma masiva][lnk-bulk-identity].

Puede importar identidades de dispositivo en bloque a un registro de identidad de un Centro de IoT mediante operaciones asincrónicas en el [punto de conexión del proveedor de recursos del Centro de IoT](#endpoints). Las importaciones son trabajos de ejecución prolongada que usan los datos de un contenedor de blobs proporcionado por el cliente para escribir datos de identidad del dispositivo en el registro de identidad del dispositivo.

- Para obtener información detallada sobre la importación y exportación de API, consulte [Centro de IoT de Azure: API de proveedor de recursos][lnk-resource-provider-apis].
- Para más información sobre la ejecución de trabajos de importación y exportación, consulte [Administración de identidades de dispositivos de Centro de IoT de forma masiva][lnk-bulk-identity].

## Seguridad <a id="security"></a>

En esta sección se describen las opciones para proteger el Centro de IoT de Azure.

### Control de acceso <a id="accesscontrol"></a>

Centro de IoT usa el siguiente conjunto de *permisos* para conceder acceso a los puntos de conexión del Centro de IoT. Los permisos limitan el acceso a un Centro de IoT basado en la funcionalidad.

* **RegistryRead**. Concede acceso de lectura al registro de identidad del dispositivo. Para obtener más información, consulte [Registro de identidad del dispositivo](#device-identity-registry).
* **RegistryReadWrite**. Concede acceso de lectura y escritura al registro de identidad del dispositivo. Para obtener más información, consulte [Registro de identidad del dispositivo](#device-identity-registry).
* **ServiceConnect**. Concede acceso a los puntos de conexión de comunicación y supervisión accesibles desde el servicio en la nube. Por ejemplo, concede permiso a los servicios en la nube de back-end para recibir mensajes de dispositivo a la nube, enviar mensajes de nube a dispositivo y recuperar las confirmaciones de entrega correspondientes.
* **DeviceConnect**. Concede acceso a los puntos de conexión de comunicación accesibles desde los dispositivos. Por ejemplo, concede permiso para enviar mensajes de dispositivo a nube y recibir mensajes de nube a dispositivo. Este permiso lo usan los dispositivos.

Puede conceder los permisos de las maneras siguientes:

* **Directivas de acceso compartido de nivel de centro**. Las directivas de acceso compartido pueden conceder cualquier combinación de los permisos enumerados anteriormente. Puede definir las directivas en el [portal de Azure][lnk-management-portal] o mediante programación usando las [API del proveedor de recursos del Centro de IoT de Azure][lnk-resource-provider-apis]. Un Centro de IoT recién creado tiene las siguientes directivas predeterminadas:

    - *iothubowner*: directiva con todos los permisos
    - *service*: directiva con el permiso **ServiceConnect**
    - *device*: directiva con el permiso **DeviceConnect**
    - *registryRead*: directiva con el permiso **RegistryRead**
    - *registryReadWrite*: directiva con los permisos **RegistryRead** y **RegistryWrite**

* **Credenciales de seguridad de cada dispositivo**. Cada Centro de IoT contiene un [registro de identidad del dispositivo](#device-identity-registry). Para cada dispositivo de este registro, puede configurar credenciales de seguridad que concedan permisos **DeviceConnect** orientados a los extremos del dispositivo correspondiente.

**Ejemplo**. En una solución típica de IoT:
- El componente de administración de dispositivos usa la directiva *registryReadWrite*.
- El componente del procesador de eventos usa la directiva *service*.
- El componente de lógica empresarial de dispositivos en tiempo de ejecución usa la directiva *service*.
- Los dispositivos individuales se conectan usando las credenciales almacenadas en el registro de identidad del Centro de IoT.

Para obtener orientación sobre temas de seguridad del Centro de IoT, consulte la sección sobre seguridad en [Diseño de la solución][lnk-guidance-security].

### Autenticación

El Centro de IoT de Azure concede acceso a los puntos de conexión mediante la comprobación de un token con las directivas de acceso compartido y las credenciales de seguridad del registro de identidad del dispositivo.

Las credenciales de seguridad, como las claves simétricas, nunca se envían en la conexión.

> [AZURE.NOTE] El proveedor de recursos del Centro de IoT de Azure se protege mediante la suscripción de Azure, igual que todos los proveedores en el [Administrador de recursos de Azure][lnk-azure-resource-manager].

Consulte el artículo [Uso de tokens de seguridad del Centro de IoT][lnk-sas-tokens] para más información sobre cómo crear y utilizar tokens de seguridad.

#### Detalles específicos de protocolo

Cada uno de los protocolos admitidos, como AMQP, MQTT y HTTP, transporta tokens de diferentes maneras.

HTTP implementa la autenticación mediante la inclusión de un token válido en el encabezado de solicitud **Authorization**.

Al usar [AMQP][lnk-amqp], el Centro de IoT admite [SASL PLAIN][lnk-sasl-plain] y [seguridad basada en notificaciones AMQP][lnk-cbs].

En el caso de la seguridad basada en notificaciones AMQP, la norma especifica cómo transmitir estos tokens.

Para SASL PLAIN, el **nombre de usuario** puede ser:

* `{policyName}@sas.root.{iothubName}` en el caso de los tokens de nivel de centro.
* `{deviceId}` en el caso de los tokens con ámbito de dispositivo.

En ambos casos, el campo de contraseña contiene el token, como se describe en el artículo [Uso de tokens de seguridad del Centro de IoT][lnk-sas-tokens].

Al utilizar MQTT, el paquete CONNECT tiene deviceId como ClientId, {iothubhostname}/{deviceId} en el campo Nombre de usuario y un token SAS en el campo Contraseña. {iothubhostname} debe ser el CName completo del Centro de IoT (por ejemplo, contoso.azure-devices.net).

##### Ejemplo: #####

Nombre de usuario (DeviceId distingue entre mayúsculas y minúsculas): `iothubname.azure-devices.net/DeviceId`

Contraseña (generar SAS con el explorador de dispositivos): `SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [AZURE.NOTE] Los [SDK del Centro de IoT de Azure][lnk-apis-sdks] generan tokens automáticamente cuando se conectan al servicio. En algunos casos, los SDK no admiten todos los protocolos o todos los métodos de autenticación.

#### SASL PLAIN comparado con CBS

Cuando se usa SASL PLAIN, un cliente que se conecta a un Centro de IoT puede usar un token único para cada conexión TCP. Cuando el token expira, la conexión TCP se desconecta del servicio y desencadena una reconexión. Este comportamiento, aunque no resulta problemático para un componente de back-end de aplicaciones, es muy perjudicial para una aplicación de dispositivo por los siguientes motivos:

*  Las puertas de enlace normalmente se conectan en nombre de muchos dispositivos. Cuando se usa SASL PLAIN, tienen que crear una conexión TCP distintiva para cada dispositivo que se conecta a un Centro de IoT. Esto aumenta considerablemente el consumo de energía y de recursos de red, y aumenta la latencia de cada conexión de dispositivo.
* Los dispositivos con recursos restringidos se verán afectados negativamente por el aumento del uso de recursos para volver a conectarse después de cada expiración del token.

### Control de las credenciales de nivel de centro

Puede restringir el ámbito de las directivas de seguridad de nivel de centro mediante la creación de tokens con un URI de recurso restringido. Por ejemplo, el punto de conexión para enviar mensajes de dispositivo a la nube desde un dispositivo es **/devices/{deviceId}/messages/events**. También puede usar una directiva de acceso compartido de nivel de centro con permisos **DeviceConnect** para firmar un token cuyo resourceURI sea **/devices/{deviceId}**, de forma que se cree un token que solo se pueda usar para enviar dispositivos en nombre del valor de **deviceId** del dispositivo.

Se trata de un mecanismo similar a la [directiva de editor de Centros de eventos][lnk-event-hubs-publisher-policy], que permite que se implementen métodos de autenticación personalizados, tal como se explica en la sección de seguridad de [Diseño de la solución][lnk-guidance-security].

## Mensajería

El Centro de IoT proporciona primitivas de mensajería para comunicarse:

- [Nube a dispositivo](#c2d): desde un back-end de aplicación (*servicio* o *nube*).
- [Dispositivo a nube](#d2c): desde un dispositivo a un back-end de aplicación.

Las propiedades básicas de la funcionalidad de mensajería del Centro de IoT son la confiabilidad y durabilidad de los mensajes. Esto permite la resistencia a la conectividad intermitente en el dispositivo y a los picos de carga del procesamiento de eventos en la nube. El Centro de IoT implementa *al menos una vez* garantías de entrega para la mensajería del dispositivo a la nube y de la nube al dispositivo.

Centro de IoT admite varios protocolos accesibles desde los dispositivos (por ejemplo, MQTT, AMQP y HTTP). Con el fin de admitir la interoperabilidad sin problemas entre protocolos, Centro de IoT define un formato de mensaje común que es compatible con todos los protocolos accesibles desde el dispositivo.

### Formato de mensajes <a id="messageformat"></a>

Los mensajes del Centro de IoT constan de:

* Un conjunto de *propiedades del sistema*. Estas son propiedades que interpreta o establece Centro de IoT. Este conjunto es el predeterminado.
* Un conjunto de *propiedades de la aplicación*. Se trata de un diccionario de propiedades de cadena que la aplicación puede definir y acceder sin necesidad de deserializar el cuerpo del mensaje. Centro de IoT nunca modifica estas propiedades.
* Un cuerpo binario opaco.

Consulte [API y SDK del Centro de IoT][lnk-apis-sdks] para obtener más información sobre cómo se codifica el mensaje en distintos protocolos.

Es el conjunto de propiedades del sistema en los mensajes del Centro de IoT.

| Propiedad | Descripción |
| -------- | ----------- |
| MessageId | Un identificador configurable por el usuario para el mensaje, que normalmente se usa para patrones de solicitud y respuesta. Formato: una cadena que distingue mayúsculas y minúsculas (de hasta 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Número de secuencia | Un número (exclusivo para cada cola de dispositivo) asignado por Centro de IoT a cada mensaje de nube a dispositivo. |
| Para | Se usa en mensajes de [nube a dispositivo](#c2d) para especificar el destino. |
| ExpiryTimeUtc | Fecha y hora de la expiración del mensaje. |
| EnqueuedTime | Fecha y hora en la que el Centro de IoT recibió el mensaje. |
| CorrelationId | Cadena de propiedad en un mensaje de respuesta que normalmente contiene el identificador del mensaje de la solicitud en los patrones de solicitud y respuesta. |
| UserId | Se usa para especificar el origen de los mensajes. Cuando el Centro de IoT genera mensajes, se establece en `{iot hub name}`. |
| Ack | Se usa en los mensajes de nube a dispositivo para solicitar a Centro de IoT que genere mensajes de comentarios debido al consumo del mensaje por el dispositivo. Valores posibles: **none** (valor predeterminado): no se genera ningún mensaje de comentarios, **positive**: recibe un mensaje de comentarios si el mensaje expiró, **negative**: recibe un mensaje de comentarios si el mensaje expiró (o se alcanzó el número máximo de entregas) sin que se complete en el dispositivo y **full**: comentarios positivos y negativos. Para obtener más información, consulte [Comentarios de mensajes](#feedback). |
| ConnectionDeviceId | Establecido por Centro de IoT en los mensajes de dispositivo a la nube. Contiene el **deviceId** del dispositivo que envió el mensaje. |
| ConnectionDeviceGenerationId | Establecido por Centro de IoT en los mensajes de dispositivo a la nube. Contiene el **generationId** (como se indica en [Propiedades de identidad de dispositivos](#deviceproperties)) del dispositivo que envió el mensaje. |
| ConnectionAuthMethod | Establecido por Centro de IoT en los mensajes de dispositivo a la nube. Información sobre el método de autenticación usado para autenticar el dispositivo que envía el mensaje. Para obtener más información, consulte [Propiedades contra la suplantación](#antispoofing).|

### Elección del protocolo de comunicación <a id="amqpvshttp"></a>

El Centro de IoT admite los protocolos [AMQP][lnk-amqp], AMQP sobre WebSockets, MQTT y HTTP/1 para las comunicaciones del dispositivo. A continuación se muestra una lista de las consideraciones con respecto a sus usos.

* **Patrón de nube a dispositivo**. HTTP/1 no cuenta con una forma eficaz de implementar la inserción de servidor. Por lo tanto, cuando se usa HTTP/1, los dispositivos sondean los mensajes de nube a dispositivo en el Centro de IoT. Esto resulta muy ineficaz tanto para el dispositivo como para el Centro de IoT. Las directrices actuales, cuando se utiliza HTTP/1, indican que cada dispositivo sondee cada 25 minutos o más. Por otro lado, AMQP y MQTT admiten la inserción de servidor al recibir mensajes de nube a dispositivo, lo que permite inserciones inmediatas de mensajes desde el Centro de IoT al dispositivo. Si le preocupa la latencia de entrega, es mucho mejor usar el protocolo AMQP o MQTT. Por otro lado, para los dispositivos apenas conectados, HTTP/1 funciona también.
* **Puertas de enlace de campo**. Cuando se utiliza HTTP/1 y MQTT, no puede conectar varios dispositivos (cada uno con sus propias credenciales por dispositivo) con la misma conexión TLS. En consecuencia, estos protocolos no son óptimos para implementar [escenarios de puerta de enlace de campo][lnk-azure-gateway-guidance], ya que requieren una conexión TLS entre dicha puerta de enlace y el Centro de IoT para cada dispositivo conectado a aquella.
* **Dispositivos con bajos recursos**. Las bibliotecas MQTT y HTTP/1 tienen una superficie menor que las bibliotecas AMQP. Por ello, si el dispositivo tiene pocos recursos (por ejemplo, menos de 1 MB de RAM), estos protocolos pueden ser la única implementación de protocolo disponible.
* **Cruce seguro de red**. El estándar MQTT escucha en el puerto 8883. Esto podría producir problemas en las redes que están cerradas para los protocolos que no son HTTP. HTTP y AMQP (sobre WebSockets) están disponibles para usarse en este escenario.
* **Tamaño de carga**. AMQP y MQTT son protocolos binarios, que son mucho más compactos que HTTP/1.

En un nivel alto, debe usar AMQP (o AMQP sobre WebSockets) siempre que sea posible y utilizar solo MQTT cuando las restricciones de recursos impidan el uso de AMQP. HTTP/1 debe usarse solo si el recorrido por la red y la configuración de red impiden el uso de MQTT y AMQP. Además, cuando se utiliza HTTP/1, cada dispositivo debe sondear para ver si hay mensajes de la nube a dispositivo cada 25 minutos o más.

> [AZURE.NOTE] Ciertamente, durante el desarrollo, es aceptable sondear con una frecuencia mayor de 25 minutos.

<a id="mqtt-support">
#### Notas sobre la compatibilidad con MQTT
Centro de IoT implementa el protocolo MQTT v3.1.1 con las siguientes limitaciones y comportamiento específico:

  * **No se admite QoS 2**: cuando un cliente de dispositivo publica un mensaje con **QoS 2**, el Centro de IoT cierra la conexión de red. Cuando un cliente de dispositivo se suscribe a un tema con **QoS 2**, el Centro de IoT concede el QoS de nivel 1 (el máximo) en el paquete **SUBACK**.
  * **Retain**: si un cliente de dispositivo publica un mensaje con la marca RETAIN establecida en 1, el Centro de IoT agrega la propiedad de aplicación **x-opt-retain** al mensaje. Esto significa que el Centro de IoT no conserva el mensaje, sino que lo pasa a la aplicación de back-end.
  
Consulte el artículo [Compatibilidad con MQTT del Centro de IoT][lnk-mqtt-support] para obtener más información.

Como consideración final, debe revisar la sección [Puerta de enlace de protocolos de IoT de Azure][lnk-azure-protocol-gateway], donde se explica cómo implementar una puerta de enlace de protocolo personalizado de alto rendimiento que interactúe directamente con el Centro de IoT. La puerta de enlace de protocolos de IoT de Azure le permite personalizar el protocolo del dispositivo para dar cabida a las implementaciones de MQTT de Brownfield u otros protocolos personalizados. La desventaja de este planteamiento es el requisito de autohospedar y operar una puerta de enlace de protocolo personalizado.

### Dispositivo a nube <a id="d2c"></a>

Tal y como se detalla en la sección [Puntos de conexión](#endpoints), los mensajes de dispositivo a nube se envían a través de un punto de conexión orientado al dispositivo (**/devices/{deviceId}/messages/events**) y se reciben por medio de un punto de conexión orientado al servicio (**/messages/events**), que es compatible con [Centros de eventos][lnk-event-hubs]. Por lo tanto, puede usar la integración de los Centros de eventos estándar y los SDK para recibir mensajes de dispositivo a la nube.

El Centro de IoT implementa la mensajería de dispositivo a la nube de manera muy similar a los [centros de eventos][lnk-event-hubs], siendo los mensajes de dispositivo a nube del Centro de IoT más similares a los *eventos* de los centros de eventos que los [mensajes][lnk-servicebus] del *Bus de servicio*.

Esta implementación tiene las implicaciones siguientes:

* De forma similar a los *eventos* de los centros de eventos, los mensajes de dispositivo a nube son duraderos y se conservan en un Centro de IoT hasta 7 días (consulte [Opciones de configuración de dispositivo a nube](#d2cconfiguration)).
* Los mensajes de dispositivo a nube se fragmentan en un conjunto fijo de particiones que se establece en el momento de la creación (consulte [Opciones de configuración de dispositivo a nube](#d2cconfiguration)).
* De igual forma que los centros de eventos, los clientes que leen mensajes de dispositivo a nube tienen que administrar particiones y puntos de control. Consulte [Centros de eventos: consumo de eventos][lnk-event-hubs-consuming-events].
* Al igual que los eventos de los centros de eventos, los mensajes de dispositivo a nube pueden tener como máximo 256 Kb y se pueden agrupar en lotes para optimizar los envíos. Los lotes pueden tener un tamaño máximo de 256 Kb y como máximo 500 mensajes.

Sin embargo, hay algunas diferencias importantes entre los mensajes de dispositivo a nube del Centro de IoT y los Centros de eventos:

* Como se explica en la sección [Seguridad](#security), el Centro de IoT permite la autenticación por dispositivo y el control de acceso.
* El Centro de IoT permite millones de dispositivos conectados simultáneamente (consulte [Cuotas y limitación](#throttling)), mientras que los centros de eventos se limitan a 5000 conexiones AMQP por espacio de nombres.
* El Centro de IoT no admite la creación de particiones arbitraria con un valor **PartitionKey**. Los mensajes de dispositivo a nube se particionan en función de su **deviceId** de origen.
* El escalado de un Centro de IoT es ligeramente diferente en el caso de los centros de eventos. Para obtener más información, consulte [Escalado de un Centro de IoT][lnk-guidance-scale].

Tenga en cuenta que esto no significa que puede sustituir Centro de IoT en Centros de eventos en todas las situaciones. Por ejemplo, en algunos cálculos de procesamiento de eventos, podría ser necesario volver a crear particiones de eventos con respecto a un campo o propiedad diferentes antes de analizar los flujos de datos. En esta situación, puede usar un Centro de eventos para desacoplar las dos partes de la canalización de procesamiento de la transmisión. Para obtener más información, consulte la sección *Particiones* del artículo [Información general de los Centros de eventos de Azure][lnk-eventhub-partitions].

Para obtener información detallada sobre cómo usar la mensajería de dispositivo a nube, consulte [API y SDK del Centro de IoT][lnk-apis-sdks].

> [AZURE.NOTE] Cuando se usa HTTP para enviar mensajes de dispositivo a nube, los valores y nombres de propiedad solo pueden contener caracteres alfanuméricos ASCII más ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

#### Tráfico sin telemetría

En muchos casos, además de los puntos de datos de telemetría, los dispositivos también envían mensajes y solicitudes que requieren la ejecución y el control de la capa de lógica de negocio de la aplicación. Por ejemplo, alertas críticas que deben desencadenar una acción específica en el back-end o respuestas de dispositivo a los comandos enviados desde el back-end.

Consulte el artículo sobre el [procesamiento de dispositivo a nube][lnk-guidance-d2c-processing] para obtener más información sobre la mejor manera de procesar este tipo de mensajes.

#### Opciones de configuración de dispositivo a nube <a id="d2cconfiguration"></a>

Centro de IoT muestra las propiedades siguientes para permitirle controlar la mensajería de dispositivo a la nube.

* **Número de particiones**. Establezca esta propiedad durante la creación para definir el número de particiones para ingesta de eventos de dispositivo a la nube.
* **Tiempo de retención**. Esta propiedad especifica el tiempo de retención para los mensajes de dispositivo a nube. El valor predeterminado es un día, pero se puede aumentar a siete días.

Además, de forma análoga a los Centros de eventos, el Centro de IoT permite administrar los grupos de consumidores en el punto de conexión de recepción del dispositivo a la nube.

Puede modificar todas estas propiedades mediante el [Portal de Azure][lnk-management-portal], o por medio de programación con las [API del proveedor de recursos del Centro de IoT de Azure][lnk-resource-provider-apis].

#### Propiedades contra la suplantación <a id="antispoofing"></a>

Para evitar la suplantación de dispositivos en los mensajes de dispositivo a nube, el Centro de IoT marca todos los mensajes con las siguientes propiedades:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Las dos primeras contienen los valores **deviceId** y **generationId** del dispositivo de origen, tal como se indicó en [Propiedades de identidad de dispositivos](#deviceproperties).

La propiedad **ConnectionAuthMethod** contiene un objeto JSON serializado con las siguientes propiedades:

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

### Nube a dispositivo <a id="c2d"></a>

Tal y como se detalla en la sección [Puntos de conexión](#endpoints), puede enviar mensajes de nube a dispositivo mediante un punto de conexión orientado al servicio (**/messages/devicebound**), y un dispositivo puede recibirlos por medio de un punto de conexión específico del dispositivo (**/devices/{deviceId}/messages/devicebound**).

Cada mensaje de la nube al dispositivo se destina a un único dispositivo al establecerse la propiedad **to** en **/devices/{deviceId}/messages/devicebound**.

**Importante**: cada cola de dispositivos puede contener como máximo 50 mensajes de nube a dispositivo. Si se intenta enviar más mensajes al mismo dispositivo, se producirá un error.

> [AZURE.NOTE] Al enviar mensajes de nube a dispositivo, los valores y nombres de propiedad solo pueden contener caracteres alfanuméricos ASCII más ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

#### Ciclo de vida de los mensajes <a id="message lifecycle"></a>

Para implementar *al menos una vez* la garantía de entrega, los mensajes de nube a dispositivo se almacenan en colas por dispositivo y los dispositivos tienen que reconocer explícitamente la *finalización* para que el Centro de IoT pueda quitarlos de la cola. Esto garantiza la resistencia frente a errores de dispositivo y de conectividad.

En el diagrama siguiente se detalla el gráfico de estado del ciclo de vida de un mensaje de nube a dispositivo.

![Ciclo de vida de los mensajes de nube a dispositivo][img-lifecycle]

Cuando el servicio envía un mensaje, se considera que está *Enqueued* (En cola). Cuando un dispositivo quiere *recibir* un mensaje, el Centro de IoT *bloquea* este último (establece el estado en **Invisible**) para permitir que otros subprocesos del mismo dispositivo empiecen a recibir otros mensajes. Cuando el subproceso de un dispositivo termina de procesar un mensaje, informa al Centro de IoT *finalizando* dicho mensaje.

Un dispositivo también puede:

- *Rechazar* el mensaje, lo que hace que el Centro de IoT lo establezca en estado **Deadlettered** (Procesado como devuelto).
- *Abandonar* el mensaje, lo que hace que el Centro de IoT vuelva a ponerlo en la cola con el estado **Enqueued** (En cola).

Podría producirse un error en el subproceso al procesar un mensaje sin notificar a Centro de IoT. En este caso, los mensajes pasan automáticamente del estado **Invisible** al estado **Enqueued** (En cola) después de un *tiempo de espera de visibilidad (o bloqueo)* con un valor predeterminado de un minuto. Un mensaje puede cambiar entre los estados **Enqueued** (En cola) e **Invisible** un número de veces especificado en la propiedad *Número máximo de entregas* en el Centro de IoT. Después de ese número de transiciones, el Centro de IoT establece el estado del mensaje en **Deadlettered** (Procesado como devuelto). De igual forma, el Centro de IoT establece el estado de un mensaje en **Enqueued** (Procesado como devuelto) después de su fecha de caducidad (consulte [Período de vida](#ttl)).

Para ver un tutorial sobre los mensajes de nube a dispositivo, consulte [Introducción a los mensajes de nube a dispositivo del Centro de IoT de Azure][lnk-getstarted-c2d-tutorial]. Para consultar temas de referencia sobre cómo las diferentes API y SDK exponen la funcionalidad de dispositivo de nube, vea [API y SDK del Centro de IoT][lnk-apis-sdks].

> [AZURE.NOTE] Normalmente los mensajes de nube a dispositivo se completan siempre que la pérdida del mensaje no afecte a la lógica de aplicación. Esto podría suceder en varios escenarios diferentes. Por ejemplo, el contenido del mensaje se conservó correctamente en el almacenamiento local, o bien una operación se ejecutó correctamente o el mensaje transporta información temporal cuya pérdida no afecta a la funcionalidad de la aplicación. A veces, en el caso de tareas de ejecución prolongada, puede completar el mensaje de nube a dispositivo después de que persistió la descripción de la tarea en el almacenamiento local y, luego, notificar al back-end de aplicaciones con uno o varios mensajes de dispositivo a la nube en diferentes fases de progreso de la tarea.

#### Período de vida <a id="ttl"></a>

Cada mensaje de nube a dispositivo tiene una fecha de expiración. La puede establecer explícitamente el servicio (en la propiedad **ExpiryTimeUtc**), o bien el Centro de IoT usando el *período de vida* predeterminado especificado como una propiedad del Centro de IoT. Consulte [Opciones de configuración de nube a dispositivo](#c2dconfiguration).

> [AZURE.NOTE] Una forma habitual de aprovechar la expiración de mensajes consiste en establecer valores cortos de período de vida con el objetivo de evitar enviar mensajes a dispositivos desconectados. Así se consigue el mismo resultado que con mantener el estado de la conexión de los dispositivos, con la diferencia de que la primera opción resulta mucho más eficiente. También es posible hacer que el Centro de IoT le notifique, solicitando acuses de recibo de mensajes, de qué dispositivos pueden recibir mensajes y cuáles no se encuentran conectados o presentan errores.

#### Comentarios de mensaje <a id="feedback"></a>

Cuando envía un mensaje de nube a dispositivo, el servicio puede solicitar la entrega de los comentarios de cada mensaje en relación con el estado final de ese mensaje.

- Si establece la propiedad **Ack** en **positive**, el Centro de IoT genera un mensaje de comentarios únicamente si el mensaje de nube a dispositivo alcanza el estado **Completado**.
- Si establece la propiedad **Ack** en **negative**, el Centro de IoT genera un mensaje de comentarios únicamente si el mensaje de nube a dispositivo alcanza el estado **Deadletterd** (Procesado como devuelto).
- Al establecer la propiedad **Ack** en **full**, el Centro de IoT genera un mensaje de comentarios en cualquier caso.

> [AZURE.NOTE] Si **Ack** es **full** y no se recibe ningún mensaje de comentarios, significa que este último caducó y que el servicio no puede saber qué ocurrió con el mensaje original. En la práctica, un servicio debe asegurarse de que puede procesar el comentario antes de que expire. El tiempo de expiración máximo es de dos días, por lo tanto, debe haber tiempo suficiente para poner en funcionamiento el servicio en caso de error.

Como se explica en [Puntos de conexión](#endpoints), el Centro de IoT envía los comentarios a través de un punto de conexión orientado al servicio (**/messages/servicebound/feedback**) en forma de mensajes. La semántica de recepción de los comentarios es la misma que para los mensajes de nube a dispositivo y tienen el mismo [ciclo de vida del mensaje](#message lifecycle). Siempre que sea posible, los comentarios del mensaje se realizan por lotes en un único mensaje, con el formato siguiente.

Cada mensaje recuperado por un dispositivo desde el punto de conexión de comentarios tiene las siguientes propiedades:

| Propiedad | Descripción |
| -------- | ----------- |
| EnqueuedTime | Marca de tiempo que indica cuándo se creó el mensaje. |
| UserId | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

El cuerpo es una matriz serializada de JSON de registros, cada uno con las siguientes propiedades:

| Propiedad | Descripción |
| -------- | ----------- |
| EnqueuedTimeUtc | Marca de tiempo que indica cuándo se produjo el resultado del mensaje. Por ejemplo, el dispositivo completado o el mensaje expirado. |
| OriginalMessageId | **MessageId** del mensaje de nube a dispositivo al que pertenece esta información de comentarios. |
| StatusCode | Entero requerido. Se utiliza en los mensajes de comentarios generados por el Centro de IoT. <br/> 0 = correcto <br/> 1 = mensaje caducado <br/> 2 = número máximo de entregas superado <br/> 3 = mensaje rechazado |
| Descripción | Valores de cadena para **StatusCode**. |
| DeviceId | **DeviceId** del dispositivo de destino del mensaje de nube a dispositivo al que pertenece este elemento de comentarios. |
| DeviceGenerationId | **DeviceGenerationId** del dispositivo de destino del mensaje de nube a dispositivo al que pertenece este elemento de comentarios. |


**Importante**. El servicio tiene que especificar un **MessageId** para el mensaje de nube a dispositivo para poder correlacionar sus comentarios con el mensaje original.

**Ejemplo**. Este es un cuerpo de ejemplo de un mensaje de comentarios.

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

#### Opciones de configuración de nube a dispositivo <a id="c2dconfiguration"></a>

Cada Centro de IoT muestra las siguientes opciones de configuración para la mensajería de nube a dispositivo.

| Propiedad | Descripción | Intervalo y valor predeterminado |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | TTL predeterminado para los mensajes de nube a dispositivo. | Intervalo de ISO\_8601 hasta 2D (1 minuto como mínimo). Valor predeterminado: 1 hora. |
| maxDeliveryCount | Número máximo de entregas para las colas de nube a dispositivo por dispositivo. | De 1 a 100. Valor predeterminado: 10 |
| feedback.ttlAsIso8601 | Retención de mensajes de comentarios del límite de servicio. | Intervalo de ISO\_8601 hasta 2D (1 minuto como mínimo). Valor predeterminado: 1 hora. |
| feedback.maxDeliveryCount | Número máximo de entregas para la cola de comentarios. | De 1 a 100. Valor predeterminado: 100. |

Para obtener más información, consulte [Administración de Centros de IoT a través del portal de Azure][lnk-manage].

## Cuotas y limitación <a id="throttling"></a>

Cada suscripción de Azure puede tener como máximo 10 Centros de IoT.

Cada Centro de IoT se aprovisionó con un determinado número de unidades en una unidad de almacenamiento específico (para obtener más información, consulte [Centro de IoT Precios][lnk-pricing]). El valor de SKU y el número de unidades determinan la cuota diaria máxima de mensajes que puede enviar.

El valor de SKU también determina los valores de limitación que aplica el Centro de IoT a las operaciones.

### Limitaciones de operación

Las limitaciones de operación son las limitaciones de velocidad que se aplican en los intervalos de minutos y están diseñadas para evitar abusos. El Centro de IoT intenta evitar los errores de devolución siempre que sea posible, pero inicia la excepción de devolución si se infringe la limitación durante demasiado tiempo.

A continuación se muestra la lista de las limitaciones aplicadas. Los valores hacen referencia a un centro individual.

| Limitación | Valor por centro |
| -------- | ------------- |
| Operaciones de registro de identidad (crear, recuperar, enumerar, actualizar y eliminar) | 100/min/unidad, hasta 5.000/min |
| Conexiones de dispositivos | 120/seg/unidad (para S2), 12/seg/unidad (para S1). <br/>Mínimo de 100/s. <br/> Por ejemplo, dos unidades S1 equivalen a 2 * 12 = 24/s, pero tendrá al menos 100/s en todas las unidades. Con nueve unidades S1 tiene 108/s (9 * 12) en las unidades. |
| Envíos de dispositivo a nube | 120/seg/unidad (para S2), 12/seg/unidad (para S1). <br/>Mínimo de 100/s. <br/> Por ejemplo, dos unidades S1 equivalen a 2 * 12 = 24/s, pero tendrá al menos 100/s en todas las unidades. Con nueve unidades S1 tiene 108/s (9 * 12) en las unidades. |
| Envíos de nube a dispositivo | 100/min/unidad |
| Recepciones de nube a dispositivo | 1000/min/unidad |

Es importante aclarar que la limitación de las *conexiones de dispositivo* determina la velocidad a la que se pueden establecer nuevas conexiones de dispositivo con un Centro de IoT, no el número máximo de dispositivos conectados a la vez. La limitación depende del número de unidades aprovisionadas para el concentrador.

Por ejemplo, si compra una sola unidad S1, tendrá una limitación de 100 conexiones por segundo. Esto significa que, para conectar 100 000 dispositivos, se tarda al menos 1000 segundos (aproximadamente 16 minutos). Sin embargo, puede tener el mismo número de dispositivos conectados al mismo tiempo que de dispositivos registrados en el registro de identidad de dispositivos.

La entrada de blog [IoT Hub throttling and you][lnk-throttle-blog] (Limitación del Centro de IoT) proporciona una explicación detallada del comportamiento de la limitación del Centro de IoT.

**Nota**. En cualquier momento, es posible aumentar las cuotas o las limitaciones si aumenta el número de unidades aprovisionadas en un Centro de IoT.

**Importante**: Las operaciones de registro de identidad están diseñadas para usarse en tiempo de ejecución en escenarios de administración y aprovisionamiento de dispositivos. La lectura o actualización de un gran número de identidades de dispositivos se realiza mediante [trabajos de importación y exportación](#importexport).

## Pasos siguientes

Ahora que vio información general sobre el desarrollo del Centro de IoT, siga estos vínculos para obtener más información:

- [Introducción a los Centros de IoT (tutorial)][lnk-get-started]
- [Compatibilidad de hardware y de plataformas de sistema operativo][lnk-compatibility]
- [Centro para desarrolladores de IoT de Azure][lnk-iotdev]
- [Diseño de la solución][lnk-guidance]

[host del procesador de eventos de Centros de eventos]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx

[portal de Azure]: https://portal.azure.com

[img-endpoints]: ./media/iot-hub-devguide/endpoints.png
[img-lifecycle]: ./media/iot-hub-devguide/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide/eventhubcompatible.png

[lnk-compatibility]: iot-hub-tested-configurations.md
[lnk-apis-sdks]: iot-hub-sdks-summary.md
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx

[lnk-sas-tokens]: iot-hub-sas-tokens.md
[lnk-azure-gateway-guidance]: iot-hub-guidance.md#field-gateways
[lnk-guidance-provisioning]: iot-hub-guidance.md#provisioning
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-guidance-heartbeat]: iot-hub-guidance.md#heartbeat

[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-guidance]: iot-hub-guidance.md
[lnk-getstarted-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../resource-group-overview.md
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-createuse-sas]: ../storage-dotnet-shared-access-signature-part-2/
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-guidance-d2c-processing]: iot-hub-csharp-csharp-process-d2c.md
[lnk-management-portal]: https://portal.azure.com
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-tls]: https://tools.ietf.org/html/rfc5246
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-manage]: iot-hub-manage-through-portal.md
[lnk-mqtt-support]: iot-hub-mqtt-support.md
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/

<!---HONumber=AcomDC_0525_2016-->