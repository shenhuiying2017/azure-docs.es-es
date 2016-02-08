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
 ms.date="01/07/2016"
 ms.author="dobett"/>

# Guía del desarrollador del Centro de IoT de Azure

Centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional confiable y segura entre millones de dispositivos IoT y el back-end de una aplicación.

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
* **Puntos de conexión de dispositivos**: para cada dispositivo aprovisionado en el registro de identidad del dispositivo, Centro de IoT muestra un conjunto de puntos de conexión que puede usar el dispositivo para enviar y recibir mensajes. Estos puntos de conexión se exponen actualmente usando los protocolos HTTP y [AMQP][lnk-amqp]\:
    - *Envío de mensajes de dispositivo a nube*. Use este punto de conexión para enviar mensajes de dispositivo a la nube. Para obtener más información, consulte [Mensajería de dispositivo a nube](#d2c).
    - *Recepción de mensajes de nube a dispositivo*. El dispositivo usa ese punto de conexión para recibir mensajes de nube a dispositivos dirigidos. Para obtener más información, consulte [Mensajería de nube a dispositivo](#c2d).
* **Puntos de conexión de servicio**: cada Centro de IoT muestra un conjunto de puntos de conexión que usa el back-end de aplicaciones para comunicarse con los dispositivos. Estos puntos de conexión se exponen actualmente usando solo el protocolo [AMQP][lnk-amqp].
    - *Recepción de mensajes de dispositivo a nube*. Este punto de conexión es compatible con los [Centros de eventos de Azure][lnk-event-hubs] y puede usarse para leer todos los mensajes de dispositivo a la nube enviados por los dispositivos. Para obtener más información, consulte [Mensajería de dispositivo a nube](#d2c).
    - *Envío de mensajes de nube a dispositivo y recepción de confirmaciones de entrega*. Estos puntos de conexión permiten al back-end de aplicaciones enviar mensajes confiables de nube a dispositivo y recibir las confirmaciones de entrega o expiración correspondientes. Para obtener más información, consulte [Mensajería de nube a dispositivo](#c2d).

El artículo [API y SDK del Centro de IoT][lnk-apis-sdks] describe las distintas formas a las que se puede acceder a estos puntos de conexión.

Finalmente, es importante tener en cuenta que todos los puntos de conexión de Centro de IoT usan el protocolo [TLS][lnk-tls] y ningún punto de conexión se expone en canales sin cifrar o no seguros.

### Cómo interpretar los puntos de conexión compatibles con los centros de eventos. <a id="eventhubcompatible"></a>

Cuando se usa el [SDK de Bus de servicio de Azure para .NET](https://www.nuget.org/packages/WindowsAzure.ServiceBus) o el [host del procesador de eventos de Centros de eventos][] descrito en el blog, puede usar cualquier cadena de conexión de Centro de IoT con los permisos correctos y después usar **messages/events** como nombre del Centro de eventos.

Cuando se usan SDK (o integraciones de productos) que no detectan el Centro de IoT, tiene que recuperar un punto de conexión y un nombre de centro de eventos compatibles con los centros de eventos en la configuración del Centro de IoT en el [portal de Azure][]\:

1. En la hoja del Centro de IoT, haga clic en **Configuración** y después e **Mensajería**,
2. En la sección **Configuración de dispositivo a la nube**, encontrará un cuadro **Punto de conexión compatible con Centro de eventos**, **Nombre compatible con Centro de eventos** y **Particiones**.

    ![][img-eventhubcompatible]

> [AZURE.NOTE] En ocasiones, el SDK requiere un valor de **nombre de host** o **espacio de nombres**. En ese caso, quite el esquema de **Punto de conexión compatible con Centro de eventos**. Por ejemplo, si el punto de conexión compatible con el centro de eventos es ****sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, el **nombre de host** sería **iothub-ns-myiothub-1234.servicebus.windows.net** y el **espacio de nombres** sería **iothub-ns-myiothub-1234**.

Luego, puede usar cualquier directiva de seguridad de acceso compartido que tenga permisos **ServiceConnect** para conectarse al Centro de eventos especificado.

En caso de que tenga que crear una cadena de conexión del Centro de eventos con la información anterior, use el modelo siguiente:

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

La siguiente es una lista de los SDK e integraciones que puede usar con los puntos de conexión compatibles con el Centro de eventos que muestra Centro de IoT:

* [Cliente de Centros de eventos de Java](https://github.com/hdinsight/eventhubs-client)
* [Spout de Apache Storm](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). Puede ver el [origen de spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) en GitHub.
* [Integración de Apache Spark](../hdinsight/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)

## Registro de identidad del dispositivo

Cada Centro de IoT tiene un registro de identidad del dispositivo que se usa para crear recursos de cada dispositivo en el servicio, como una cola que contiene los mensajes de nube a dispositivo en curso, y permite el acceso a los puntos de conexión accesibles desde el dispositivo, tal como se explica en la sección [Control de acceso](#accesscontrol).

En un nivel superior, el registro de identidad del dispositivo es una colección de recursos de identidad de dispositivos compatible con REST. Las secciones siguientes detallan las propiedades de los recursos de identidad del dispositivo y las operaciones que el registro permite en las identidades.

> [AZURE.NOTE] Consulte [API y SDK del Centro de IoT][lnk-apis-sdks] para obtener más información sobre el protocolo HTTP y los SDK que puede usar para interactuar con el registro de identidad del dispositivo.

### Propiedades de identidad del dispositivo <a id="deviceproperties"></a>

Las identidades de dispositivos se representan como documentos JSON con las propiedades siguientes.

| Propiedad | Opciones | Descripción |
| -------- | ------- | ----------- |
| deviceId | necesarias, de solo lectura en actualizaciones | Una cadena que distingue mayúsculas y minúsculas (de hasta 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':', '.', '+', '&percnt;', '_', '&num;', '&ast;', '?', '!', '(', ')', ',', '=', '&commat;', ';', '&dollar;', '''}`. |
| generationId | requerido, de solo lectura | Una cadena que distingue mayúsculas y minúsculas generada por el centro de hasta 128 caracteres. Se usa para distinguir dispositivos con el mismo **deviceId** cuando se eliminaron y se volvieron a crear. |
| ETag | requerido, de solo lectura | Una cadena que representa un valor de etag débil para la identidad del dispositivo, como por [RFC7232][lnk-rfc7232].|
| auth | opcional | Un objeto compuesto que contiene material de seguridad e información de autenticación. |
| auth.symkey | opcional | Un objeto compuesto que contiene una clave principal y una secundaria, almacenadas en formato base64. |
| status | requerido | Puede ser **Habilitado** o **Deshabilitado**. Si está **habilitado**, el dispositivo se puede conectar. Si está **deshabilitado**, este dispositivo no puede acceder a ningún punto de conexión accesible desde el dispositivo. |
| statusReason | opcional | Una cadena de 128 caracteres que almacena el motivo del estado de la identidad del dispositivo. Se permiten todos los caracteres UTF-8. |
| statusUpdateTime | solo lectura | Fecha y hora de la última actualización del estado. |
| connectionState | solo lectura | **Conectado** o **Desconectado**, representa la vista del Centro de IoT del estado de conexión del dispositivo. |
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

Todas estas operaciones permiten usar la simultaneidad optimista, tal como se especifica en [RFC7232][lnk-rfc7232].

> [AZURE.IMPORTANT] La única manera de recuperar todas las identidades en un registro de identidades de un centro es usar la funcionalidad [Exportar](#importexport).

El registro de identidad del dispositivo de Centro de IoT: - No contiene los metadatos de la aplicación. - Se puede acceder a él como un diccionario mediante **deviceId** como clave. - No admite consultas expresivas.

Una solución de IoT normalmente tiene un almacén independiente específico de la solución que contiene los metadatos específicos de la aplicación. Por ejemplo, el almacén específico de la solución en una solución de un edificio inteligente puede registrar la sala en la que se implementa un sensor de temperatura.

### Deshabilitación de dispositivos

Puede deshabilitar los dispositivos actualizando la propiedad **status** de una identidad en el registro. Normalmente se usa en dos escenarios:

- Durante el proceso de orquestación de aprovisionamiento. Para más información, consulte [Diseño de la solución - Aprovisionamiento de dispositivos][lnk-guidance-provisioning].
- Si por cualquier motivo considera que un dispositivo está en peligro o no está autorizado.

### Exportación de identidades de dispositivos <a id="importexport"></a>

Las exportaciones son trabajos de larga duración que usan un contenedor de blobs proporcionado por el cliente para leer y escribir los datos de identidad del dispositivo.

Puede exportar identidades de dispositivos de forma masiva desde el registro de identidades de un Centro de IoT, mediante el uso de operaciones asincrónicas en el [punto de conexión de proveedor de recursos del Centro de IoT](#endpoints).

Estas son las operaciones que se pueden realizar en los trabajos de exportación:

* Crear un trabajo de exportación
* Recuperar el estado de un trabajo en ejecución
* Cancelar un trabajo en ejecución

> [AZURE.NOTE] Cada concentrador puede tener solo un único trabajo de ejecución en un momento dado.

Para obtener información detallada sobre la importación y exportación de API, consulte [Centro de IoT de Azure: API de proveedor de recursos][lnk-resource-provider-apis].

#### Trabajos

Todos los trabajos de exportación tienen las siguientes propiedades:

| Propiedad | Opciones | Descripción |
| -------- | ------- | ----------- |
| jobId | generadas por el sistema, omitidas durante la creación | |
| creationTime | generadas por el sistema, omitidas durante la creación | |
| endOfProcessingTime | generadas por el sistema, omitidas durante la creación | |
| type | solo lectura | **Exportación** |
| status | generadas por el sistema, omitidas durante la creación | **En cola**, **Iniciado**, **Completado**, **Con error** |
| progreso | generadas por el sistema, omitidas durante la creación | Valor entero del porcentaje de finalización. |
| outputBlobContainerURI | necesarias para todos los trabajos | Identificador URI de Firma de acceso compartido de blob con acceso de escritura a un contenedor de blobs (consulte [Firmas de acceso compartido, Parte 2: Creación y uso de una firma de acceso compartido con el servicio BLOB][lnk-createuse-sas]). Se usa para mostrar el estado del trabajo y los resultados. |
| includeKeysInExport | opcional | Si es **true**, las claves se incluyen en la exportación; si no, las claves se exportan como **null**. El valor predeterminado es **false**. |
| failureReason | generadas por el sistema, omitidas durante la creación | Si el estado es **Con error**, una cadena contiene el motivo. |

#### Trabajos de exportación

Los trabajos de exportación toman un URI de Firma de acceso compartido de blob como parámetro. Esto concede acceso de escritura a un contenedor de blobs para habilitar el trabajo y generar sus resultados.

El trabajo escribe los resultados de salida en el contenedor de blobs especificado en un archivo denominado **job\_{job\_id}\_devices.txt**. Este archivo contiene identidades de dispositivos serializadas como JSON, tal como se especifica en [Propiedades de identidad del dispositivo](#deviceproperties). Los materiales de seguridad se establecen en **null** en caso de que **includeKeysInExport** se establezca en **false**.

**Ejemplo**:

```
{"deviceId":"devA","auth":{"symKey":{"primaryKey":"123"}},"status":"enabled"}
{"deviceId":"devB","auth":{"symKey":{"primaryKey":"234"}},"status":"enabled"}
{"deviceId":"devC","auth":{"symKey":{"primaryKey":"345"}},"status":"enabled"}
{"deviceId":"devD","auth":{"symKey":{"primaryKey":"456"}},"status":"enabled"}
```

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

* **Credenciales de seguridad de cada dispositivo**. Cada Centro de IoT contiene un [registro de identidad del dispositivo](#device-identity-registry). Para cada dispositivo de este registro, puede configurar credenciales de seguridad que concedan permisos **DeviceConnect** orientados a los puntos de conexión del dispositivo correspondiente.

**Ejemplo**. En una solución típica de IoT:-El componente de administración de dispositivos usa la directiva *registryReadWrite*. - El componente de procesador de eventos usa la directiva *service*. - El componente de lógica de negocios de dispositivos en tiempo de ejecución usa la directiva *service*. - Los dispositivos individuales se conectan mediante las credenciales almacenadas en el registro de identidad de Centro de IoT.

Para obtener orientación sobre temas de seguridad de Centro de IoT, consulte la sección de seguridad de [Diseño de la solución][lnk-guidance-security].

### Autenticación

El Centro de IoT de Azure concede acceso a los puntos de conexión mediante la comprobación de un token con las directivas de acceso compartido y las credenciales de seguridad del registro de identidad del dispositivo.

Las credenciales de seguridad, como las claves simétricas, nunca se envían en la conexión.

> [AZURE.NOTE] El proveedor de recursos del Centro de IoT de Azure se protege mediante la suscripción de Azure, igual que todos los proveedores en el [Administrador de recursos de Azure][lnk-azure-resource-manager].

#### Formato del token de seguridad <a id="tokenformat"></a>

El token de seguridad tiene el formato siguiente:

	SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

Estos son los valores esperados:

| Valor | Descripción |
| ----- | ----------- |
| {signature} | Una cadena de firma de HMAC-SHA256 con el formato: `{URL-encoded-resourceURI} + "\n" + expiry` **Importante**: La clave se descodifica en base64 y se utiliza como clave para realizar el cálculo de HMAC-SHA256. |
| {resourceURI} | Prefijo URI (por segmento) de los puntos de conexión a los que se puede acceder con este token. Por ejemplo, `/events` |
| {expiry} | Cadenas UTF8 para el número de segundos transcurridos desde el tiempo 00:00:00 UTC el 1 de enero de 1970. |
| {URL-encoded-resourceURI} | Codificación de dirección URL en minúsculas del URI del recurso en minúsculas |
| {policyName} | El nombre de la directiva de acceso compartido a la que hace referencia este token. Ausente en el caso de los token que hacen referencia a las credenciales del registro de dispositivos. |

**Nota sobre el prefijo**: el prefijo URI se calcula por segmento y no por carácter. Por ejemplo `/a/b` es un prefijo de `/a/b/c` pero `/a/bc`.

#### Detalles específicos de protocolo

Cada protocolo admitido, como HTTP y AMQP, transporta tokens de diferentes maneras.

HTTP implementa la autenticación mediante la inclusión de un token válido en el encabezado de solicitud **Authorization**. Un parámetro de consulta llamado **Authorization** también puede transportar el token.

Al usar [AMQP][lnk-amqp], el Centro de IoT admite [SASL PLAIN][lnk-sasl-plain] y [seguridad basada en notificaciones AMQP][lnk-cbs].

En el caso de la seguridad basada en notificaciones AMQP, la norma especifica cómo transmitir estos tokens.

Para SASL PLAIN, el **nombre de usuario** puede ser:

* `{policyName}&commat;sas.root.{iothubName}` en el caso de los tokens de nivel de centro.
* `{deviceId}` en el caso de los tokens con ámbito de dispositivo.

En ambos casos el campo de contraseña contiene el token, tal como se describe en la sección [Formato de tokens](#tokenformat).

> [AZURE.NOTE] Los [SDK del Centro de IoT de Azure][lnk-apis-sdks] generan tokens automáticamente cuando se conectan al servicio. En algunos casos, los SDK no admiten todos los protocolos o todos los métodos de autenticación.

#### SASL PLAIN comparado con CBS

Cuando se usa SASL PLAIN, un cliente que se conecta a un Centro de IoT puede usar un token único para cada conexión TCP. Cuando el token expira, la conexión TCP se desconecta del servicio y desencadena una reconexión. Este comportamiento, aunque no resulta problemático para un componente de back-end de aplicaciones, es muy perjudicial para una aplicación de dispositivo por los siguientes motivos:

*  Las puertas de enlace normalmente se conectan en nombre de muchos dispositivos. Cuando se usa SASL PLAIN, tienen que crear una conexión TCP distintiva para cada dispositivo que se conecta a un Centro de IoT. Esto aumenta considerablemente el consumo de energía y de recursos de red, y aumenta la latencia de cada conexión de dispositivo.
* Los dispositivos con recursos restringidos se verán afectados negativamente por el aumento del uso de recursos para volver a conectarse después de cada expiración del token.

### Control de las credenciales de nivel de centro

Puede restringir el ámbito de las directivas de seguridad de nivel de centro mediante la creación de tokens con un URI de recurso restringido. Por ejemplo, el punto de conexión para enviar mensajes de dispositivo a la nube desde un dispositivo es **/devices/{deviceId}/events**. También puede usar una directiva de acceso compartido de nivel de centro con permisos **DeviceConnect** para firmar un token cuyo resourceURI sea **/devices/{deviceId}/events**, de forma que se crea un token que solo se puede usar para enviar dispositivos en nombre del dispositivo **deviceId**.

Se trata de un mecanismo similar a la [directiva de edición de Centros de eventos][lnk-event-hubs-publisher-policy] y permite que se implementen métodos de autenticación personalizados, tal como se explica en la sección de seguridad de [Diseño de la solución][lnk-guidance-security].

## Mensajería

Centro de IoT proporciona primitivas de mensajería para comunicarse:- [Nube a dispositivo](#c2d): desde un back-end de aplicación (*servicio* o *nube*).- [Dispositivo a la nube](#d2c): desde un dispositivo a un back-end de aplicación.

Las propiedades básicas de la funcionalidad de mensajería del Centro de IoT son la confiabilidad y durabilidad de los mensajes. Esto permite la resistencia a la conectividad intermitente en el dispositivo y a los picos de carga del procesamiento de eventos en la nube. Centro de IoT implementa *al menos una vez* las garantías de entrega para las mensajerías de dispositivo a la nube y de la nube a dispositivo.

Centro de IoT admite varios protocolos accesibles desde los dispositivos (por ejemplo, AMQP y HTTP/1). Con el fin de admitir la interoperabilidad sin problemas entre protocolos, Centro de IoT define un formato de mensaje común que es compatible con todos los protocolos accesibles desde el dispositivo.

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
| Para | Se usa en mensajes de [nube a dispositivo](#c2d) para especificar el campo de destino.|
| ExpiryTimeUtc | Fecha y hora de la expiración del mensaje. |
| EnqueuedTime | Hora en la que el Centro de IoT se recibió el mensaje. |
| CorrelationId | Propiedad de cadena que normalmente contiene el identificador del mensaje de la solicitud en los patrones de solicitud y respuesta. |
| UserId | Se usa para especificar el origen de los mensajes. Cuando el Centro de IoT genera mensajes, se establece en `{iot hub name}`. |
| Ack | Se usa en los mensajes de nube a dispositivo para solicitar a Centro de IoT que genere mensajes de comentarios debido al consumo del mensaje por el dispositivo. Valores posibles: **none** (valor predeterminado): no se genera ningún mensaje de comentarios, **positive**: recibe un mensaje de comentarios si el mensaje expiró, **negative**: recibe un mensaje de comentarios si el mensaje expiró (o se alcanzó el número máximo de entregas) sin que se complete en el dispositivo y **full**: comentarios positivos y negativos. Para obtener más información, consulte [Comentarios de mensajes](#feedback). |
| ConnectionDeviceId | Establecido por Centro de IoT en los mensajes de dispositivo a la nube. Contiene el **deviceId** del dispositivo que envió el mensaje. |
| ConnectionDeviceGenerationId | Establecido por Centro de IoT en los mensajes de dispositivo a la nube. Contiene el **generationId** (como se indica en [Propiedades de identidad de dispositivos](#deviceproperties)) del dispositivo que envió el mensaje. |
| ConnectionAuthMethod | Establecido por Centro de IoT en los mensajes de dispositivo a la nube. Información sobre el método de autenticación usado para autenticar el dispositivo que envía el mensaje. Para obtener más información, consulte [Propiedades contra la suplantación](#antispoofing).|

### Elección del protocolo de comunicación <a id="amqpvshttp"></a>

El Centro de IoT admite los protocolos [AMQP][lnk-amqp] y HTTP/1 para las comunicaciones del dispositivo. A continuación se muestra una lista de las consideraciones con respecto a sus usos.

* **Patrón de nube a dispositivo**. HTTP/1 no cuenta con una forma eficaz de implementar la inserción de servidor. Por lo tanto, cuando se usa HTTP/1, los dispositivos sondean los mensajes de nube a dispositivo en el Centro de IoT. Esto resulta muy ineficaz tanto para el dispositivo como para el Centro de IoT. Las directrices actuales, cuando se usa HTTP/1, consisten e establecer un intervalo de sondeo para cada dispositivo de menos de una vez cada 25 minutos. Por otro lado, AMQP admite la inserción de servidor al recibir mensajes de nube a dispositivo, lo que permite inserciones inmediatas de mensajes desde el Centro de IoT al dispositivo. Si le preocupa la latencia de entrega, es mucho mejor usar el protocolo AMQP. Por otro lado, para los dispositivos apenas conectados, HTTP/1 funciona también.
* **Puertas de enlace de campo**. Dadas las limitaciones de HTTP/1 con respecto a la inserción de servidor, no resulta adecuado su uso en [escenarios de puerta de enlace de campo][lnk-azure-gateway-guidance].
* **Dispositivos con bajos recursos**. Las bibliotecas HTTP/1 son mucho menores que las de AMQP. Como tales, si el dispositivo tiene pocos recursos (por ejemplo, menos de 1 MB de RAM), HTTP/1 puede ser la única implementación de protocolo disponible.
* **Cruce seguro de red**. El estándar AMQP escucha en el puerto 5672. Esto podría producir problemas en las redes que están cerradas para los protocolos que no son HTTP.
* **Tamaño de carga**. AMQP es un protocolo binario, que es mucho más compacto que HTTP/1.

En un nivel superior, debe usar AMQP siempre que sea posible y use solo HTTP/1 si los recursos del dispositivo o la configuración de red no admiten AMQP. Además, cuando se usa HTTP/1, la frecuencia de sondeo debe establecerse a menos de una vez cada 25 minutos para cada dispositivo. Ciertamente, durante el desarrollo, es aceptable disponer de frecuencias de sondeo más frecuentes.

Como consideración final, es importante hacer referencia a la [puerta de enlace del protocolo de IoT de Azure][lnk-azure-protocol-gateway], que le permite implementar una puerta de enlace MQTT de alto rendimiento que interactúa directamente con el Centro de IoT. El protocolo MQTT admite la inserción de servidor (lo que permite la entrega inmediata de mensajes de nube a dispositivo al dispositivo) y está disponible para dispositivos con muy pocos recursos. La principal desventaja de este enfoque es el requisito de autohospedar y administrar una puerta de enlace de protocolo.

### Dispositivo a nube <a id="d2c"></a>

Tal como se detalla en la sección [Puntos de conexión](#endpoints), los mensajes de dispositivo a la nube se envían a través de un punto de conexión del dispositivo (**/devices/{deviceId}/messages/events**) y se reciben a través de un punto de conexión accesible desde el servicio (**/messages/events**), que es compatible con [Centros de eventos][lnk-event-hubs]. Por lo tanto, puede usar la integración de los Centros de eventos estándar y los SDK para recibir mensajes de dispositivo a la nube.

El Centro de IoT implementa la mensajería de dispositivo a la nube de manera muy similar a los [centros de eventos][lnk-event-hubs], siendo los mensajes de dispositivo a nube del Centro de IoT más similares a los *eventos* de los centros de eventos que los [mensajes][lnk-servicebus] del *Bus de servicio*.

Esta implementación tiene las implicaciones siguientes:

* De forma similar a los *eventos* de los centros de eventos, los mensajes de dispositivo a nube son duraderos y se conservan en un Centro de IoT hasta 7 días (consulte [Opciones de configuración de dispositivo a nube](#d2cconfiguration)).
* Los mensajes de dispositivo a la nube se fragmentan en un conjunto fijo de particiones que se establece en el momento de creación (consulte [Opciones de configuración de dispositivo a nube](#d2cconfiguration)).
* De igual forma que los centros de eventos, los clientes que leen mensajes de dispositivo a nube tienen que administrar particiones y puntos de control. Consulte [Centros de eventos: consumo de eventos][lnk-event-hubs-consuming-events].
* Al igual que los eventos de los centros de eventos, los mensajes de dispositivo a nube pueden tener como máximo 256 Kb y se pueden agrupar en lotes para optimizar los envíos. Los lotes pueden tener un tamaño máximo de 256 Kb y como máximo 500 mensajes.

Sin embargo, hay algunas diferencias importantes entre los mensajes de dispositivo a nube del Centro de IoT y los Centros de eventos:

* Como se explica en la sección [Seguridad](#security), el Centro de IoT permite la autenticación por dispositivo y el control de acceso.
* El Centro de IoT permite millones de dispositivos conectados simultáneamente (consulte [Cuotas y limitación](#throttling)), mientras que los centros de eventos se limitan a 5000 conexiones AMQP por espacio de nombres.
* Centro de IoT no admite la partición arbitraria con un dispositivo **PartitionKey**. Los mensajes de dispositivo a la nube se dividen en función de su **deviceId** de origen.
* El escalado de un Centro de IoT es ligeramente diferente en el caso de los centros de eventos. Para obtener más información, consulte [Escalado de un Centro de IoT][lnk-guidance-scale].

Tenga en cuenta que esto no significa que puede sustituir Centro de IoT en Centros de eventos en todas las situaciones. Por ejemplo, en algunos cálculos de procesamiento de eventos, podría ser necesario volver a crear particiones de eventos con respecto a un campo o propiedad diferentes antes de analizar los flujos de datos. En esta situación, puede usar un Centro de eventos para desacoplar las dos partes de la canalización de procesamiento de la transmisión.

Para obtener información detallada sobre cómo usar la mensajería de dispositivo a nube, consulte [API y SDK del Centro de IoT][lnk-apis-sdks].

> [AZURE.NOTE] Cuando se utiliza HTTP para enviar mensajes de dispositivo a la nube, las siguientes cadenas pueden contener únicamente caracteres ASCII: los valores de propiedad del sistema y los valores y nombres de las propiedades de la aplicación.

#### Tráfico sin telemetría

En muchos casos, además de los puntos de datos de telemetría, los dispositivos también envían mensajes y solicitudes *interactivos* que requieren la ejecución y el control de la capa de lógica de negocio de la aplicación. Por ejemplo, alertas críticas que deben desencadenar una acción específica en el back-end o respuestas de dispositivo a los comandos enviados desde el back-end.

Consulte [Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT][lnk-guidance-d2c-processing] para más información sobre la mejor manera de procesar este tipo de mensajes.

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

Tal como se detalla en la sección [Puntos de conexión](#endpoints), puede enviar mensajes de la nube al dispositivo a través de un punto de conexión dirigido al servicio (**/messages/devicebound**) y un dispositivo puede recibirlos a través de un punto de conexión específico del dispositivo (**/devices/{deviceId}/messages/devicebound**).

Cada mensaje de nube a dispositivo está destinado a un único dispositivo, estableciendo la propiedad **to** en **/devices/{deviceId}/messages/devicebound**.

**Importante**: cada cola de dispositivos puede contener como máximo 50 mensajes de nube a dispositivo. Si se intenta enviar más mensajes al mismo dispositivo, se producirá un error.

> [AZURE.NOTE] Al enviar mensajes de nube a dispositivo, las siguientes cadenas pueden contener únicamente caracteres ASCII: los valores de propiedad del sistema y los valores y nombres de las propiedades de la aplicación.

#### Ciclo de vida de los mensajes <a id="message lifecycle"></a>

Para implementar *al menos una vez* la garantía de entrega, los mensajes de nube a dispositivo se almacenan en colas por dispositivo y los dispositivos tienen que reconocer explícitamente la *finalización* para que el Centro de IoT pueda quitarlos de la cola. Esto garantiza la resistencia frente a errores de dispositivo y de conectividad.

En el diagrama siguiente se detalla el gráfico de estado del ciclo de vida de un mensaje de nube a dispositivo.

![Ciclo de vida de los mensajes de nube a dispositivo][img-lifecycle]

Cuando el servicio envía un mensaje, se considera que está *en cola*. Cuando un dispositivo desea *recibir* un mensaje, Centro de IoT *bloquea* el mensaje (establece el estado en **Invisible**), con el fin de permitir a otros subprocesos del mismo dispositivo empezar a recibir otros mensajes. Cuando el subproceso de un dispositivo haya finalizado el procesamiento, lo notifica a Centro de IoT *finalizando* el mensaje.

Un dispositivo también puede:- *Rechazar* el mensaje, que provoca que el Centro de IoT lo establezca en el estado **Deadlettered**. - *Abandonar* el mensaje, que causa que el Centro de IoT vuelva a colocar el mensaje en la cola con el estado establecido en **Enqueued**.

Podría producirse un error en el subproceso al procesar un mensaje sin notificar a Centro de IoT. En este caso, los mensajes pasan automáticamente del estado **Invisible** al estado **Enqueued** después de un *tiempo de espera de visibilidad (o bloqueo)* con un valor predeterminado de un minuto. Un mensaje puede pasar entre los estados **Enqueued** e **Invisible** durante un número de veces especificado en la propiedad *max delivery count* en el Centro de IoT. Después de ese número de transiciones, el Centro de IoT establece el estado del mensaje en **Deadlettered**. De igual forma, el Centro de IoT establece el estado de un mensaje en **Deadlettered** después de su fecha de expiración (consulte [Período de vida](#ttl)).

Para ver un tutorial sobre los mensajes de nube a dispositivo, consulte [Introducción a los mensajes de nube a dispositivo del Centro de IoT de Azure][lnk-getstarted-c2d-tutorial]. Para consultar temas de referencia sobre cómo las diferentes API y SDK exponen la funcionalidad de dispositivo de nube, vea [API y SDK del Centro de IoT][lnk-apis-sdks].

> [AZURE.NOTE] Normalmente los mensajes de nube a dispositivo se completan siempre que la pérdida del mensaje no afecte a la lógica de aplicación. Esto podría suceder en varios escenarios diferentes. Por ejemplo, el contenido del mensaje se conservó correctamente en el almacenamiento local, o bien una operación se ejecutó correctamente o el mensaje transporta información temporal cuya pérdida no afecta a la funcionalidad de la aplicación. A veces, en el caso de tareas de larga ejecución, puede completar el mensaje de nube a dispositivo después de que persistió la descripción de la tarea en el almacenamiento local y, luego, notificar el back-end de aplicaciones con uno o varios mensajes de dispositivo a la nube en diferentes fases del progreso de la tarea.

#### Período de vida <a id="ttl"></a>

Cada mensaje de nube a dispositivo tiene una fecha de expiración. La puede establecer explícitamente el servicio (en la propiedad **ExpiryTimeUtc**), o bien el Centro de IoT usando el *período de vida* predeterminado especificado como una propiedad del Centro de IoT. Consulte [Opciones de configuración de nube a dispositivo](#c2dconfiguration).

#### Comentarios de mensaje <a id="feedback"></a>

Cuando envía un mensaje de nube a dispositivo, el servicio puede solicitar la entrega de los comentarios de cada mensaje en relación con el estado final de ese mensaje.

- Si establece la propiedad **Ack** en **positive**, Centro de IoT genera un mensaje de comentarios únicamente si el mensaje de nube a dispositivo alcanza el estado **Completed**.
- Si establece la propiedad **Ack** en **negative**, Centro de IoT genera un mensaje de comentarios únicamente si el mensaje de nube a dispositivo alcanza el estado **Deadletterd**.
- Al establecer la propiedad **Ack** en **full**, Centro de IoT genera un mensaje de comentarios en cualquier caso.

Como se explica en [Puntos de conexión](#endpoints), Centro de IoT ofrece comentarios a través de un punto de conexión orientado a servicios (**/messages/servicebound/feedback**) como mensajes. La semántica de recepción de los comentarios es la misma que para los mensajes de nube a dispositivo que tienen el mismo [ciclo de vida del mensaje](#message lifecycle). Siempre que sea posible, los comentarios del mensaje se realizan por lotes en un único mensaje, con el formato siguiente.

Cada mensaje recuperado desde el punto de conexión de comentarios tiene las siguientes propiedades.

| Propiedad | Descripción |
| -------- | ----------- |
| EnqueuedTime | Marca de tiempo que indica cuándo se creó el lote. |
| UserId | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

El cuerpo es una matriz serializada de JSON de registros, cada uno con las siguientes propiedades:

| Propiedad | Descripción |
| -------- | ----------- |
| EnqueuedTimeUtc | Marca de tiempo que indica cuándo se produjo el resultado del mensaje. Por ejemplo, el dispositivo completado o el mensaje expirado. |
| OriginalMessageId | **MessageId** del mensaje de nube a dispositivo al que pertenece esta información de comentarios. |
| Descripción | Los valores de cadena para los resultados anteriores. |
| DeviceId | **DeviceId** del dispositivo de destino del mensaje de nube a dispositivo al que pertenece este elemento de comentarios. |
| DeviceGenerationId | **DeviceGenerationId** del dispositivo de destino del mensaje de nube a dispositivo al que pertenece este elemento de comentarios. |

**Importante**. El servicio tiene que especificar un **MessageId** para el mensaje de nube a dispositivo para poder correlacionar sus comentarios con el mensaje original.

**Ejemplo**. Este es un cuerpo de ejemplo de un mensaje de comentarios.

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
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

## Cuotas y limitación <a id="throttling"></a>

Cada suscripción de Azure puede tener como máximo 10 Centros de IoT.

Cada Centro de IoT se aprovisionó con un determinado número de unidades en una unidad de almacenamiento específico (para obtener más información, consulte [Centro de IoT Precios][lnk-pricing]). La unidad de almacenamiento y el número de unidades determinan la cuota máxima de diaria de mensajes que se pueden enviar, así como el número máximo de identidades del dispositivo en el registro de identidad. El número identidades en el registro limita el número de dispositivos conectados de forma simultánea.

La SKU también determina los valores de limitación que aplica Centro de IoT a las operaciones.

### Cuota de registro de identidad del dispositivo

El Centro de IoT permite a lo sumo 1100 actualizaciones del dispositivo (crear, actualizar y eliminar) por unidad (con independencia de la unidad de almacenamiento) por día.

### Limitaciones de operación

Las limitaciones de operación son las limitaciones de velocidad que se aplican en los intervalos de minutos y están diseñadas para evitar abusos. El Centro de IoT intenta evitar los errores de devolución siempre que sea posible, pero inicia la excepción de devolución si se infringe la limitación durante demasiado tiempo.

A continuación se muestra la lista de las limitaciones aplicadas. Los valores hacen referencia a un centro individual.

| Limitación | Valor por centro |
| -------- | ------------- |
| Las operaciones de registro de identidades (crear, recuperar, enumerar, actualizar y eliminar), importación y exportación masiva o individual | 100/min/unidad, hasta 5.000/min |
| Conexiones de dispositivos | 100/s/unidad |
| Envíos de dispositivo a nube | 120/seg/unidad (para S2), 12/seg/unidad (para S1). Mínimo de 100/s. |
| Operaciones de nube a dispositivo (envíos, recepciones, comentarios) | 100/min/unidad |

**Nota**. En cualquier momento, es posible aumentar las cuotas o las limitaciones si aumenta el número de unidades aprovisionadas en un Centro de IoT.

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

[lnk-compatibility]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx

[lnk-azure-gateway-guidance]: iot-hub-guidance.md#field-gateways
[lnk-guidance-provisioning]: iot-hub-guidance.md#provisioning
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth

[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-guidance]: iot-hub-guidance.md
[lnk-getstarted-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

[lnk-amqp]: https://www.amqp.org/
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

<!---HONumber=AcomDC_0128_2016-->