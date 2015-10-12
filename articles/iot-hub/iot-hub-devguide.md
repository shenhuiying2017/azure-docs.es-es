<properties
 pageTitle="Guía del desarrollador del Centro de IoT de Azure | Microsoft Azure"
 description="Guía de desarrollador del Centro de IoT de Azure que abarca los extremos del Centro de IoT, seguridad, registro de identidad del dispositivo y mensajería"
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
 ms.date="09/08/2015"
 ms.author="elioda"/>

# Guía del desarrollador del Centro de IoT de Azure

El Centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y un back-end de aplicaciones.

El Centro de IoT de Azure permite:

* Comunicaciones seguras con las credenciales de seguridad de cada dispositivo y el control de acceso
* Una mensajería confiable de gran escala de dispositivo a nube y de nube a dispositivo, y
* Conectividad fácil del dispositivo con las bibliotecas de dispositivos para las plataformas y lenguajes más populares.

Estas son las áreas que se tratan en este documento:

- [Extremos](#endpoints). En esta sección se describen los diferentes extremos que expone cada Centro de IoT para las operaciones en tiempo de ejecución y de administración.
- [Registro de identidad del dispositivo](#identityregistry). En esta sección se describe qué información se almacena en cada registro de identidad del dispositivo del Centro de IoT y cómo se puede acceder a ella y modificarla.
- [Seguridad](#security). En esta sección se describe el modelo de seguridad para los dispositivos y componentes de nube que se usan para conceder acceso a la funcionalidad del Centro de IoT.
- [Mensajería](#messaging). En esta sección se describen las características de mensajería (dispositivo a nube y nube a dispositivo) que muestra el Centro de IoT.
- [Cuotas y limitación](#throttling). En esta sección se resumen las cuotas que se aplican al uso del Centro de IoT.

## Extremos <a id="endpoints"></a>

El Centro de IoT de Azure es un servicio multiinquilino y muestra funcionalidades a una variedad de actores. En la ilustración siguiente se muestran los diferentes extremos expuestos por el Centro de IoT de Azure.

![Extremos del Centro de IoT][img-endpoints]

Esta es una descripción de los extremos:

* **Proveedor de recursos**: el proveedor de recursos del Centro de IoT muestra una interfaz del [Administrador de recursos de Azure][lnk-arm] que permite a los propietarios de la suscripción de Azure crear Centros de IoT, actualizar las propiedades del Centro de IoT y eliminar Centros de IoT. Las propiedades del Centro de IoT controlan las directivas de seguridad de nivel de centro (a diferencia del control de acceso de nivel de dispositivo, consulte [Control de acceso](#accesscontrol)) y las opciones funcionales para la mensajería de nube a dispositivo y de dispositivo a nube. El proveedor de recursos también permite [Exportar identidades del dispositivo](#importexport).
* **Administración de identidades de dispositivo**: cada Centro de IoT muestra un conjunto de extremos HTTP REST para administrar identidades del dispositivo (es decir, crear, recuperar, actualizar y eliminar). Las identidades del dispositivo se usan para autenticación de dispositivos y control de acceso. Consulte [Registro de identidad del dispositivo](#identityregistry) para obtener más información.
* **Extremos de dispositivos**: para cada dispositivo aprovisionado en el registro de identidad del dispositivo, el Centro de IoT muestra un conjunto de extremos que se usan para comunicarse hacia y desde ese dispositivo. Actualmente se exponen estos extremos en HTTP y [AMQP][lnk-amqp]\:
    - *Envío de mensajes de dispositivo a nube*. Este extremo se usa para enviar mensajes de dispositivo a nube. Consulte [Mensajes de dispositivo a nube](#d2c) para obtener más información.
    - *Recepción de mensajes de nube a dispositivo*. Este extremo lo usa el dispositivo para recibir mensajes de nube a dispositivos dirigidos a él. Consulte [Mensajes de nube a dispositivo](#c2d) para obtener más información.
* **Extremos del servicio**: cada Centro de IoT también muestra un conjunto de extremos que usa el back-end de aplicaciones (*servicio*) para comunicarse con los dispositivos. Estos extremos se exponen usando solo el protocolo [AMQP][lnk-amqp].
    - *Recepción de mensajes de dispositivo a nube*. Este extremo es compatible con los [Centros de eventos de Azure][lnk-event-hubs] y puede usarse para leer todos los mensajes de dispositivo a nube enviados por los dispositivos. Consulte [Mensajes de dispositivo a nube](#d2c) para obtener más información.
    - *Envío de mensajes de nube a dispositivo y recepción de confirmaciones de entrega*. Estos extremos permiten al back-end de aplicaciones enviar mensajes confiables de nube a dispositivo y recibir las confirmaciones de entrega o expiración correspondientes. Consulte [Mensajes de nube a dispositivo](#c2d) para obtener más información.

El artículo [API y SDK del Centro de IoT][lnk-apis-sdks] describe las distintas formas a las que se puede acceder a estos extremos.

Finalmente es importante tener en cuenta que todos los extremos del Centro de IoT se exponen a través [TLS][lnk-tls] y ningún extremo se expone en canales sin cifrar o no seguros.

### Cómo interpretar los extremos compatibles con Centros de eventos. <a id="eventhubcompatible"></a>
Al usar [SDK de Bus de servicio de Azure para .NET](https://www.nuget.org/packages/WindowsAzure.ServiceBus) o [Centros de eventos - Host del procesador de eventos], puede usar cualquier cadena de conexión del Centro de IoT con los permisos correctos y después usar `messages/events` como nombre del Centro de eventos.

Cuando se usan SDK (o integraciones de productos) que no detectan el Centro de IoT, tiene que recuperar un extremo y un nombre de Centro de eventos compatibles con los Centros de eventos en la configuración del Centro de IoT en el [Portal de vista previa de Azure]\:

1. En la hoja del Centro de IoT, haga clic en **Configuración** y después e **Mensajería**,
2. En la sección **Configuración de dispositivo a nube**, encontrará un cuadro **Extremo compatible con el Centro de eventos**, **Nombre compatible con el Centro de eventos** y **Particiones**.

    ![][img-eventhubcompatible]

> [AZURE.NOTE]A veces el SDK requiere un **nombre de host** o un **espacio de nombres**; en ese caso, quite el esquema del **extremo compatible con Centro de eventos**. Por ejemplo, si el extremo compatible con Centro de eventos es `sb://iothub-ns-myiothub-1234.servicebus.windows.net/`, el **nombre de host** debe ser `iothub-ns-myiothub-1234.servicebus.windows.net` y el **espacio de nombres** debe ser `iothub-ns-myiothub-1234`.

Después puede usar cualquier directiva de seguridad de acceso compartido que tenga permisos **ServiceConnect** para conectarse con el Centro de eventos anterior.

En caso de que tenga que crear una cadena de conexión del Centro de eventos con la información anterior, puede usar el modelo siguiente:

        Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}


Esta es una lista de SDK y de integración que puede usara con el Centro de IoT:

* [Cliente de Centros de eventos de Java](https://github.com/hdinsight/eventhubs-client)
* [Spout de Apache Storm](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md), encontrará el vínculo al origen de spout [aquí](https://github.com/apache/storm/tree/master/external/storm-eventhubs).
* [Integración de Apache Spark](../hdinsight/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)

## Registro de identidad del dispositivo <a id="identityregistry"></a>
Cada Centro de IoT tiene un registro de identidad del dispositivo que se usa para crear recursos de cada dispositivo en el servicio, como una cola que contiene los mensajes de nube a dispositivo en curso, y permite el acceso a los extremos accesibles desde el dispositivo, tal como se explica en la sección [Control de acceso](#accesscontrol).

En un nivel superior el registro de identidad del dispositivo es una colección de RESTful de recursos de identidad de dispositivos. Las secciones siguientes detallan las propiedades de los recursos de identidad del dispositivo y las operaciones permitidas en las identidades por el registro.

> [AZURE.NOTE]Puede consultar [API y SDK del Centro de IoT][lnk-apis-sdks] para obtener más información sobre el protocolo HTTP y los SDK disponibles para interactuar con el registro de identidad del dispositivo.

### Propiedades de identidad de dispositivos <a id="deviceproperties"></a>
Las identidades de dispositivos se representan como documentos JSON con las propiedades siguientes.

| Propiedad | Opciones | Descripción |
| -------- | ------- | ----------- |
| deviceId | necesarias, de solo lectura en actualizaciones | Una cadena que distingue mayúsculas y minúsculas (de hasta 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':', '.', '+', '&percnt;', '_', '&num;', '&ast;', '?', '!', '(', ')', ',', '=', '&commat;', ';', '&dollar;', '''}`. |
| generationId | requerido, de solo lectura | Una cadena que distingue mayúsculas y minúsculas generada por el centro de hasta 128 caracteres. Se usa para distinguir dispositivos con el mismo **deviceId** cuando se eliminaron y se volvieron a crear. |
| ETag | requerido, de solo lectura | Una cadena que representa un valor de etag débil para la identidad del dispositivo, como por [RFC7232][lnk-rfc7232].|
| auth | opcional | Un objeto compuesto que contiene material de seguridad e información de autenticación. |
| auth.symkey | opcional | Un objeto compuesto que contiene claves principales y secundarias, almacenados en formato base64. |
| status | requerido | Puede ser **Habilitado** o **Deshabilitado**. Si está **habilitado**, el dispositivo se puede conectar. Si está **deshabilitado**, este dispositivo no puede acceder a ningún extremo accesible desde el dispositivo. |
| statusReason | opcional | Una cadena de 128 caracteres que almacena el motivo del estado de la identidad del dispositivo. Se permiten todos los caracteres UTF-8. |
| statusUpdateTime | solo lectura | Fecha y hora de la última actualización del estado. |
| connectionState | solo lectura | **Conectado** o **Desconectado**, representa la vista del Centro de IoT del estado de conexión del dispositivo. |
| connectionStateUpdatedTime | solo lectura | Fecha y hora de la última vez que se actualizó el estado de conexión. |
| lastActivityTime | solo lectura | Fecha y hora de la última vez que el dispositivo se conectó, recibió o envió un mensaje. |

> [AZURE.NOTE]El estado de la conexión solo puede representar la vista del Centro de IoT del estado de la conexión. Este estado se puede retrasar según las configuraciones y las condiciones de red.

### Operaciones de identidad de dispositivos
El registro de identidad del dispositivo del Centro de IoT de Azure muestra las operaciones siguientes:

* Crear la identidad del dispositivo
* Actualizar la identidad del dispositivo
* Recuperar la identidad del dispositivo mediante el identificador
* Eliminar la identidad del dispositivo
* Enumerar hasta 1.000 identidades

Todas las operaciones anteriores permiten usar la simultaneidad optimista, tal como se especifica en [RFC7232][lnk-rfc7232].

> [AZURE.IMPORTANT]La única manera de recuperar todas las identidades en un registro de identidades de un centro es usar la funcionalidad [Exportar](#importexport).

Los registros de identidad del dispositivo de un centro no contienen los metadatos de la aplicación, se puede acceder a ellos como un diccionario mediante **deviceId** como clave y no admiten consultas expresivas. Las soluciones de IoT tendrán un almacén específico de la solución con metadatos específicos de la aplicación, como la sala implementada para un sensor de temperatura en una solución para un edificio pequeño.

### Deshabilitación de dispositivos
Puede deshabilitar los dispositivos actualizando la propiedad **status** de una identidad en el registro. Normalmente se usa en dos escenarios:

1. Durante el proceso de orquestación de aprovisionamiento. Consulte [Guía del Centro de IoT de Azure - Aprovisionamiento][lnk-guidance-provisioning] para obtener más información.
2. Si por cualquier motivo considera que un dispositivo está en peligro o temporalmente no autorizado.

### Exportación de identidades de dispositivos <a id="importexport"></a>
Las exportaciones son trabajos de larga duración que usan un contenedor de blobs proporcionado por el cliente para leer y escribir los datos de identidad del dispositivo.

Puede exportar las identidades del dispositivo de forma masiva desde el registro de identidad de un Centro de IoT. Esto puede hacerse con operaciones asincrónicas en el extremo de proveedor de recursos del Centro de IoT de Azure ([Extremos](#endpoints)).

Estas son las operaciones que se pueden realizar en los trabajos de exportación:

* Crear un trabajo de exportación
* Recuperar el estado de un trabajo en ejecución
* Cancelar un trabajo en ejecución

> [AZURE.NOTE]Cada concentrador puede tener solo un único trabajo de ejecución en un momento dado.

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
| outputBlobContainerURI | necesarias para todos los trabajos | URI de Firma de acceso compartido de blob con acceso de escritura a un contenedor de blobs (consulte [Creación y uso de una firma de acceso compartido con el servicio BLOB][lnk-createuse-sas] y [Creación de una firma de acceso compartido en Java][lnk-sas-java]). Se usa para mostrar el estado del trabajo y los resultados. |
| includeKeysInExport | opcional | Si se incluyen las claves **true** en la salida de exportación; de lo contrario, las claves se exportan como null. Valor predeterminado: **false**. |
| failureReason | generadas por el sistema, omitidas durante la creación | Si el estado es **Con error**, una cadena contiene el motivo. |

#### Trabajos de exportación
Los trabajos de exportación toman, como parámetro, un identificador URI de Firma de acceso compartido que concede acceso de escritura a un contenedor de blobs. Se usa para mostrar el resultado de la operación.

Los resultados de la salida se escriben en el contenedor de blobs especificado en un archivo denominado `job_{job_id}_devices.txt`. Este archivo contendrá las identidades del dispositivo serializadas como JSON, tal como se especifica en [Propiedades de identidad de dispositivos](#deviceproperties). Los materiales de seguridad se establecerán en **null** en caso de que **includeKeysInExport** se establezca en **false**.

**Ejemplo**:

    {"deviceId":"devA","auth":{"symKey":{"primaryKey":"123"}},"status":"enabled"}
    {"deviceId":"devB","auth":{"symKey":{"primaryKey":"234"}},"status":"enabled"}
    {"deviceId":"devC","auth":{"symKey":{"primaryKey":"345"}},"status":"enabled"}
    {"deviceId":"devD","auth":{"symKey":{"primaryKey":"456"}},"status":"enabled"}


## Seguridad <a id="security"></a>

En esta sección se describen las opciones para proteger el Centro de IoT de Azure.

### Control de acceso <a id="accesscontrol"></a>
El Centro de IoT de Azure usa el siguiente conjunto de *permisos* para conceder acceso al extremo de cada centro. Los permisos limitan el acceso a un Centro de IoT basado en la funcionalidad.

* **RegistryRead**. Este permiso concede acceso de lectura al registro de identidad del dispositivo. Consulte [Registro de identidad del dispositivo](#identityregistry) para obtener más información.
* **RegistryReadWrite**. Este permiso concede acceso de lectura y escritura al registro de identidad del dispositivo. Consulte [Registro de identidad del dispositivo](#identityregistry) para obtener más información.
* **ServiceConnect**. Este permiso concede acceso a la comunicación accesible desde el servicio en la nube y a los extremos de supervisión. Por ejemplo, concede permiso a los servicios en la nube para recibir mensajes de dispositivo a nube, enviar mensajes de nube a dispositivo y recuperar las confirmaciones de entrega correspondientes.
* **DeviceConnect**. Este permiso concede acceso a los extremos de comunicación accesibles desde los dispositivos. Por ejemplo, concede permiso para enviar mensajes de dispositivo a nube y recibir mensajes de nube a dispositivo. Este permiso se usa principalmente por los dispositivos.

Se conceden permisos de las maneras siguientes:

* **Directivas de acceso compartido de nivel de centro**. *Las directivas de acceso compartido* pueden conceder cualquier combinación de los permisos enumerados anteriormente. Puede definir las directivas en el [Portal de administración de Azure][lnk-management-portal] o mediante programación usando las [API del proveedor de recursos del Centro de IoT de Azure][lnk-resource-provider-apis]. Un Centro de IoT recién creado tiene las siguientes directivas predeterminadas:
    - *iothubowner*: directiva con todos los permisos
    - *service*: directiva con el permiso **ServiceConnect**
    - *device*: directiva con el permiso **DeviceConnect**
    - *registryRead*: directiva con el permiso **RegistryRead**
    - *registryReadWrite*: directiva con los permisos **RegistryRead** y **RegistryWrite**
* **Credenciales de seguridad de cada dispositivo**. Cada Centro de IoT contiene un [registro de identidad del dispositivo](#identityregistry). Para cada dispositivo de este registro, puede configurar credenciales de seguridad que concedan permisos **DeviceConnect** orientados a los extremos del dispositivo correspondiente.

**Ejemplo**. En una solución de IoT, suele haber un componente de administración de dispositivos que usa la directiva *registryReadWrite*, además un componente de procesador de eventos y un componente de lógica de negocios de dispositivos en tiempo de ejecución que usan la directiva *service*. Los dispositivos individuales se conectan usando las credenciales almacenadas en el registro de identidad del centro.

Para obtener orientación sobre temas de seguridad del Centro de IoT, consulte la sección de seguridad de [Guía del Centro de IoT de Azure][lnk-guidance-security].

### Autenticación
El Centro de IoT de Azure concede acceso a los extremos mediante la comprobación de un token con las directivas de acceso compartido y las credenciales de seguridad del registro de identidad del dispositivo. Las credenciales de seguridad, como las claves simétricas, nunca se envían en la conexión.

> [AZURE.NOTE]El proveedor de recursos del Centro de IoT de Azure se protege mediante la suscripción de Azure, igual que todos los proveedores en el [Administrador de recursos de Azure][lnk-azure-resource-manager].

#### Formato del token de seguridad <a id="tokenformat"></a>
El token de seguridad tiene el formato siguiente:

        SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

Estos son los valores esperados:

| Valor | Descripción |
| ----- | ----------- |
| {signature} | Una firma de HMAC-SHA256 de la cadena: `{URL-encoded-resourceURI} + "\n" + expiry` |
| {resourceURI} | Prefijo URI (por segmento) de los extremos a los que se puede acceder con este token. Por ejemplo, `/events` |
| {expiry} | Cadenas UTF8 para el número de segundos transcurridos desde el tiempo 00:00:00 UTC el 1 de enero de 1970. |
| {URL-encoded-resourceURI} | Identificador URI codificado para URL (minúscula) |
| {policyName} | El nombre de la directiva de acceso compartido a la que hace referencia este token. Ausente en el caso de los token que hacen referencia a las credenciales del registro de dispositivos. |

**Nota sobre el prefijo**: el prefijo URI se calcula por segmento y no por carácter. Por ejemplo `/a/b` es un prefijo de `/a/b/c` pero *no* para `/a/bc`.

#### Detalles específicos de protocolo
Cada protocolo admitido (como HTTP y AMQP) transporta tokens de diferentes maneras.

La autenticación de HTTP se implementa mediante la inclusión de un token válido en el encabezado de solicitud **Autorización**. Un parámetro de consulta llamado **Authorization** también puede transportar el token.

Al usar [AMQP][lnk-amqp], el Centro de IoT de Azure admite [SASL PLAIN][lnk-sasl-plain] y [seguridad basada en notificaciones AMQP][lnk-cbs].

En el caso de la seguridad basada en notificaciones AMQP, la norma especifica cómo transmitir los tokens enumerados anteriormente.

Para SASL PLAIN, puede ser el nombre de usuario: * `{policyName}&commat;sas.root.{iothubName}` en el caso de tokens de nivel de centro. * `{deviceId}` en el caso de tokens centrados en el dispositivo.

En ambos casos el campo de contraseña contiene el token, tal como se describe en la sección [Formato de tokens](#tokenformat).

> [AZURE.NOTE]Los [SDK del Centro de IoT de Azure][lnk-apis-sdks] generan tokens automáticamente cuando se conectan al servicio. En algunos casos, los SDK están limitados por el protocolo que admiten o el método de autenticación disponible. Consulte la documentación de [SDK de Centro de IoT de Azure][lnk-apis-sdks] para obtener más información.

#### SASL PLAIN comparado con CBS
Cuando se usa SASL PLAIN, un cliente que se conecta a un Centro de IoT puede usar un token único para cada conexión TCP. Cuando el token expira, la conexión TCP se desconecta del servicio, desencadenando una reconexión. Este comportamiento, aunque no resulta problemático para un componente de back-end de aplicaciones, es muy perjudicial para una aplicación de dispositivo por los siguientes motivos:

*  Las puertas de enlace normalmente se conectan en nombre de muchos dispositivos. Cuando se usa SASL PLAIN, tienen que crear una conexión TCP distintiva para cada dispositivo que se conecta a un Centro de IoT. Esto aumenta considerablemente el consumo de energía y de recursos de red, y aumenta la latencia de cada conexión de dispositivo.
* Los dispositivos con recursos restringidos se verán afectados principalmente por el aumento del uso de recursos para volver a conectarse después de cada expiración del token.

### Control de las credenciales de nivel de centro
Puede restringir el ámbito de las directivas de seguridad de nivel de centro mediante la creación de tokens con un URI de recurso restringido. Por ejemplo, el extremo para enviar mensajes de dispositivo a nube desde un dispositivo es `/devices/{deviceId}/events`. También puede usar una directiva de acceso compartido de nivel de centro con permisos **DeviceConnect** para firmar un token cuyo resourceURI sea `/devices/{deviceId}`, creando un token que solo se pueda usar para enviar dispositivos en nombre del dispositivo **deviceId**.

Se trata de un mecanismo similar a la [directiva de edición de los Centros de eventos][lnk-event-hubs-publisher-policy] y permite la implementación de métodos de autenticación personalizados, tal como se explica en la sección de seguridad de la [Guía del Centro de IoT de Azure][lnk-guidance-security].

## Mensajería
El Centro de IoT de Azure proporciona primitivas de mensajería para comunicarse desde un back-end de aplicaciones (*servicio* o *nube*) a los dispositivos, y viceversa. Nos referiremos a estas funcionalidades como [Dispositivo a nube](#d2c) y [Nube a dispositivo](#c2d).

Las propiedades básicas de la funcionalidad de mensajería del Centro de IoT son la confiabilidad y durabilidad de los mensajes. Esto permite la resistencia a la conectividad intermitente en el dispositivo y a los picos de carga del procesamiento de eventos en la nube. El Centro de IoT implementa *al menos una vez* las garantías de entrega para las mensajerías D2C y C2D.

Centro de IoT admite varios protocolos accesibles desde los dispositivos (por ejemplo, AMQP y HTTP/1). Con el fin de admitir la interoperabilidad sin problemas entre protocolos, el Centro de IoT define un formato de mensaje común que es compatible con todos los protocolos accesibles desde el dispositivo.

### Formato de mensajes <a id="messageformat"></a>
Los mensajes del Centro de IoT constan de:

* Un conjunto de *propiedades del sistema*. Son las propiedades que se interpretan o se establecen por el Centro de IoT. Este conjunto es el predeterminado.
* Un conjunto de *propiedades de la aplicación*. Se trata de un diccionario de propiedades de cadena que la aplicación puede definir y acceder sin necesidad de deserializar el cuerpo. Estas propiedades no se modifican nunca por el Centro de IoT.
* Un cuerpo binario opaco.

Consulte [API y SDK del Centro de IoT][lnk-apis-sdks] para obtener más información sobre cómo se codifica el mensaje en distintos protocolos.

Es el conjunto de propiedades del sistema en los mensajes del Centro de IoT.

| Propiedad | Descripción |
| -------- | ----------- |
| MessageId | Un identificador de conjunto de usuarios para el mensaje, que normalmente se usa para patrones de solicitud y respuesta. Formato: una cadena que distingue mayúsculas y minúsculas (de hasta 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Número de secuencia | Un número (exclusivo para cada cola de dispositivo) asignado por el Centro de IoT a cada mensaje C2D. |
| Para | Se usa en [nube a dispositivo](#c2d) para el destino del mensaje.|
| ExpiryTimeUtc | Fecha y hora de la expiración del mensaje. |
| EnqueuedTime | Hora en la que el Centro de IoT se recibió el mensaje. |
| CorrelationId | Propiedad de cadena que normalmente contiene el identificador del mensaje de la solicitud en los patrones de solicitud y respuesta. |
| UserId | Se usa para especificar el origen de los mensajes. Cuando el Centro de IoT genera mensajes, se establece en `{iot hub name}`. |
| Ack | Se usa en los mensajes C2D para solicitar al Centro de IoT que genere mensajes de comentarios debido al consumo del mensaje por el dispositivo. Valores posibles: **none** (valor predeterminado): no se genera ningún mensaje de comentarios, **positive**: recibe un mensaje de comentarios si el mensaje expiró, **negative**: recibe un mensaje de comentarios si el mensaje expiró (o se alcanzó el número máximo de entregas) sin que se complete en el dispositivo y **full**: comentarios positivos y negativos. Para obtener más información, consulte [Comentarios de mensajes](#feedback). |
| ConnectionDeviceId | Establecido por el Centro de IoT en mensajes de D2C. Contiene el deviceId del dispositivo que envió el mensaje. |
| ConnectionDeviceGenerationId | Establecido por el Centro de IoT en mensajes de D2C. Contiene el generationId (como se indica en [Propiedades de identidad de dispositivos](#deviceproperties)) del dispositivo que envió el mensaje. |
| ConnectionAuthMethod | Establecido por el Centro de IoT en mensajes de D2C. Información sobre el método de autenticación usado para autenticar el dispositivo que envía el mensaje. Consulte [Contra la suplantación de D2C](#antispoofing) para obtener más información.|

### Elección del protocolo de comunicación <a id="amqpvshttp"></a>
El Centro de IoT admite los protocolos [AMQP][lnk-amqp] y HTTP/1 para las comunicaciones del dispositivo. Aquí se muestra una lista de las consideraciones con respecto a sus usos.

* **Patrón de nube a dispositivo**. HTTP/1 no cuenta con una forma eficaz de implementar la inserción de servidor. Se deduce que, cuando se usa HTTP/1, los dispositivos realizan un sondeo en el Centro de IoT de Azure para buscar mensajes de nube a dispositivo. Esto resulta muy ineficaz tanto para el dispositivo como para el Centro de IoT. Las directrices actuales, cuando se usa HTTP/1, consisten e establecer un intervalo de sondeo para cada dispositivo de menos de una vez cada 25 minutos. Por otro lado, AMQP admite la inserción de servidor al recibir mensajes de nube a dispositivo y lo que permite inserciones inmediatas de mensajes desde el Centro de IoT al dispositivo. Si le preocupa la latencia de entrega, es mucho mejor usar el protocolo AMQP. Por otro lado, para los dispositivos apenas conectados, HTTP/1 funciona también.
* **Puertas de enlace de campo**. Dadas las limitaciones de HTTP/1 con respecto a la inserción de servidor, no es adecuado usarlo en [escenarios de puerta de enlace de campo][lnk-azure-gateway-guidance].
* **Dispositivos de recursos bajos**. Las bibliotecas HTTP/1 son mucho menores que las de AMQP. Si se deduce que el dispositivo tiene pocos recursos (por ejemplo, menos de 1Mb de RAM), HTTP/1 puede ser la única implementación de protocolo disponible.
* **Cruce seguro de red**. El estándar AMQP escucha en el puerto 5672. Esto podría producir problemas en las redes que están cerradas para los protocolos que no son HTTP. Tenga en cuenta que esta limitación es temporal ya que el Centro de IoT implementará AMQP sobre WebSockets.
* **Tamaño de carga**. AMQP es un protocolo binario, que es mucho más compacto que HTTP/1.

En un nivel superior, se recomienda usar AMQP siempre que sea posible y usar HTTP/1 si los recursos del dispositivo o la configuración de red no admiten AMQP. Además, cuando se usa HTTP/1, la frecuencia de sondeo debe establecerse a menos de una vez cada 25 minutos para cada dispositivo. Ciertamente, durante el desarrollo, es aceptable disponer de frecuencias de sondeo más frecuentes.

Como consideración final, es importante hacer referencia a la [puerta de enlace del protocolo de IoT de Azure][lnk-azure-protocol-gateway], que le permite implementar una puerta de enlace MQTT de alto rendimiento que interactúa directamente con el Centro de IoT. MQTT admite la inserción de servidor (lo que permite la entrega inmediata de mensajes C2D al dispositivo) y está disponible para dispositivos con muy pocos recursos. La principal desventaja de este enfoque es el requisito de autohospedar y administrar una puerta de enlace de protocolo.

### Dispositivo a nube <a id="d2c"></a>
Tal como se detalla en la sección [Extremos](#endpoints), los mensajes de dispositivo a nube se envían a través de un extremo del dispositivo (en particular `/devices/{deviceId}/messages/events`) y se reciben a través de un extremo accesible desde el servicio (`/messages/events`), que es compatible con [Centros de eventos][lnk-event-hubs]. Por lo tanto, puede usar la integración de los Centros de eventos estándar y los SDK para recibir mensajes.

El Centro de IoT de Azure implementa la mensajería de dispositivo a la nube de manera muy similar a los [Centros de eventos][lnk-event-hubs], siendo los mensajes de dispositivo a nube del Centro de IoT más similares a los *eventos* de los Centros de eventos que los [mensajes][lnk-servicebus] del *Bus de servicio*.

Esto tiene las implicaciones siguientes:

* De forma similar a los *eventos* de los Centros de eventos, los mensajes de dispositivo a nube son duraderos y se conservan en un Centro de IoT hasta 7 días (consulte [Opciones de configuración de dispositivo a nube](#d2cconfiguration)).
* Los mensajes de dispositivo a nube se fragmentan en un conjunto fijo de particiones que se establece en el tiempo de creación (consulte [Opciones de configuración de dispositivo a nube](#d2cconfiguration)).
* De igual forma que los Centros de eventos, los clientes que leen mensajes de dispositivo a nube tienen que administrar las particiones y puntos de control, consulte [Centros de eventos - Consumo de eventos][lnk-event-hubs-consuming-events].
* Como los eventos de los Centros de eventos, los mensajes de dispositivo a nube pueden tener como máximo 256 Kb y se pueden agrupar en lotes para optimizar los envíos. Los lotes pueden tener un tamaño máximo de 256 Kb y como máximo 500 mensajes.

Sin embargo, hay algunas diferencias importantes entre los mensajes de dispositivo a nube del Centro de IoT y los Centros de eventos:

* Como se explica en la sección [Seguridad](#security), el Centro de IoT permite la autenticación por dispositivo y el control de acceso.
* El Centro de IoT permite millones de dispositivos conectados simultáneamente (consulte [Cuotas y limitación](#throttling)), mientras que los Centros de eventos se limitan a 5.000 conexiones AMQP por espacio de nombres.
* El Centro de IoT no permite el particionamiento arbitrario mediante una **PartitionKey**. Los mensajes de dispositivo a nube se dividen en particiones con respecto a su **deviceId** de origen.
* El escalado del Centro de IoT es ligeramente diferente al de los Centros de eventos; consulte la sección [Escala][lnk-guidance-scale] de la Guía del Centro de IoT de Azure para obtener más información.

Tenga en cuenta que lo anterior no significa que el Centro de IoT sustituya a los Centros de eventos en todas las situaciones. Por ejemplo, en algunos cálculos de procesamiento de eventos, podría ser necesaria para volver a crear particiones de eventos con respecto a un campo o propiedad diferentes antes de analizar los flujos de datos. En ese caso, un Centro de eventos podría usarse para desacoplar las dos partes de la canalización de procesamiento de la transmisión.

Para obtener información detallada sobre cómo usar la mensajería de dispositivo a nube, consulte [API y SDK del Centro de IoT][lnk-apis-sdks].

#### Tráfico sin telemetría
En muchos casos, los dispositivos envían no solo puntos de datos de telemetría al back-end de aplicaciones, sino también mensajes y solicitudes *interactivos* que requieren la ejecución y el control de la capa de lógica de negocio de la aplicación. Unos buenos ejemplos son las alertas críticas que tienen que desencadenar una acción específica en el back-end o el dispositivo que responde a los comandos.

Consulte la sección [Procesamiento de dispositivo a nube][lnk-guidance-d2c-processing] de la Guía del Centro de IoT para obtener más información sobre la mejor manera de procesar este tipo de mensajes.

#### Opciones de configuración de dispositivo a nube <a id="d2cconfiguration"></a>
Un Centro de IoT expone las propiedades siguientes para controlar la mensajería de D2C.

* **Número de particiones**. Esta propiedad puede establecerse durante la creación y define el número de particiones para la recopilación de eventos de D2C.
* **Tiempo de retención**. Esta propiedad especifica el tiempo de retención para los mensajes de dispositivo a nube. El valor predeterminado es de 1 día, pero se puede aumentar hasta 7 días.

Además, de forma análoga a los Centros de eventos, el Centro de IoT permite la administración de los grupos de consumidores en el extremo de recepción del dispositivo a nube.

Todas las propiedades anteriores se pueden modificar a través del [Portal de administración de Azure][lnk-management-portal], o mediante programación a través de [Centro de IoT de Azure - API de proveedor de recursos][lnk-resource-provider-apis].

#### Propiedades contra la suplantación <a id="antispoofing"></a>
Para evitar la suplantación de identidad en los mensajes de dispositivo a nube, el Centro de IoT marca todos los mensajes con las siguientes propiedades:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Las dos primeras contienen los valores **deviceId** y **generationId** del dispositivo de origen, tal como se indicó en [Propiedades de identidad de dispositivos](#deviceproperties). La propiedad **ConnectionAuthMethod** contiene un objeto JSON serializado con las siguientes propiedades:

    {
        "scope": "{ hub | device}",
        "type": "{ symkey | sas}",
        "issuer": "iothub"
    }

### Nube a dispositivo <a id="c2d"></a>
Como ya se indicó en la sección [Extremos](#endpoints), los mensajes de nube a dispositivo se envían a través de un extremo conectado al servicio (`/messages/devicebound`) y se reciben a través de series de extremos accesibles desde el dispositivo (`/devices/{deviceId}/messages/devicebound`).

Cada mensaje de nube a dispositivo está destinado a un único dispositivo, estableciendo la propiedad **to** en `/devices/{deviceId}/messages/devicebound`.

**Importante**: cada cola de dispositivos puede contener como máximo 50 mensajes de nube a dispositivo. Si se intenta enviar más mensajes al mismo dispositivo, se producirá un error.

#### Ciclo de vida de los mensajes <a id="message lifecycle"></a>
Para implementar *al menos una vez* la garantía de entrega, los mensajes de nube a dispositivo se almacenan en colas por dispositivo y los dispositivos tienen que reconocer explícitamente la *finalización* para que el Centro de IoT pueda quitarlos de la cola. Esto garantiza la resistencia frente a errores de dispositivo y de conectividad.

En la siguiente ilustración se detalla el gráfico de estado del ciclo de vida de un mensaje de nube a dispositivo.

![Ciclo de vida de los mensajes de nube a dispositivo][img-lifecycle]

Cuando el servicio envía un mensaje, se considera que está *En cola*. Cuando un dispositivo desea *recibir* un mensaje, el Centro de IoT *bloquea* el mensaje (estado **Invisible**), con el fin de permitir a otros subprocesos del mismo dispositivo empezar a recibir otros mensajes. Cuando el subproceso de un dispositivo haya finalizado el procesamiento, lo notifica al Centro de IoT *finalizando* el mensaje. También puede decidir *rechazar* el mensaje, que se envía a un estado **Procesado como correo devuelto** o *Abandonar* el mensaje, que lo pone de nuevo en la cola (estado **Puesto en cola**).

Dado que podría producirse un error en un subproceso al procesar un mensaje sin notificar al Centro de IoT, los mensajes pasarán automáticamente de **Invisible** a **Puesto en cola** después de un *tiempo de espera de visibilidad (o bloqueo)* (valor predeterminado: 1 minuto). Puede pasar un mensaje entre los estados **Puesto en cola** e **Invisible** para un número especificado de veces en la propiedad *max delivery count* en Centro de IoT. Después de ese número de transiciones, el mensaje se procesará automáticamente como correo devuelto. De forma similar, el mensaje se procesará automáticamente como correo devuelto después de su fecha de expiración (consulte [Período de vida](#ttl)).

Consulte el tutorial [Introducción a los mensajes de nube a dispositivo de Centro de IoT][lnk-getstarted-c2d-tutorial] para obtener información sobre los mensajes de nube a dispositivo y [API y SDK del Centro de IoT][lnk-apis-sdks] para obtener información sobre cómo exponen las distintas API y SDK la funcionalidad de nube a dispositivo.

> [AZURE.NOTE]Normalmente los mensajes de nube a dispositivo se deben completar siempre que la pérdida del mensaje no afecte a la lógica de aplicación. Esto puede ocurrir en varios escenarios diferentes, por ejemplo, el contenido del mensaje se conservó correctamente en el almacenamiento local, o una operación se ejecutó correctamente o el mensaje transporta información temporal cuya pérdida no afecta a la funcionalidad de la aplicación. A veces, en el caso de las tareas de larga ejecución, es común completar el mensaje de nube a dispositivo después que la descripción de la tarea esté persistente en el almacenamiento local y, a continuación, notificar el back-end de aplicaciones con uno o varios mensajes de dispositivo a nube en diferentes fases de progreso de la tarea.

#### Período de vida <a id="ttl"></a>
Cada mensaje de nube a dispositivo tiene una fecha de expiración. Se puede establecer explícitamente por el servicio (en la propiedad **ExpiryTimeUtc**), o la establece el Control de IoT usando el período de vida predeterminado especificado como una propiedad del Centro de IoT; consulte [Opciones de configuración de nube a dispositivo](#c2dconfiguration).

#### Comentarios de mensaje <a id="feedback"></a>
Al enviar un mensaje de nube a dispositivo, el servicio puede solicitar la entrega de los comentarios de cada mensaje en relación con el estado final de ese mensaje. Al establecer la propiedad **Ack** en **positive**, el Centro de IoT generará un mensaje de comentarios únicamente si el mensaje de nube a dispositivo alcanza el estado **Completado**. Al establecer la propiedad **Ack** en **negative**, el Centro de IoT generará un mensaje de comentarios únicamente si el mensaje de nube a dispositivo alcanza el estado **Procesado como correo devuelto**. Al establecer la propiedad **Ack** en **full**, el Centro de IoT generará un mensaje de comentarios en cualquier caso.

Tal como se detalla en [Extremos](#endpoints), los comentarios se entregan mediante un extremo accesible desde el servicio (`/messages/servicebound/feedback`) como mensajes. La semántica de recepción de comentarios es la misma que para los mensajes de nube a dispositivo con el mismo [ciclo de vida del mensaje](#message lifecycle). Siempre que sea posible, los comentarios del mensaje se realizan por lotes en un único mensaje, con el formato siguiente.

Cada mensaje recuperado desde el extremo de comentarios tiene las siguientes propiedades.

| Propiedad | Descripción |
| -------- | ----------- |
| EnqueuedTime | Marca de tiempo de la creación del lote. |
| UserId | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

El cuerpo es una matriz serializada de JSON de registros, cada uno con las siguientes propiedades:

| Propiedad | Descripción |
| -------- | ----------- |
| EnqueuedTime | Marca de tiempo de cuando se produjo el resultado del mensaje. Por ejemplo, el dispositivo completado o el mensaje expirado. |
| CorrelationId | **MessageId** del mensaje de nube a dispositivo al que pertenece esta información de comentarios. |
| StatusCode | **0** si es correcto, **1** si el mensaje expiró, **2** si se supera el número máximo de entregas, **3** si se rechazó el mensaje. |
| Descripción | Los valores de cadena para los resultados anteriores. |
| DeviceId | **DeviceId** del dispositivo de destino del mensaje de nube a dispositivo al que pertenece este elemento de comentarios. |
| DeviceGenerationId | **DeviceGenerationId** del dispositivo de destino del mensaje de nube a dispositivo al que pertenece este elemento de comentarios. |

**Importante**. El servicio tiene que especificar un **MessageId** para el mensaje de nube a dispositivo para poder correlacionar sus comentarios con el mensaje original.

**Ejemplo**. Este es un cuerpo de ejemplo de un mensaje de comentarios.

    [
        {
            "CorrelationId": "0987654321",
            "EnqueuedTime": "2015-07-28T16:24:48.789Z",
            "StatusCode": "0",
            "Description": "Success",
            "DeviceId": "123",
            "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
        },
        {
            ...
        },
        ...
    ]

#### Opciones de configuración de nube a dispositivo <a id="c2dconfiguration"></a>
Cada Centro de IoT expone las siguientes opciones de configuración para la mensajería de nube a dispositivo.

| Propiedad | Descripción | Intervalo y valor predeterminado |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | TTL predeterminado para los mensajes de nube a dispositivo. | Intervalo de ISO\_8601 hasta 2D (1 minuto como mínimo). Valor predeterminado: 1 hora. |
| maxDeliveryCount | Número máximo de entregas para las colas de nube a dispositivo por dispositivo. | De 1 a 100. Valor predeterminado: 10 |
| feedback.ttlAsIso8601 | Retención de mensajes de comentarios del límite de servicio. | Intervalo de ISO\_8601 hasta 2D (1 minuto como mínimo). Valor predeterminado: 1 hora. |
| feedback.maxDeliveryCount | Número máximo de entregas para la cola de comentarios. | De 1 a 100. Valor predeterminado: 100. |


## Cuotas y limitación <a id="throttling"></a>
Cada suscripción de Azure puede tener como máximo 10 Centros de IoT.

Cada Centro de IoT se haya aprovisionado con un determinado número de unidades en una unidad de almacenamiento específico (para obtener más información, consulte [Precios del Centro de IoT de Azure][lnk-pricing]). La unidad de almacenamiento y el número de unidades determinan la cuota máxima de diaria de mensajes que se pueden enviar, así como el número máximo de identidades del dispositivo en el registro de identidad. El número de dispositivos conectados de forma simultánea está limitado por el número de identidades en el registro.

También determinan los valores de limitación que el Centro de IoT aplicará a las operaciones.

### Cuota de registro de identidad del dispositivo
El Centro de IoT permitirá a lo sumo 1.100 actualizaciones del dispositivo (crear, actualizar y eliminar) por unidad (con independencia de la unidad de almacenamiento) por día.

### Limitaciones de operación
Las limitaciones de operación son las limitaciones de velocidad que se aplican en los intervalos de minutos y están diseñadas para evitar abusos. El Centro de IoT intenta evitar los errores de devolución siempre que sea posible, pero iniciará la excepción de devolución si se infringe la limitación durante demasiado tiempo. A continuación se muestra en la lista de las limitaciones aplicadas. Los valores hacen referencia a un centro individual.

| Limitación | Valor por centro |
| -------- | ------------- |
| Las operaciones de registro de identidades (crear, recuperar, enumerar, actualizar y eliminar), importación y exportación masiva o individual | 100/min/unidad, hasta 5.000/min |
| Conexiones de dispositivos | 100/s/unidad |
| Envíos D2C | 2000/min/unidad (para S2), 60/min/unidad (para S1). Mínimo de 100/s. |
| Operaciones de C2D (envíos, recepción, comentarios) | 100/min/unidad |

**Nota**. En cualquier momento, es posible aumentar las cuotas o las limitaciones si aumentan las unidades aprovisionadas en un Centro de IoT.

## Pasos siguientes

Ya vio información general sobre el desarrollo del Centro de IoT, siga estos vínculos para obtener más información:

- [Introducción a los Centros de IoT (Tutorial)][lnk-get-started]
- [Compatibilidad de hardware y de plataformas de sistema operativo][lnk-compatibility]
- [Planeación de la implementación de IoT][lnk-guidance]

[Centros de eventos - Host del procesador de eventos]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx

[Portal de vista previa de Azure]: https://ms.portal.azure.com

[img-summary]: media/iot-hub-devguide/summary.png
[img-endpoints]: media/iot-hub-devguide/endpoints.png
[img-lifecycle]: media/iot-hub-devguide/lifecycle.png
[img-eventhubcompatible]: media/iot-hub-devguide/eventhubcompatible.png

[lnk-compatibility]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-azure-hub-sdks]: https://msdn.microsoft.com/library/mt488521.aspx
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-reference-architecture]: iot-hub-what-is-azure-iot.md

[lnk-azure-gateway-guidance]: iot-hub-guidance.md#fieldgateways
[lnk-guidance-provisioning]: iot-hub-guidance.md#provisioning
[lnk-guidance-scale]: iot-hub-guidance.md#scale
[lnk-guidance-security]: iot-hub-guidance.md#customauth

[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-guidance]: iot-hub-guidance.md
[lnk-getstarted-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

[lnk-amqp]: https://www.amqp.org/
[lnk-arm]: https://azure.microsoft.com/es-ES/documentation/articles/resource-group-overview/
[lnk-azure-resource-manager]: https://azure.microsoft.com/es-ES/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-createuse-sas]: https://azure.microsoft.com/es-ES/documentation/articles/storage-dotnet-shared-access-signature-part-2/
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-event-hubs]: http://azure.microsoft.com/services/event-hubs/
[lnk-event-hubs-consuming-events]: https://azure.microsoft.com/es-ES/documentation/articles/event-hubs-programming-guide/#consume-events-event-consumers
[lnk-guidance-d2c-processing]: iot-hub-csharp-csharp-process-d2c.md
[lnk-management-portal]: https://portal.azure.com
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-sas-java]: https://msdn.microsoft.com/es-ES/library/azure/Hh875756.aspx
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-servicebus]: http://azure.microsoft.com/services/service-bus/
[lnk-tls]: https://tools.ietf.org/html/rfc5246

<!---HONumber=Oct15_HO1-->