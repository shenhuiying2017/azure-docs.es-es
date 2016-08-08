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
 ms.date="05/29/2016" 
 ms.author="dobett"/>

# Guía del desarrollador del Centro de IoT de Azure

Centro de IoT de Azure es un servicio totalmente administrado que ayuda a habilitar la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y un back-end de aplicación.

El Centro de IoT Azure ayuda a proporcionar:

* Comunicaciones seguras a través del uso de credenciales de seguridad para cada dispositivo y el control de acceso.
* Una mensajería confiable de gran escala de dispositivo a la nube y de nube a dispositivo.
* Conectividad fácil del dispositivo con las bibliotecas de dispositivos para las plataformas y lenguajes más populares.

En este artículo se tratan los temas siguientes:

- [Puntos de conexión](#endpoints). En esta sección se describen los diferentes puntos de conexión que expone cada Centro de IoT para las operaciones en tiempo de ejecución y de administración.
- [Registro de identidad del dispositivo](#device-identity-registry). En esta sección se describe qué información se almacena en cada registro de identidad del dispositivo de Centro de IoT y cómo se puede acceder a ella y modificarla.
- [Seguridad](#security). En esta sección se describe el modelo de seguridad que se usa para conceder acceso a la funcionalidad de Centro de IoT tanto para los dispositivos como para los componentes de nube.
- [Mensajería](#messaging). En esta sección se describen las características de mensajería (dispositivo a la nube y nube a dispositivo) que muestra Centro de IoT.
- [Cuotas y limitación](#throttling). En esta sección se resumen las cuotas que se aplican al uso del Centro de IoT.

## Puntos de conexión <a id="endpoints"></a>

Centro de IoT de Azure es un servicio multiinquilino, que muestra su funcionalidad a diversos actores. En el siguiente diagrama se muestran los diferentes puntos de conexión expuestos por Centro de IoT.

![Puntos de conexión del Centro de IoT][img-endpoints]

Esta es una descripción de los puntos de conexión:

* **Proveedor de recursos**. El proveedor de recursos de Centro de IoT muestra una interfaz de [Azure Resource Manager][lnk-arm] que permite a los propietarios de suscripciones de Azure crear y eliminar Centros de IoT, así como actualizar las propiedades de Centro de IoT. Las propiedades de Centro de IoT controlan las directivas de seguridad de nivel de centro a diferencia del control de acceso de nivel de dispositivo (consulte [Control de acceso](#accesscontrol) más adelante en este artículo) y las opciones funcionales para la mensajería de nube a dispositivo y de dispositivo a nube. El proveedor de recursos también permite [exportar identidades de dispositivo](#importexport).
* **Administración de identidades de dispositivo**. Cada Centro de IoT muestra un conjunto de puntos de conexión HTTP REST para administrar identidades de dispositivo (crear, recuperar, actualizar y eliminar). Las identidades del dispositivo se usan para autenticación de dispositivos y control de acceso. Para obtener más información, consulte [Registro de identidad del dispositivo](#device-identity-registry).
* **Puntos de conexión de dispositivo**. Para cada dispositivo aprovisionado en el registro de identidad del dispositivos, Centro de IoT muestra un conjunto de puntos de conexión que el dispositivo puede usar para enviar y recibir mensajes:
    - *Envío de mensajes de dispositivo a nube*. Use este punto de conexión para enviar mensajes de dispositivo a la nube. Para obtener más información, consulte [Mensajería de dispositivo a nube](#d2c).
    - *Recepción de mensajes de nube a dispositivo*. El dispositivo usa ese punto de conexión para recibir mensajes de nube a dispositivos dirigidos. Para obtener más información, consulte [Mensajería de nube a dispositivo](#c2d).
    - *Iniciar cargas de archivos*. Un dispositivo usa este punto de conexión para recibir un URI de SAS de Almacenamiento de Azure del Centro de IoT para cargar un archivo. Para más información, consulte [Cargas de archivos](#fileupload).

    Estos puntos de conexión se exponen mediante los protocolos HTTP 1.1, [MQTT v3.1.1][lnk-mqtt] y [AMQP 1.0][lnk-amqp]. Tenga en cuenta que AMQP también está disponible sobre [WebSockets][lnk-websockets] en el puerto 443.
* **Puntos de conexión de servicio**. Cada Centro de IoT muestra un conjunto de puntos de conexión que el back-end de aplicaciones puede usar para comunicarse con los dispositivos. Estos extremos se exponen actualmente usando solo el protocolo [AMQP][lnk-amqp].
    - *Recepción de mensajes de dispositivo a nube*. Este punto de conexión es compatible con los [Centros de eventos de Azure][lnk-event-hubs]. Un servicio back-end puede usarse para leer todos los mensajes de dispositivo a nube enviados por los dispositivos. Para obtener más información, consulte [Mensajería de dispositivo a nube](#d2c).
    - *Envío de mensajes de nube a dispositivo y recepción de confirmaciones de entrega*. Estos puntos de conexión permiten al back-end de aplicaciones enviar mensajes confiables de nube a dispositivo y recibir las confirmaciones de entrega o expiración correspondientes. Para obtener más información, consulte [Mensajería de nube a dispositivo](#c2d).
    - *Recepción de notificaciones de archivos*. Este punto de conexión de mensajería le permite recibir notificaciones del momento en que los dispositivos cargan correctamente un archivo.

En el artículo [SDK de Centro de IoT][lnk-sdks] se describen las distintas formas con las que se puede acceder a estos puntos de conexión.

Finalmente, es importante tener en cuenta que todos los puntos de conexión del Centro de IoT usan el protocolo [TLS][lnk-tls] y ningún punto de conexión se expone en canales sin cifrar o no seguros.

### Cómo interpretar los extremos compatibles con los centros de eventos. <a id="eventhubcompatible"></a>

Cuando se usa el [SDK de Bus de servicio de Azure para .NET][lnk-servicebus-sdk] o el [host del procesador de eventos de Centros de eventos][lnk-eventprocessorhost], puede utilizar cualquier cadena de conexión del Centro de IoT con los permisos correctos y después emplear **messages/events** como nombre del Centro de eventos.

Cuando use SDK (o integraciones de productos) que no detectan el Centro de IoT, tiene que recuperar un punto de conexión y un nombre de centro de eventos que sean compatibles de la configuración del Centro de IoT en el [Portal de Azure][lnk-management-portal]\:

1. En la hoja Centro de IoT, haga clic en **Configuración** > **Mensajería**.
2. En la sección **Device-to-cloud settings** (Configuración de dispositivo a nube), encontrará los valores **Event Hub-compatible endpoint** (Punto de conexión compatible con Centro de eventos), **Event Hub-compatible name** (Nombre compatible con Centro de eventos) y **Particiones**.

    ![Configuración de dispositivo a nube][img-eventhubcompatible]

> [AZURE.NOTE] Si el SDK requiere un valor de **Nombre de host** o **Espacio de nombres**, quite el esquema del **punto de conexión compatible con Centro de eventos**. Por ejemplo, si el punto de conexión compatible con el Centro de eventos es **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, el** nombre de host** sería **iothub-ns-myiothub-1234.servicebus.windows.net** y el ****espacio de nombres** iothub-ns-myiothub-1234**.

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

Cada Centro de IoT contiene un registro de identidad de dispositivo. Puede utilizar este registro para crear recursos por dispositivo en el servicio, como una cola que contenga mensajes en curso de nube a dispositivo. También puede usar el registro para permitir el acceso a los puntos de conexión accesibles desde el dispositivo, como se explica en la sección [Control de acceso](#accesscontrol).

En un nivel superior, el registro de identidad del dispositivo es una colección de recursos de identidad de dispositivos compatible con REST. Las secciones siguientes detallan las propiedades de los recursos de identidad del dispositivo y las operaciones que el registro permite en las identidades.

> [AZURE.NOTE] Consulte [SDK de Centro de IoT][lnk-sdks] para más información sobre el protocolo HTTP y los SDK que puede usar para interactuar con el registro de identidad de dispositivo.

### Propiedades de identidad del dispositivo <a id="deviceproperties"></a>

Las identidades de dispositivos se representan como documentos JSON con las propiedades siguientes.

| Propiedad | Opciones | Descripción |
| -------- | ------- | ----------- |
| deviceId | necesarias, de solo lectura en actualizaciones | Una cadena que distingue mayúsculas y minúsculas (de hasta 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| generationId | requerido, de solo lectura | Una cadena de hasta 128 caracteres que distingue mayúsculas y minúsculas generada por el centro. Se usa para distinguir dispositivos con el mismo **deviceId** cuando se han eliminado y vuelto a crear. |
| ETag | requerido, de solo lectura | Una cadena que representa un valor de etag débil para la identidad del dispositivo, como por [RFC7232][lnk-rfc7232].|
| auth | opcional | Un objeto compuesto que contiene material de seguridad e información de autenticación. |
| auth.symkey | opcional | Un objeto compuesto que contiene una clave principal y una secundaria, almacenadas en formato base64. |
| status | requerido | Indicador de acceso Puede ser **Habilitado** o **Deshabilitado**. Si está **habilitado**, el dispositivo se puede conectar. Si está **deshabilitado**, este dispositivo no puede acceder a ningún extremo accesible desde el dispositivo. |
| statusReason | opcional | Una cadena de 128 caracteres que almacena el motivo del estado de identidad del dispositivo. Se permiten todos los caracteres UTF-8. |
| statusUpdateTime | solo lectura | Un indicador temporal, que muestra la fecha y hora de la última actualización de estado. |
| connectionState | solo lectura | Un campo que indica el estado de la conexión: **Conectado** o **Desconectado**. Este campo representa la vista de Centro de IoT del estado de conexión del dispositivo. **Importante**: Este campo debe usarse solo para fines de desarrollo o depuración. El estado de conexión se actualiza solo para dispositivos que usen AMQP o MQTT. Además, se basa en pings de nivel de protocolo (pings MQTT o pings AMQP) y puede tener un retraso de 5 minutos como máximo. Por estos motivos es posible que haya falsos positivos, como dispositivos que se notifican como conectados pero que en realidad están desconectados. |
| connectionStateUpdatedTime | solo lectura | Un indicador temporal que muestra la fecha y hora de la última vez que se actualizó el estado de conexión. |
| lastActivityTime | solo lectura | Un indicador temporal que muestra la fecha y hora de la última vez que el dispositivo se conectó, recibió o envió un mensaje. |

> [AZURE.NOTE] El estado de la conexión solo puede representar la vista del Centro de IoT del estado de la conexión. Las actualizaciones de este estado se pueden retrasar, dependiendo de las configuraciones y las condiciones de red.

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

> [AZURE.IMPORTANT] Solo debe utilizar el registro de identidad del dispositivo para las operaciones de aprovisionamiento y administración de dispositivos. Las operaciones de alto rendimiento en tiempo de ejecución no deben depender de la realización de operaciones en el registro de identidad del dispositivo. Por ejemplo, comprobar el estado de conexión de un dispositivo antes de enviar un comando no es un modelo compatible. Asegúrese de comprobar las [tasas de limitación](#throttling) para el registro de identidad de dispositivo y el patrón del [latido de dispositivo][lnk-guidance-heartbeat].

### Deshabilitación de dispositivos

Puede deshabilitar los dispositivos actualizando la propiedad **status** de una identidad en el registro. Normalmente se usa en dos escenarios:

- Durante el proceso de orquestación de aprovisionamiento. Para más información, consulte [Diseño de la solución - Aprovisionamiento de dispositivos][lnk-guidance-provisioning].
- Si por cualquier motivo considera que un dispositivo está en peligro o no está autorizado.

### Importación y exportación de identidades de dispositivo <a id="importexport"></a>

Puede exportar identidades de dispositivo de forma masiva desde el registro de identidad de un Centro de IoT mediante operaciones asincrónicas en el [punto de conexión del proveedor de recursos de Centro de IoT](#endpoints). Las exportaciones son trabajos de ejecución prolongada que usan un contenedor de blobs proporcionado por el cliente para guardar los datos de identidad del dispositivo que se leen en el registro de identidad.

Puede importar identidades de dispositivo en bloque a un registro de identidad del Centro de IoT mediante operaciones asincrónicas en el [punto de conexión del proveedor de recursos de Centro de IoT](#endpoints). Las importaciones son trabajos de ejecución prolongada que usan los datos de un contenedor de blobs proporcionado por el cliente para escribir datos de identidad del dispositivo en el registro de identidad del dispositivo.

- Para obtener información detallada sobre la importación y exportación de API, consulte [Centro de IoT de Azure: API de proveedor de recursos][lnk-resource-provider-apis].
- Para más información sobre la ejecución de trabajos de importación y exportación, consulte [Administración de identidades de dispositivos de Centro de IoT de forma masiva][lnk-bulk-identity].

## Seguridad <a id="security"></a>

En esta sección se describen las opciones para proteger el Centro de IoT de Azure.

### Control de acceso <a id="accesscontrol"></a>

El Centro de IoT usa el siguiente conjunto de *permisos* para conceder acceso a cada uno de los puntos de conexión del Centro de IoT. Los permisos limitan el acceso a un Centro de IoT basado en la funcionalidad.

* **RegistryRead**. Concede acceso de lectura al registro de identidad del dispositivo. Para obtener más información, consulte [Registro de identidad del dispositivo](#device-identity-registry).
* **RegistryReadWrite**. Concede acceso de lectura y escritura al registro de identidad del dispositivo. Para obtener más información, consulte [Registro de identidad del dispositivo](#device-identity-registry).
* **ServiceConnect**. Concede acceso a los puntos de conexión de comunicación y supervisión accesibles desde el servicio en la nube. Por ejemplo, concede permiso a los servicios en la nube de back-end para recibir mensajes de dispositivo a la nube, enviar mensajes de nube a dispositivo y recuperar las confirmaciones de entrega correspondientes.
* **DeviceConnect**. Concede acceso a los puntos de conexión de comunicación accesibles desde los dispositivos. Por ejemplo, concede permiso para enviar mensajes de dispositivo a nube y recibir mensajes de nube a dispositivo. Este permiso lo usan los dispositivos.

Puede conceder los permisos de las maneras siguientes:

* **Directivas de acceso compartido de nivel de centro**. Las directivas de acceso compartido pueden conceder cualquier combinación de los permisos enumerados anteriormente. Puede definir las directivas en el [Portal de Azure][lnk-management-portal] o mediante programación usando las [API del proveedor de recursos de Centro de IoT de Azure][lnk-resource-provider-apis]. Un Centro de IoT recién creado tiene las siguientes directivas predeterminadas:

    - **iothubowner**: directiva con todos los permisos.
    - **service**: directiva con el permiso ServiceConnect.
    - **device**: directiva con el permiso DeviceConnect.
    - **registryRead**: directiva con el permiso RegistryRead.
    - **registryReadWrite**: directiva con los permisos RegistryRead y RegistryWrite.


* **Credenciales de seguridad de cada dispositivo**. Cada Centro de IoT contiene un [registro de identidad del dispositivo](#device-identity-registry). Para cada dispositivo de este registro, puede configurar credenciales de seguridad que concedan permisos **DeviceConnect** orientados a los extremos del dispositivo correspondiente.

Por ejemplo en una solución típica de IoT:
- El componente de administración de dispositivos usa la directiva *registryReadWrite*.
- El componente de procesador de eventos usa la directiva *service*.
- El componente de lógica empresarial de dispositivos en tiempo de ejecución usa la directiva *service*.
- Los dispositivos individuales se conectan usando las credenciales almacenadas en el registro de identidad del Centro de IoT.

Para más información sobre temas de seguridad del Centro de IoT, consulte la sección sobre seguridad en [Diseño de la solución][lnk-guidance-security].

### Autenticación

El Centro de IoT de Azure concede acceso a los puntos de conexión mediante la comprobación de un token con las directivas de acceso compartido y las credenciales de seguridad del registro de identidad del dispositivo.

Las credenciales de seguridad, como las claves simétricas, nunca se envían en la conexión.

> [AZURE.NOTE] El proveedor de recursos del Centro de IoT de Azure se protege mediante la suscripción de Azure, igual que todos los proveedores en el [Administrador de recursos de Azure][lnk-azure-resource-manager].

Consulte el artículo [Uso de tokens de seguridad de Centro del IoT][lnk-sas-tokens] para más información sobre cómo crear y utilizar tokens de seguridad.

#### Detalles específicos de protocolo

Cada uno de los protocolos admitidos, como AMQP, MQTT y HTTP, transporta tokens de diferentes maneras.


HTTP implementa la autenticación mediante la inclusión de un token válido en el encabezado de solicitud **Authorization**.


Al usar [AMQP][lnk-amqp], el Centro de IoT admite [SASL PLAIN][lnk-sasl-plain] y [seguridad basada en notificaciones AMQP][lnk-cbs].

En el caso de la seguridad basada en notificaciones AMQP, la norma especifica cómo transmitir estos tokens.

Para SASL PLAIN, el **nombre de usuario** puede ser:

* `{policyName}@sas.root.{iothubName}` en el caso de los tokens de nivel de centro.
* `{deviceId}` en el caso de los tokens con ámbito de dispositivo.

En ambos casos, el campo de contraseña contiene el token, como se describe en el artículo [Uso de tokens de seguridad del Centro de IoT][lnk-sas-tokens].

Al utilizar MQTT, el paquete CONNECT tiene deviceId como ClientId, {iothubhostname}/{deviceId} en el campo Nombre de usuario y un token SAS en el campo Contraseña. {iothubhostname} debe ser el CName completo de Centro de IoT (por ejemplo, contoso.azure-devices.net).

##### Ejemplo: #####

Nombre de usuario (DeviceId distingue entre mayúsculas y minúsculas): `iothubname.azure-devices.net/DeviceId`

Contraseña (Generar SAS con el Explorador de dispositivos): `SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [AZURE.NOTE] Los [SDK del Centro de IoT de Azure][lnk-sdks] generan tokens automáticamente cuando se conectan al servicio. En algunos casos, los SDK no admiten todos los protocolos o todos los métodos de autenticación.

#### Consideraciones especiales para SASL PLAIN

Cuando se usa SASL PLAIN, un cliente que se conecta a un Centro de IoT puede usar un token único para cada conexión TCP. Cuando el token expira, la conexión TCP se desconecta del servicio y desencadena una reconexión. Este comportamiento, aunque no resulta problemático para un componente de back-end de aplicaciones, es muy perjudicial para una aplicación de dispositivo por los siguientes motivos:

*  Las puertas de enlace normalmente se conectan en nombre de muchos dispositivos. Cuando se usa SASL PLAIN, tienen que crear una conexión TCP distintiva para cada dispositivo que se conecta a un Centro de IoT. Esto aumenta considerablemente el consumo de energía y de recursos de red, y aumenta la latencia de cada conexión de dispositivo.
* Los dispositivos con recursos restringidos se ven afectados negativamente por el aumento del uso de recursos para volver a conectarse después de cada expiración del token.

### Restricción de las credenciales de nivel de centro

Puede restringir el ámbito de las directivas de seguridad de nivel de centro mediante la creación de tokens con un URI de recurso restringido. Por ejemplo, el punto de conexión para enviar mensajes de dispositivo a nube desde un dispositivo es **/devices/{deviceId}/messages/events**. También puede usar una directiva de acceso compartido de nivel de centro con permisos **DeviceConnect** para firmar un token cuyo resourceURI sea **/devices/{deviceId}**. Con ello se crea un token que solo se puede usar para enviar dispositivos en nombre del **deviceId** del dispositivo.

Se trata de un mecanismo similar a la [directiva de editor de Centros de eventos][lnk-event-hubs-publisher-policy], que permite que se implementen métodos de autenticación personalizados. Para más información, consulte la sección de seguridad de [Diseño de la solución][lnk-guidance-security].

## Mensajería

El Centro de IoT proporciona primitivas de mensajería para comunicarse:

- [Nube a dispositivo](#c2d): desde un back-end de aplicación (*servicio* o *nube*).
- [Dispositivo a nube](#d2c): desde un dispositivo hasta un back-end de aplicación.
- [Cargas de archivos](#fileupload) desde un dispositivo hasta una cuenta de almacenamiento de Azure asociada.

Las propiedades básicas de la funcionalidad de mensajería del Centro de IoT son la confiabilidad y durabilidad de los mensajes. Esto permite la resistencia a la conectividad intermitente en el dispositivo y a los picos de carga del procesamiento de eventos en la nube. El Centro de IoT implementa *al menos una vez* garantías de entrega para la mensajería del dispositivo a la nube y de la nube al dispositivo.

Centro de IoT admite varios protocolos accesibles desde los dispositivos (por ejemplo, MQTT, AMQP y HTTP). Con el fin de admitir la interoperabilidad sin problemas entre protocolos, Centro de IoT define un formato de mensaje común que es compatible con todos los protocolos accesibles desde el dispositivo.

### Formato de mensajes <a id="messageformat"></a>

Los mensajes del Centro de IoT constan de:

* Un conjunto de *propiedades del sistema*. Estas son propiedades que interpreta o establece Centro de IoT. Este conjunto es el predeterminado.
* Un conjunto de *propiedades de la aplicación*. Se trata de un diccionario de propiedades de cadena que la aplicación puede definir y acceder sin necesidad de deserializar el cuerpo del mensaje. Centro de IoT nunca modifica estas propiedades.
* Un cuerpo binario opaco.

Para más información sobre cómo se codifica el mensaje en distintos protocolos, consulte [SDK de Centro de IoT][lnk-sdks].

Es el conjunto de propiedades del sistema en los mensajes del Centro de IoT.

| Propiedad | Descripción |
| -------- | ----------- |
| MessageId | Un identificador configurable por el usuario para el mensaje, que normalmente se usa para patrones de solicitud y respuesta. Formato: una cadena que distingue mayúsculas y minúsculas (de hasta 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Número de secuencia | Un número (exclusivo para cada cola de dispositivo) asignado por Centro de IoT a cada mensaje de nube a dispositivo. |
| Para | Un destino especificado en los mensajes [de nube a dispositivo](#c2d). |
| ExpiryTimeUtc | Fecha y hora de la expiración del mensaje. |
| EnqueuedTime | Fecha y hora en la que el Centro de IoT recibió el mensaje. |
| CorrelationId | Cadena de propiedad en un mensaje de respuesta que normalmente contiene el identificador del mensaje de la solicitud en los patrones de solicitud y respuesta. |
| UserId | Un identificador que se utiliza para especificar el origen de los mensajes. Cuando el Centro de IoT genera mensajes, se establece en `{iot hub name}`. |
| Ack | Un generador de mensajes de comentarios. Esta propiedad se usa en los mensajes de nube a dispositivo para solicitar a Centro de IoT que genere mensajes de comentarios debido al consumo del mensaje por el dispositivo. Valores posibles: **none** (valor predeterminado): no se genera ningún mensaje de comentarios, **positive**: recibe un mensaje de comentarios si el mensaje se completó, **negative**: recibe un mensaje de comentarios si el mensaje expiró (o se alcanzó el número máximo de entregas) sin que se complete en el dispositivo y **full**: comentarios positivos y negativos. Para obtener más información, consulte [Comentarios de mensajes](#feedback). |
| ConnectionDeviceId | Un identificador establecido por Centro de IoT en los mensajes de dispositivo a nube. Contiene el **deviceId** del dispositivo que envió el mensaje. |
| ConnectionDeviceGenerationId | Un identificador establecido por Centro de IoT en los mensajes de dispositivo a nube. Contiene el **generationId** (como se indica en [Propiedades de identidad de dispositivos](#deviceproperties)) del dispositivo que envió el mensaje. |
| ConnectionAuthMethod | Un método de autenticación establecido por Centro de IoT en los mensajes de dispositivo a nube. Esta propiedad contiene información sobre el método de autenticación usado para autenticar el dispositivo que envía el mensaje. Para más información, consulte la sección [Propiedades contra la suplantación](#antispoofing).|

### Elección del protocolo de comunicación <a id="amqpvshttp"></a>

El Centro de IoT admite los protocolos [AMQP][lnk-amqp], AMQP sobre WebSockets, MQTT y HTTP/1 para las comunicaciones del dispositivo. Tenga en cuenta lo siguiente con respecto a sus usos.

* **Patrón de nube a dispositivo**. HTTP/1 no cuenta con una forma eficaz de implementar la inserción de servidor. Por lo tanto, cuando se usa HTTP/1, los dispositivos sondean los mensajes de nube a dispositivo en Centro de IoT. Esto resulta muy ineficaz tanto para el dispositivo como para el Centro de IoT. En las directrices actuales de HTTP/1, cada dispositivo sondea cada 25 minutos o más. Por otro lado, AMQP y MQTT admiten inserción de servidor al recibir mensajes de nube a dispositivo. Permiten inserciones inmediatas de mensajes desde Centro de IoT en el dispositivo. Si le preocupa la latencia de entrega, es mucho mejor usar el protocolo AMQP o MQTT. Para dispositivos conectados en raras ocasiones, HTTP/1 funciona bien.
* **Puertas de enlace de campo**. Cuando se utiliza HTTP/1 y MQTT, no puede conectar varios dispositivos (cada uno con sus propias credenciales por dispositivo) con la misma conexión TLS. Por lo tanto, en los [escenarios de puerta de enlace de campo][lnk-azure-gateway-guidance] estos protocolos no son óptimos, ya que requieren una conexión TLS entre dicha puerta de enlace y el Centro de IoT para cada dispositivo conectado a aquella.
* **Dispositivos con bajos recursos**. Las bibliotecas MQTT y HTTP/1 tienen una superficie menor que las bibliotecas AMQP. Por ello, si el dispositivo tiene pocos recursos (por ejemplo, menos de 1 MB de RAM), estos protocolos pueden ser la única implementación de protocolo disponible.
* **Cruce seguro de red**. El estándar MQTT escucha en el puerto 8883. Esto podría producir problemas en las redes que están cerradas para los protocolos que no son HTTP. HTTP y AMQP (sobre WebSockets) están disponibles para usarse en este escenario.
* **Tamaño de carga**. AMQP y MQTT son protocolos binarios, que son mucho más compactos que HTTP/1.

En general, debe usar AMQP (o AMQP sobre WebSockets) siempre que sea posible y utilizar solo MQTT cuando las restricciones de recursos impidan el uso de AMQP. HTTP/1 debe usarse solo si el recorrido por la red y la configuración de red impiden el uso de MQTT y AMQP. Además, cuando se utiliza HTTP/1, cada dispositivo debe sondear para ver si hay mensajes de la nube a dispositivo cada 25 minutos o más.

> [AZURE.NOTE] Durante el desarrollo, es aceptable sondear con una frecuencia mayor de 25 minutos.

<a id="mqtt-support">
#### Notas sobre la compatibilidad con MQTT
Centro de IoT implementa el protocolo MQTT v3.1.1 con las siguientes limitaciones y comportamiento específico:

  * **No se admite QoS 2**. Cuando un cliente de dispositivo publica un mensaje con **QoS 2**, el Centro de IoT cierra la conexión de red. Cuando un cliente de dispositivo se suscribe a un tema con **QoS 2**, el Centro de IoT concede el QoS de nivel 1 (el máximo) en el paquete **SUBACK**.
  * **Los mensajes Retain no se conservan**. Si un cliente de dispositivo publica un mensaje con la marca RETAIN establecida en 1, el Centro de IoT agrega la propiedad de aplicación **x-opt-retain** al mensaje. Esto significa que el Centro de IoT no conserva el mensaje, sino que lo pasa a la aplicación de back-end.

Para más información, consulte [Compatibilidad con MQTT del Centro de IoT][lnk-mqtt-support].

Como consideración final, debe revisar la [puerta de enlace de protocolo de Azure IoT][lnk-azure-protocol-gateway]. Esto le permite implementar una puerta de enlace de protocolo personalizado de alto rendimiento que interactúa directamente con Centro de IoT. La puerta de enlace de protocolos de IoT de Azure le permite personalizar el protocolo del dispositivo para dar cabida a las implementaciones de MQTT de Brownfield u otros protocolos personalizados. De todas formas, este enfoque requiere autohospedar y operar una puerta de enlace de protocolo personalizado.

### Dispositivo a nube <a id="d2c"></a>

Tal como se detalla en la sección [Puntos de conexión](#endpoints), se envían mensajes de dispositivo a nube a través de un punto de conexión accesible desde el dispositivo (**/devices/{deviceId}/messages/events**). Los mensajes se reciben a través de un punto de conexión accesible desde el servicio (**/messages/events**) que es compatible con los [Centros de eventos][lnk-event-hubs]. Por lo tanto, puede usar la integración de los Centros de eventos estándar y los SDK para recibir mensajes de dispositivo a la nube.

El Centro de IoT implementa mensajería de dispositivo a nube de forma similar a los [Centros de eventos][lnk-event-hubs]. Los mensajes de dispositivo a nube del Centro de IoT son más parecidos a los *eventos* de los Centros de eventos que a los *mensajes* de [Bus de servicio][lnk-servicebus].

Esta implementación tiene las implicaciones siguientes:

* Como sucede con los eventos de Centros de eventos, los mensajes de dispositivo a nube son duraderos y se conservan en un Centro de IoT hasta siete días (consulte [Opciones de configuración de dispositivo a nube](#d2cconfiguration)).
* Los mensajes de dispositivo a nube se fragmentan en un conjunto fijo de particiones que se establece en el momento de la creación (consulte [Opciones de configuración de dispositivo a nube](#d2cconfiguration)).
* De igual forma que los centros de eventos, los clientes que leen mensajes de dispositivo a nube tienen que administrar particiones y puntos de control. Consulte [Centros de eventos: consumo de eventos][lnk-event-hubs-consuming-events].
* Al igual que los eventos de los Centros de eventos, los mensajes de dispositivo a nube pueden tener como máximo 256 KB y se pueden agrupar en lotes para optimizar los envíos. Los lotes pueden tener un tamaño máximo de 256 KB y como máximo 500 mensajes.

De todas formas, hay algunas diferencias importantes entre los mensajes de dispositivo a nube de Centro de IoT y los Centros de eventos:

* Como se explica en la sección [Seguridad](#security), el Centro de IoT permite la autenticación y el control de acceso por dispositivo.
* El Centro de IoT permite millones de dispositivos conectados simultáneamente (consulte [Cuotas y limitación](#throttling)), mientras que los centros de eventos se limitan a 5000 conexiones AMQP por espacio de nombres.
* El Centro de IoT no permite el particionamiento arbitrario mediante una **PartitionKey**. Los mensajes de dispositivo a nube se dividen en particiones en función de su valor de **deviceId** de origen.
* El escalado de un Centro de IoT es ligeramente diferente que el de los Centros de eventos. Para obtener más información, consulte [Escalado de un Centro de IoT][lnk-guidance-scale].

Tenga en cuenta que esto no significa que puede sustituir Centro de IoT en Centros de eventos en todas las situaciones. Por ejemplo, en algunos cálculos de procesamiento de eventos, podría ser necesario volver a crear particiones de eventos con respecto a un campo o propiedad diferentes antes de analizar los flujos de datos. En esta situación, puede usar un Centro de eventos para desacoplar las dos partes de la canalización de procesamiento de la transmisión. Para más información, consulte la sección *Particiones* del artículo [Información general de los Centros de eventos de Azure][lnk-eventhub-partitions].

Para obtener información detallada sobre cómo usar la mensajería de dispositivo a nube, consulte [API y SDK del Centro de IoT][lnk-sdks].

> [AZURE.NOTE] Cuando se usa HTTP para enviar mensajes de dispositivo a nube, los valores y los nombres de propiedad solo pueden contener caracteres alfanuméricos ASCII y ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

#### Tráfico sin telemetría

En muchos casos, además de los puntos de datos de telemetría, los dispositivos también envían mensajes y solicitudes que requieren la ejecución y el control de la capa de lógica de negocio de la aplicación. Por ejemplo, alertas críticas que deben desencadenar una acción específica en el back-end o respuestas de dispositivo a los comandos enviados desde el back-end.

Consulte [Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT][lnk-guidance-d2c-processing] para más información sobre la mejor manera de procesar este tipo de mensajes.

#### Opciones de configuración de dispositivo a nube <a id="d2cconfiguration"></a>

Centro de IoT muestra las propiedades siguientes para permitirle controlar la mensajería de dispositivo a la nube.

* **Número de particiones**. Establezca esta propiedad durante la creación para definir el número de particiones para ingesta de eventos de dispositivo a la nube.
* **Tiempo de retención**. Esta propiedad especifica el tiempo de retención para los mensajes de dispositivo a nube. El valor predeterminado es un día, pero se puede aumentar a siete días.

Además, de forma análoga a los Centros de eventos, el Centro de IoT permite administrar los grupos de consumidores en el punto de conexión de recepción de dispositivo a nube.

Puede modificar todas estas propiedades mediante programación con las [API del proveedor de recursos del Centro de IoT de Azure][lnk-resource-provider-apis] o usando el [Portal de Azure][lnk-management-portal].

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

Tal como se detalla en la sección [Puntos de conexión](#endpoints), puede enviar mensajes de nube a dispositivo mediante un punto de conexión accesible desde el servicio (**/messages/devicebound**). Un dispositivo puede recibirlos mediante un punto de conexión específico del dispositivo (**/devices/{deviceId}/messages/devicebound**).

Cada mensaje de nube a dispositivo se destina a un único dispositivo estableciendo la propiedad **to** en **/devices/{deviceId}/messages/devicebound**.

>[AZURE.IMPORTANT] Cada cola de dispositivos puede contener como máximo 50 mensajes de nube a dispositivo. Si se intenta enviar más mensajes al mismo dispositivo, se producirá un error.

> [AZURE.NOTE] Al enviar mensajes de nube a dispositivo, los valores y los nombres de propiedad solo pueden contener caracteres alfanuméricos ASCII y ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

#### Ciclo de vida de los mensajes <a id="message lifecycle"></a>

Para implementar la garantía de entrega de mensajes por lo menos una vez, los mensajes de nube a dispositivo se almacenan en colas por dispositivo. Los dispositivos tienen que confirmar explícitamente la *finalización* para que el Centro de IoT los quite de la cola. Esto garantiza la resistencia frente a errores de dispositivo y de conectividad.

En el diagrama siguiente se detalla el gráfico de estado del ciclo de vida de un mensaje de nube a dispositivo.

![Ciclo de vida de los mensajes de nube a dispositivo][img-lifecycle]

Cuando el servicio envía un mensaje, se considera que está *Enqueued* (En cola). Cuando un dispositivo quiere *recibir* un mensaje, el Centro de IoT *bloquea* dicho mensaje (establece el estado en **Invisible**) para permitir que otros subprocesos del mismo dispositivo empiecen a recibir otros mensajes. Cuando el subproceso de un dispositivo termina de procesar un mensaje, informa al Centro de IoT *finalizando* dicho mensaje.

Un dispositivo también puede:

- *Rechazar* el mensaje, lo que hace que el Centro de IoT lo establezca en estado **Deadlettered** (Procesado como devuelto).
- *Abandonar* el mensaje, lo que hace que el Centro de IoT vuelva a ponerlo en la cola con el estado **Enqueued** (En cola).

Podría producirse un error en el subproceso al procesar un mensaje sin notificar a Centro de IoT. En este caso, los mensajes pasan automáticamente del estado **Invisible** al estado **Enqueued** (En cola) después de un *tiempo de espera de visibilidad (o bloqueo)*. El valor predeterminado de este tiempo de espera es un minuto.

Un mensaje puede cambiar entre los estados **Enqueued** (En cola) e **Invisible** un número de veces especificado en la propiedad **Número máximo de entregas** en el Centro de IoT. Después de ese número de transiciones, el Centro de IoT establece el estado del mensaje en **Deadlettered** (Procesado como devuelto). De igual forma, el Centro de IoT establece el estado de un mensaje en **Deadlettered** (Procesado como devuelto) después de su fecha de caducidad (consulte [Período de vida](#ttl)).

Para ver un tutorial sobre los mensajes de nube a dispositivo, consulte [Introducción a los mensajes de nube a dispositivo del Centro de IoT de Azure][lnk-getstarted-c2d-tutorial]. Para consultar temas de referencia sobre cómo las diferentes API y SDK exponen la funcionalidad de dispositivo de nube, vea [API y SDK del Centro de IoT][lnk-sdks].

> [AZURE.NOTE] Normalmente los mensajes de nube a dispositivo se completan siempre que la pérdida del mensaje no afecte a la lógica de aplicación. Por ejemplo, el contenido del mensaje se ha guardado correctamente en el almacenamiento local, o se ha ejecutado correctamente una operación. El mensaje también puede llevar información transitoria, cuya pérdida no afectaría a la funcionalidad de la aplicación. A veces, para tareas de larga duración, puede completar el mensaje de nube a dispositivo después de guardar la descripción de la tarea en el almacenamiento local. A continuación, se puede notificar al back-end de aplicación con uno o más mensajes de dispositivo a nube en distintas fases de progreso de la tarea.

#### Expiración de mensajes (periodo de vida) <a id="ttl"></a>

Cada mensaje de nube a dispositivo tiene una fecha de expiración. La puede establecer explícitamente el servicio (en la propiedad **ExpiryTimeUtc**), o bien el Centro de IoT usando el *período de vida* predeterminado especificado como una propiedad del Centro de IoT. Consulte [Opciones de configuración de nube a dispositivo](#c2dconfiguration).

> [AZURE.NOTE] Una forma habitual de aprovechar la expiración de mensajes consiste en establecer valores cortos de período de vida con el objetivo de evitar enviar mensajes a dispositivos desconectados. Así se consigue el mismo resultado que con mantener el estado de la conexión de los dispositivos, con la diferencia de que la primera opción resulta mucho más eficiente. Solicitando acuses de recibo de mensajes, Centro de IoT le notificará cuáles son los dispositivos que pueden recibir mensajes y cuáles no se encuentran conectados o presentan errores.

#### Comentarios de mensaje <a id="feedback"></a>

Cuando envía un mensaje de nube a dispositivo, el servicio puede solicitar la entrega de los comentarios de cada mensaje en relación con el estado final de ese mensaje.

- Si establece la propiedad **Ack** en **positive**, el Centro de IoT genera un mensaje de comentarios únicamente si el mensaje de nube a dispositivo alcanza el estado **Completado**.
- Si establece la propiedad **Ack** en **negative**, el Centro de IoT genera un mensaje de comentarios únicamente si el mensaje de nube a dispositivo alcanza el estado **Deadletterd** (Procesado como devuelto).
- Al establecer la propiedad **Ack** en **full**, el Centro de IoT genera un mensaje de comentarios en cualquier caso.

> [AZURE.NOTE] Si **Ack** es **full**, y no recibe un mensaje de comentarios, significa que el mensaje de comentarios ha expirado. El servicio no puede saber qué ha ocurrido con el mensaje original. En la práctica, un servicio debe asegurarse de que puede procesar el comentario antes de que expire. El tiempo de expiración máximo es de dos días, lo que permite tener tiempo suficiente para poner de nuevo en funcionamiento el servicio en caso de error.

Como se explica en [Puntos de conexión](#endpoints), el Centro de IoT envía los comentarios a través de un punto de conexión accesible desde el servicio (**/messages/servicebound/feedback**) en forma de mensajes. La semántica de recepción de los comentarios es la misma que para los mensajes de nube a dispositivo y tienen el mismo [ciclo de vida del mensaje](#message lifecycle). Siempre que sea posible, los comentarios del mensaje se realizan por lotes en un único mensaje, con el formato siguiente.

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


>[AZURE.IMPORTANT] El servicio tiene que especificar un valor de **MessageId** para el mensaje de nube a dispositivo para poder correlacionar sus comentarios con el mensaje original.

En el ejemplo siguiente se muestra el cuerpo de un mensaje de comentarios.

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

Cada Centro de IoT expone las siguientes opciones de configuración para la mensajería de nube a dispositivo.

| Propiedad | Descripción | Intervalo y valor predeterminado |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | TTL predeterminado para los mensajes de nube a dispositivo. | Intervalo de ISO\_8601 hasta 2D (1 minuto como mínimo). Valor predeterminado: 1 hora. |
| maxDeliveryCount | Número máximo de entregas para las colas de nube a dispositivo por dispositivo. | De 1 a 100. Valor predeterminado: 10 |
| feedback.ttlAsIso8601 | Retención de mensajes de comentarios del límite de servicio. | Intervalo de ISO\_8601 hasta 2D (1 minuto como mínimo). Valor predeterminado: 1 hora. |
| feedback.maxDeliveryCount | Número máximo de entregas para la cola de comentarios. | De 1 a 100. Valor predeterminado: 100. |

Para más información, consulte [Administración de Centros de IoT a través del Portal de Azure][lnk-portal].

### Cargas de archivos <a id="fileupload"></a>

Tal como se detalla en la sección [Puntos de conexión](#endpoints), los dispositivos pueden iniciar cargas de archivos mediante el envío de una notificación a través de un punto de conexión accesible desde el dispositivo (**/devices/{deviceId}/files**). Cuando un dispositivo notifica al Centro de IoT que se ha completado una carga, el Centro de IoT genera notificaciones de carga de archivos que se pueden recibir a través de un punto de conexión accesible desde el servicio (**/messages/servicebound/filenotifications**) en forma de mensajes.

En lugar de servir de intermediario de los mensajes, el Centro de IoT actúa como distribuidor a una cuenta de Almacenamiento de Azure asociada. Un dispositivo solicita un token de almacenamiento del Centro de IoT que es específico del archivo que el dispositivo quiere cargar. El dispositivo usa el URI de SAS para cargar el archivo en el almacenamiento y, cuando la carga ha finalizado, envía una notificación de finalización al Centro de IoT. El Centro de IoT comprueba que el archivo se ha cargado y luego agrega una notificación de carga de archivo al nuevo punto de conexión de mensajería de notificaciones de archivos accesible desde el servicio.

#### Asociación de una cuenta de Almacenamiento de Azure con el Centro de IoT

Para utilizar la funcionalidad de carga de archivos, primero debe vincular una cuenta de Almacenamiento de Azure al Centro de IoT. Esto puede hacerlo en el [Portal de Azure][lnk-management-portal] o mediante programación, con las [API del proveedor de recursos del Centro de IoT de Azure][lnk-resource-provider-apis]. Una vez que ha asociado una cuenta de almacenamiento al Centro de IoT, el servicio devuelve un URI de SAS a un dispositivo cuando este inicie una solicitud de carga de archivos.

> [AZURE.NOTE] Los [SDK del Centro de IoT de Azure][lnk-sdks] administran automáticamente la recuperación del URI de SAS: cargan el archivo y notifican al Centro de IoT que la carga se ha completado.

#### Inicialización de una carga de archivos

El Centro de IoT tiene dos puntos de conexión de REST que permiten la carga de archivos: uno para obtener el URI de SAS para el almacenamiento y el otro para notificar al Centro de IoT que la carga se ha completado. El dispositivo inicia el proceso de carga de archivos mediante el envío de una operación GET al Centro de IoT en `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. El centro devuelve un URI de SAS específico del archivo que se va a cargar, junto con un identificador de correlación que se usará una vez que finalice la carga.

#### Notificación al Centro de IoT de una carga de archivos completada

El dispositivo es responsable de cargar el archivo en el almacenamiento mediante los SDK de Almacenamiento de Azure. Cuando finaliza la carga, el dispositivo envía una operación POST al Centro de IoT en `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications/{correlationId}` con el identificador de correlación recibido de la operación GET inicial.

#### Notificaciones de carga de archivos

Cuando un dispositivo carga un archivo y notifica al Centro de IoT que la carga ha finalizado, el servicio genera opcionalmente un mensaje de notificación que contiene el nombre y la ubicación de almacenamiento del archivo.

Como se ha explicado en [Puntos de conexión](#endpoints), el Centro de IoT entrega notificaciones de carga de archivos a través de un punto de conexión accesible desde el servicio (**/messages/servicebound/fileuploadnotifications**) en forma de mensajes. La semántica de recepción de las notificaciones de carga de archivos es la misma que para los mensajes de nube a dispositivo y tiene el mismo [ciclo de vida del mensaje](#message lifecycle). Cada mensaje recuperado del punto de conexión de notificación de carga de archivos es un registro JSON con las siguientes propiedades:

| Propiedad | Descripción |
| -------- | ----------- |
| EnqueuedTimeUtc | Marca de tiempo que indica cuándo se creó la notificación. |
| DeviceId | **DeviceId** del dispositivo que ha cargado el archivo. |
| BlobUri | URI del archivo cargado. |
| BlobName | Nombre del archivo cargado. |
| LastUpdatedTime | Marca de tiempo que indica cuándo se actualizó por última vez el archivo. |
| BlobSizeInBytes | Tamaño del archivo cargado. |

**Ejemplo**. Se trata de un cuerpo de ejemplo de un mensaje de notificación de carga de archivos.

```
{
	"deviceId":"mydevice",
	"blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
	"blobName":"mydevice/myfile.jpg",
	"lastUpdatedTime":"2016-06-01T21:22:41+00:00",
	"blobSizeInBytes":1234,
	"enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

#### Opciones de configuración de notificaciones de carga de archivos <a id="c2dconfiguration"></a>

Cada Centro de IoT expone las siguientes opciones de configuración para las notificaciones de carga de archivos:

| Propiedad | Descripción | Intervalo y valor predeterminado |
| -------- | ----------- | ----------------- |
| **enableFileUploadNotifications** | Controla si se escribirán o no notificaciones de carga de archivos en el punto de conexión de notificaciones de archivo. | Bool. Valor predeterminado: True. |
| **fileNotifications.ttlAsIso8601** | TTL predeterminado para las notificaciones de carga de archivos. | Intervalo ISO\_8601 hasta 48H (1 minuto como mínimo). Valor predeterminado: 1 hora. |
| **fileNotifications.lockDuration** | Duración del bloqueo de la cola de notificaciones de carga de archivos. | De 5 a 300 segundos (5 segundos como mínimo). Valor predeterminado: 60 segundos. |
| **fileNotifications.maxDeliveryCount** | Número máximo de entregas en la cola de notificaciones de carga de archivos. | De 1 a 100. Valor predeterminado: 100. |

Para más información, consulte [Administración de Centros de IoT a través del portal de Azure][lnk-portal].

## Cuotas y limitación <a id="throttling"></a>

Cada suscripción de Azure puede tener como máximo 10 Centros de IoT.

Cada Centro de IoT se aprovisionó con un determinado número de unidades en una unidad de almacenamiento específico (para obtener más información, consulte [Centro de IoT Precios][lnk-pricing]). El valor de SKU y el número de unidades determinan la cuota diaria máxima de mensajes que puede enviar.

El valor de SKU también determina los valores de limitación que aplica el Centro de IoT a las operaciones.

### Limitaciones de operación

Las limitaciones de operación son las limitaciones de velocidad que se aplican en los intervalos de minutos y están diseñadas para evitar abusos. El Centro de IoT intenta evitar los errores de devolución siempre que sea posible, pero inicia la excepción de devolución si se infringe la limitación durante demasiado tiempo.

A continuación se muestra la lista de las limitaciones aplicadas. Los valores hacen referencia a un centro individual.

| Limitación | Valor por centro |
| -------- | ------------- |
| Operaciones de registro de identidad (crear, recuperar, enumerar, actualizar y eliminar) | 5000/min/unidad (para S3) <br/> 100/min/unidad (para S1 y S2). |
| Conexiones de dispositivos | 6000/s/unidad (para S3), 120/s/unidad (para S2), 12/s/unidad (para S1). <br/>Mínimo de 100/s. <br/> Por ejemplo, dos unidades S1 equivalen a 2*12 = 24/s, pero tendrá al menos 100/s en todas las unidades. Con nueve unidades S1 tiene 108/s (9 * 12) en las unidades. |
| Envíos de dispositivo a nube | 6000/s/unidad (para S3), 120/s/unidad (para S2), 12/s/unidad (para S1). <br/>Mínimo de 100/s. <br/> Por ejemplo, dos unidades S1 equivalen a 2*12 = 24/s, pero tendrá al menos 100/s en todas las unidades. Con nueve unidades S1 tiene 108/s (9 * 12) en las unidades. |
| Envíos de nube a dispositivo | 5000/min/unidad (para S3), 100/min/unidad (para S1 y S2). |
| Recepciones de nube a dispositivo | 50000/min/unidad (para S3), 1000/min/unidad (para S1 y S2). |
| Operaciones de carga de archivos | Notificaciones de carga de 5000 archivos/min/unidad (para S3), notificaciones de carga de 100 archivos/min/unidad (para S1 y S2). <br/> 10000 URI de SAS pueden estar fuera de una cuenta de almacenamiento al mismo tiempo.<br/> 10 URI/dispositivo de SAS puede estar fuera al mismo tiempo. | 

Es importante aclarar que la limitación de las *conexiones de dispositivo* determina la velocidad a la que se pueden establecer nuevas conexiones de dispositivo con un Centro de IoT, no el número máximo de dispositivos conectados a la vez. La limitación depende del número de unidades aprovisionadas para el concentrador.

Por ejemplo, si compra una sola unidad S1, tendrá una limitación de 100 conexiones por segundo. Esto significa que, para conectar 100 000 dispositivos, se tarda al menos 1000 segundos (aproximadamente 16 minutos). Sin embargo, puede tener el mismo número de dispositivos conectados al mismo tiempo que de dispositivos registrados en el registro de identidad de dispositivos.

Consulte la entrada de blog [IoT Hub throttling and you][lnk-throttle-blog] (Limitación del Centro de IoT) para ver una explicación detallada del comportamiento de limitación del Centro de IoT.

>[AZURE.NOTE] En cualquier momento, es posible aumentar las cuotas o las limitaciones si aumenta el número de unidades aprovisionadas en un Centro de IoT.

>[AZURE.IMPORTANT] Las operaciones de registro de identidad están diseñadas para usarse en tiempo de ejecución en escenarios de administración y aprovisionamiento de dispositivos. La lectura o actualización de un gran número de identidades de dispositivo se realiza mediante [trabajos de importación y exportación](#importexport).

## Pasos siguientes

Ahora que ha visto la información general sobre desarrollo para Centro de IoT, siga estos vínculos para más información:

- [Carga de archivos desde dispositivos (tutorial)][lnk-file upload]
- [Create an IoT hub programatically (Crear un Centro de IoT mediante programación)][lnk-create-hub]
- [Introducción a C SDK][lnk-c-sdk]
- [SDK de Centro de IoT][lnk-sdks]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

- [Diseño de la solución][lnk-design]
- [Exploración de la administración de dispositivos desde Centro de IoT de Azure con la IU de ejemplo][lnk-dmui]
- [SDK de puerta de enlace de IoT (beta): envío de mensajes del dispositivo a la nube con un dispositivo simulado usando Linux][lnk-gateway]
- [Administración de Centros de IoT a través del portal de Azure][lnk-portal]
- [Protección total de la solución de IoT][lnk-securing]



[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx

[img-endpoints]: ./media/iot-hub-devguide/endpoints.png
[img-lifecycle]: ./media/iot-hub-devguide/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide/eventhubcompatible.png

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx

[lnk-sas-tokens]: iot-hub-sas-tokens.md
[lnk-azure-gateway-guidance]: iot-hub-guidance.md#field-gateways
[lnk-guidance-provisioning]: iot-hub-guidance.md#provisioning
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-guidance-heartbeat]: iot-hub-guidance.md#heartbeat

[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-getstarted-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../resource-group-overview.md
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-guidance-d2c-processing]: iot-hub-csharp-csharp-process-d2c.md
[lnk-management-portal]: https://portal.azure.com
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-tls]: https://tools.ietf.org/html/rfc5246
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-mqtt-support]: iot-hub-mqtt-support.md
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus

[lnk-file upload]: iot-hub-csharp-csharp-file-upload.md
[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md
[lnk-securing]: iot-hub-security-ground-up.md

<!---HONumber=AcomDC_0727_2016-->