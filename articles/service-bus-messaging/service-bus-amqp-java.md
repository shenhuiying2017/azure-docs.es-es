<properties 
    pageTitle="Bus de servicio y Java con AMQP 1.0 | Microsoft Azure"
    description="Uso del Bus de servicio desde Java con AMQP"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/29/2016"
    ms.author="sethm" />


# <a name="use-service-bus-from-java-with-amqp-1.0"></a>uso del Bus de servicio desde Java con AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Java Message Service (JMS) es una API estándar que funciona con middleware orientado a mensajes en la plataforma Java. El Bus de servicio de Microsoft Azure se ha probado con la biblioteca de cliente JMS basada en AMQP 1.0 desarrollada por el proyecto Apache Qpid. Esta biblioteca es compatible con la API de JMS 1.1 completa y puede usarse con cualquier servicio de mensajería compatible con AMQP 1.0. Este escenario también se admite en [Service Bus para Windows Server](https://msdn.microsoft.com/library/dn282144.aspx) (Service Bus local). Para obtener más información, consulte [AMQP de Service Bus para Windows Server][].

## <a name="download-the-apache-qpid-amqp-1.0-jms-client-library"></a>Descarga de la biblioteca de cliente Apache Qpid JMS AMQP 1.0

Para obtener información acerca de dónde descargar la versión más reciente de la biblioteca de cliente Apache Qpid JMS AMQP 1.0, visite [http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html](http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html).

Debe agregar los cuatro archivos JAR siguientes del archivo de distribución de Apache Qpid JMS AMQP 1.0 al CLASSPATH de Java cuando vaya a crear y ejecutar aplicaciones JMS con el Bus de servicio:

-   geronimo-jms\_1.1\_spec-[version].jar

-   qpid-amqp-1-0-client-[version].jar

-   qpid-amqp-1-0-client-jms-[version].jar

-   qpid-amqp-1-0-common-[version].jar

## <a name="work-with-service-bus-queues,-topics,-and-subscriptions-from-jms"></a>Trabajo con colas, temas y suscripciones del Bus de servicio desde JMS

### <a name="java-naming-and-directory-interface-(jndi)"></a>Interfaz de denominación y directorio Java (JNDI)

JMS usa la interfaz de denominación y directorio Java (JNDI) para crear una separación entre los nombres lógicos y los físicos. Se resuelven dos tipos de objetos JMS usando JNDI: **ConnectionFactory** y **Destination**. JNDI usa un modelo de proveedor al que se pueden acoplar diferentes servicios de directorio para controlar labores de resolución de nombres. La biblioteca Apache Qpid JMS AMQP 1.0 se presenta con un proveedor JNDI basado en un archivo de propiedades sencillas que se configura mediante un archivo de texto:

El proveedor JNDI del archivo de propiedades de Qpid se configura mediante un archivo de propiedades del formato siguiente:

```
# servicebus.properties – sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCONNECTIONFACTORY = amqps://[username]:[password]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
topic.TOPIC = topic1
queue.QUEUE = queue1
```

#### <a name="configure-the-connection-factory"></a>Configuración del generador de conexiones

La entrada usada para definir **ConnectionFactory** en el proveedor JNDI de archivo de propiedades Qpid tiene el formato siguiente:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Donde `[jndi\_name]` y `[ConnectionURL]` tienen los significados siguientes:

| Nombre            | Significado                                                                                                                                    |   |   |   |   |
|-----------------|--------------------------------------------------------------------------------------------------------------------------------------------|---|---|---|---|
| `[jndi\_name]`    | El nombre lógico del generador de conexiones. Este nombre se resuelve en la aplicación Java mediante el método `IntialContext.lookup()` JNDI. |   |   |   |   |
| `[ConnectionURL]` | Una URL que proporciona la biblioteca JMS con la información necesaria para el agente AMQP.                                                      |   |   |   |   |

El formato de la dirección URL de conexión es el siguiente:

```
amqps://[username]:[password]@[namespace].servicebus.windows.net
```

Donde `[namespace]`, `[username]` y `[password]` tienen los significados siguientes:

| Nombre          | Significado                                                                        |   |   |   |   |
|---------------|--------------------------------------------------------------------------------|---|---|---|---|
| `[namespace]` | El espacio de nombres de Service Bus obtenido de [Azure Portal][].                      |   |   |   |   |
| `[username]`  | El nombre de clave de Service Bus obtenido de [Azure Portal][].                    |   |   |   |   |
| `[password]`  | Formulario codificado como URL de la clave SAS de Service Bus obtenida de [Azure Portal][]. |   |   |   |   |

> [AZURE.NOTE] debe codificar la contraseña manualmente como dirección URL. Podrá encontrar una práctica utilidad de codificación de la URL en [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).

Por ejemplo, si la información obtenida del portal es la siguiente:

| Espacio de nombres:   | test.servicebus.windows.net                  |
|--------------|----------------------------------------------|
| Nombre del emisor: | RootManageSharedAccessKey                                        |
| Clave del emisor:  | abcdefg |

A continuación, para definir un objeto **ConnectionFactory** denominado `SBCONNECTIONFACTORY`, la cadena de configuración sería la siguiente:

```
connectionfactory.SBCONNECTIONFACTORY = amqps://RootManageSharedAccessKey:abcdefg@test.servicebus.windows.net
```

#### <a name="configure-destinations"></a>Configuración de destinos

La entrada que define un destino en el proveedor JNDI de archivo de propiedades Qpid tiene el formato siguiente:

```
queue.[jndi_name] = [physical_name]
topic.[jndi_name] = [physical_name]
```

Donde `[jndi\_name]` y `[physical\_name]` tienen los significados siguientes:

| Nombre              | Significado                                                                                                                                  |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| `[jndi\_name]`    | El nombre lógico del destino. Este nombre se resuelve en la aplicación Java mediante el método `IntialContext.lookup()` JNDI. |
| `[physical\name]` | El nombre de la entidad del Bus de servicio a la que la aplicación envía mensajes o de la que los recibe.                                                  |

Tenga en cuenta lo siguiente:

- El valor `[physical\name]` puede ser una cola o un tema del Bus de servicio.
- al recibir de una suscripción al tema del Bus de servicio, el nombre físico especificado en JNDI debe ser el nombre del tema. El nombre de la suscripción se proporciona cuando la suscripción duradera se crea en el código de aplicación JMS.
- También es posible tratar una suscripción del tema del Bus de servicio como una cola de JMS. Este enfoque tiene varias ventajas: se puede usar el mismo código de receptor para colas y suscripciones de tema y toda la información de dirección (los nombres de tema y suscripción) se externaliza en el archivo de propiedades.
- Para tratar una suscripción de tema del Bus de servicio como una cola de JMS, la entrada del archivo de propiedades debe tener el formato siguiente: `queue.[jndi\_name] = [topic\_name]/Subscriptions/[subscription\_name]`.|

Para definir un destino JMS lógico con nombre "TOPIC" que se asigna a un tema del Bus de servicio llamado "topic1", la entrada del archivo de propiedades sería la siguiente:

```
topic.TOPIC = topic1
```

### <a name="send-messages-using-jms"></a>Envío de mensajes mediante JMS

El código siguiente muestra cómo enviar un mensaje a un tema del Bus de servicio. Se supone que `SBCONNECTIONFACTORY` y `TOPIC` se definen en un archivo de configuración **servicebus.properties** como se describe en la sección anterior.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
 
InitialContext context = new InitialContext(env); 
 
ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
MessageProducer producer = session.createProducer(topic);
TextMessage message = session.createTextMessage("This is a text string"); 
producer.send(message);
```

### <a name="receive-messages-using-jms"></a>Recepción de mensajes mediante JMS

El siguiente código muestra `how` para recibir un mensaje de una suscripción de tema del Bus de servicio. Se supone que `SBCONNECTIONFACTORY` y TOPIC se definen en un archivo de configuración **servicebus.properties** como se describe en la sección anterior. También se supone que el nombre de la suscripción es `subscription1`.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
 
InitialContext context = new InitialContext(env);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
TopicSubscriber subscriber = session.createDurableSubscriber(topic, "subscription1");
connection.start();
Message message = messageConsumer.receive();
```

### <a name="guidelines-for-building-robust-applications"></a>Directrices para crear aplicaciones sólidas

La especificación JMS define el contrato de excepción de los métodos de la API y el código de la aplicación debe escribirse para controlar dichas excepciones. Estos son algunos otros puntos a tener en cuenta en relación con el control de excepciones:

-   Registre un **ExceptionListener** con la conexión de JMS mediante **connection.setExceptionListener**. Esto permite al cliente recibir una notificación de un problema de forma asincrónica. Esta notificación es especialmente importante para las conexiones que solo consumen mensajes, ya que no poseen ninguna otra manera de saber que su conexión provocó un error. Se llama a **ExceptionListener** si hay un problema con la conexión, sesión o vínculo AMQP subyacente. En esta situación, el programa de aplicación debería volver a crear los objetos **JMS Connection**, **Session**, **MessageProducer** y **MessageConsumer** desde el principio.

-   Para comprobar que se envió correctamente un mensaje desde un **MessageProducer** a una entidad de Service Bus, asegúrese de que la aplicación se configuró con el conjunto de propiedades del sistema **qpid.sync\_publish**. Para ello, inicie el programa con la opción de Java VM **-Dqpid.sync\_publish=true** definida en la línea de comandos al iniciar la aplicación. El establecimiento de esta opción configura la biblioteca para que no devuelva la llamada de envío hasta que se reciba la confirmación de que el mensaje lo ha aceptado el Bus de servicio. Si se produce un problema durante la operación de envío, se genera una **JMSException**. Hay dos causas posibles: 
    1. Si el problema se debe al rechazo de Service Bus del mensaje específico que se va a enviar, se generará una excepción **MessageRejectedException**. Este error es transitorio o se genera por algún problema con el mensaje. El curso de acción recomendado es realizar varios intentos de reintentar la operación con cierta lógica de interrupción. Si el problema persiste, el mensaje debe abandonarse con un error registrado localmente. No es necesario volver a crear los objetos **JMS Connection**, **Session** o **MessageProducer** en esta situación. 
    2. Si el problema se debe al cierre de Service Bus del vínculo AMQP, se generará una excepción **InvalidDestinationException**. Esto puede deberse a un problema temporal o porque la entidad del mensaje que se va a eliminar. En cualquier caso, se deben volver a crear los objetos **JMS Connection**, **Session** y **MessageProducer** Si la condición de error era temporal, esta operación finalmente se realizará de manera correcta. Si se eliminó la entidad, el error será permanente.

## <a name="messaging-between-.net-and-jms"></a>Mensajería entre .NET y JMS

### <a name="message-bodies"></a>Cuerpos de los mensajes

JMS define cinco tipos de mensajes distintos: **BytesMessage**, **MapMessage**, **ObjectMessage**, **StreamMessage** y **TextMessage**. La API de .NET de Service Bus tiene un tipo de mensaje único, [BrokeredMessage][].

#### <a name="jms-to-service-bus-.net-api"></a>JMS a la API de .NET del Bus de servicio

Las secciones siguientes muestran cómo consumir los mensajes de cada uno de los tipos de mensaje JMS desde .NET. No se incluye ningún ejemplo de **ObjectMessage** porque el cuerpo de un **ObjectMessage** contiene un objeto serializable en el lenguaje de programación Java, que no se puede interpretar con una aplicación .NET.

##### <a name="bytesmessage"></a>BytesMessage

El código siguiente muestra cómo consumir el cuerpo de un objeto **BytesMessage** mediante las API de .NET de Service Bus.

```
Stream stream = message.GetBody<Stream>();
int streamLength = (int)stream.Length;

byte[] byteArray = new byte[streamLength];
stream.Read(byteArray, 0, streamLength);

Console.WriteLine("Length = " + streamLength);
for (int i = 0; i < stream.Length; i++)
{
  Console.Write("[" + (sbyte) byteArray[i] + "]");
}
```

##### <a name="mapmessage"></a>MapMessage

El código siguiente muestra cómo consumir el cuerpo de un objeto **MapMessage** mediante las API de .NET de Service Bus. Este código recorre en iteración los elementos de mapa, y muestra el nombre y el valor de cada elemento.

```
Dictionary<String, Object> dictionary = message.GetBody<Dictionary<String, Object>>();

foreach (String mapItemName in dictionary.Keys)
{
  Object mapItemValue = null;
  if (dictionary.TryGetValue(mapItemName, out mapItemValue))
  {
    Console.WriteLine(mapItemName + ":" + mapItemValue);
  }
}
```

##### <a name="streammessage"></a>StreamMessage

El código siguiente muestra cómo consumir el cuerpo de un objeto **StreamMessage** mediante las API de .NET de Service Bus. Este código muestra cada uno de los elementos de la secuencia, junto con sus tipos.

```
List<Object> list = message.GetBody<List<Object>>();

foreach (Object item in list)
{
  Console.WriteLine(item + " (" + item.GetType() + ")");
}
```

##### <a name="textmessage"></a>TextMessage

El código siguiente muestra cómo consumir el cuerpo de un objeto **TextMessage** mediante las API de .NET de Service Bus. Este código muestra la cadena de texto contenida en el cuerpo del mensaje.

```
Console.WriteLine("Text: " + message.GetBody<String>());
```

#### <a name="service-bus-.net-apis-to-jms"></a>API de .NET del Bus de servicio a JMS

Las secciones siguientes muestran cómo puede una aplicación .NET crear un mensaje que se recibe en JMS en cada uno de los distintos tipos de mensaje de JMS. No se incluye ningún ejemplo de **ObjectMessage** porque el cuerpo de un **ObjectMessage** contiene un objeto serializable en el lenguaje de programación Java, que no se puede interpretar con una aplicación .NET.

##### <a name="bytesmessage"></a>BytesMessage

El código siguiente muestra cómo crear un objeto [BrokeredMessage][] en .NET que recibe un cliente JMS como **BytesMessage**.

```
byte[] bytes = { 33, 12, 45, 33, 12, 45, 33, 12, 45, 33, 12, 45 };
message = new BrokeredMessage(bytes);
```

##### <a name="streammessage"></a>StreamMessage

El código siguiente muestra cómo crear un objeto [BrokeredMessage][] en .NET que recibe un cliente JMS como **StreamMessage**.

```
List<Object> list = new List<Object>();
list.Add("String 1");
list.Add("String 2");
list.Add("String 3");
list.Add((double)3.14159);
message = new BrokeredMessage(list);
```

##### <a name="textmessage"></a>TextMessage

El código siguiente muestra cómo consumir el cuerpo de **TextMessage** mediante la API de .NET de Service Bus. Este código muestra la cadena de texto contenida en el cuerpo del mensaje.

```
message = new BrokeredMessage("this is a text string");
```

### <a name="application-properties"></a>Propiedades de la aplicación

####<a name="jms-to-service-bus-.net-apis"></a>JMS a API de .NET del Bus de servicio

Los mensajes JMS admiten propiedades de la aplicación de los siguientes tipos: **boolean**, **byte**, **short**, **int**, **long**, **float**, **double** y **String**. El código Java siguiente muestra cómo establecer las propiedades en un mensaje cada uno de estos tipos de propiedades.

```
message.setBooleanProperty("TestBoolean", true); 
message.setByteProperty("TestByte", (byte) 33); 
message.setDoubleProperty("TestDouble", 3.14159D); 
message.setFloatProperty("TestFloat", 3.13159F); 
message.setIntProperty("TestInt", 100); 
message.setStringProperty("TestString", "Service Bus");
```

En las API de .NET de Service Bus, las propiedades de la aplicación de mensajes se realizan en la colección **Properties** de [BrokeredMessage][]. El código siguiente muestra cómo se leen las propiedades de la aplicación de un mensaje recibido desde un cliente de JMS.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}
```

En la tabla siguiente se muestra cómo se asignan los tipos de propiedad de JMS a los tipos de propiedad de .NET.

| Tipo de propiedad de JMS | Tipo de propiedad de .NET |
|-------------------|--------------------|
| Byte              | sbyte              |
| Entero           | int                |
| Float             | float              |
| Doble            | double             |
| Booleano           | booleano               |
| String            | string             |

El tipo [BrokeredMessage][] admite propiedades de la aplicación de los siguientes tipos: **byte**, **sbyte**, **char**, **short**, **ushort**, **int**, **uint**, **long**, **ulong**, **float**, **double**, **decimal**, **bool**, **Guid**, **string**, **Uri**, **DateTime**, **DateTimeOffset** y **TimeSpan**. El código .NET siguiente muestra cómo establecer las propiedades en un objeto [BrokeredMessage][] con cada uno de estos tipos de propiedades.

```
message.Properties["TestByte"] = (byte)128;
message.Properties["TestSbyte"] = (sbyte)-22;
message.Properties["TestChar"] = (char) 'X';
message.Properties["TestShort"] = (short)-12345;
message.Properties["TestUshort"] = (ushort)12345;
message.Properties["TestInt"] = (int)-100;
message.Properties["TestUint"] = (uint)100;
message.Properties["TestLong"] = (long)-12345;
message.Properties["TestUlong"] = (ulong)12345;
message.Properties["TestFloat"] = (float)3.14159;
message.Properties["TestDouble"] = (double)3.14159;
message.Properties["TestDecimal"] = (decimal)3.14159;
message.Properties["TestBoolean"] = true;
message.Properties["TestGuid"] = Guid.NewGuid();
message.Properties["TestString"] = "Service Bus";
message.Properties["TestUri"] = new Uri("http://www.bing.com");
message.Properties["TestDateTime"] = DateTime.Now;
message.Properties["TestDateTimeOffSet"] = DateTimeOffset.Now;
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

El código Java siguiente muestra cómo se leen las propiedades de la aplicación de un mensaje recibido desde un cliente de .NET del Bus de servicio.

```
Enumeration propertyNames = message.getPropertyNames(); 
while (propertyNames.hasMoreElements()) 
{ 
  String name = (String) propertyNames.nextElement(); 
  Object value = message.getObjectProperty(name); 
  System.out.println(name + ": " + value + " (" + value.getClass() + ")"); 
}
```

En la tabla siguiente se muestra cómo se asignan los tipos de propiedad de .NET a los tipos de propiedad de JMS.

| Tipo de propiedad de .NET | Tipo de propiedad de JMS | Notas                                                                                                                                                               |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte               | UnsignedByte      | -                                                                                                                                                                      |
| sbyte              | Byte              | -                                                                                                                                                                     |
| char               | Character         | -                                                                                                                                                                     |
| short              | Breve             | -                                                                                                                                                                     |
| ushort             | UnsignedShort     | -                                                                                                                                                                     |
| int                | Entero           | -                                                                                                                                                                     |
| uint               | UnsignedInteger   | -                                                                                                                                                                     |
| long               | long              | -                                                                                                                                                                     |
| ulong              | UnsignedLong      | -                                                                                                                                                                     |
| float              | Float             | -                                                                                                                                                                     |
| double             | Double            | -                                                                                                                                                                     |
| Decimal            | BigDecimal        | -                                                                                                                                                                     |
| booleano               | Booleano           | -                                                                                                                                                                     |
| Guid               | UUID              | -                                                                                                                                                                     |
| string             | Cadena            | -                                                                                                                                                                     |
| DateTime           | Date              | -                                                                                                                                                                     |
| Datetimeoffset     | DescribedType     | DateTimeOffset.UtcTicks asignado al tipo AMQP:<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type> |
| TimeSpan           | DescribedType     | Timespan.Ticks asignado al tipo AMQP:<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>                        |
| Identificador URI                | DescribedType     | Uri.AbsoluteUri asignado al tipo AMQP:<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>                               |

### <a name="standard-headers"></a>Encabezados estándar

Las tablas siguientes muestran cómo se asignan los encabezados estándar y las propiedades estándar [BrokeredMessage][] de JMS mediante AMQP 1.0.

#### <a name="jms-to-service-bus-.net-apis"></a>JMS a API de .NET del Bus de servicio

| JMS              | .NET del Bus de servicio               | Notas                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|------------------|--------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| JMSCorrelationID | Message.CorrelationID          | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| JMSDeliveryMode  | No está disponible actualmente        | Service Bus solo admite mensajes duraderos; por ejemplo, DeliveryMode.PERSISTENT, independientemente de lo especificado.                                                                                                                                                                                                                                                                                                                                                         |
| JMSDestination   | Message.To                     | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| JMSExpiration    | Message. TimeToLive            | Conversión                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| JMSMessageID     | Message.MessageID              | De forma predeterminada, JMSMessageID está codificado en formato binario en el mensaje AMQP. Tras recepción de binary-messageid, la biblioteca de clientes de .NET se convierte en una representación de cadena en función de los valores Unicode de los bytes. Para cambiar la biblioteca JMS a fin de que use identificadores de mensaje de cadena, anexe la cadena "binary-messageid = false" a los parámetros de consulta ConnectionURL de JNDI. Por ejemplo: “amqps://[username]:[password]@[namespace].servicebus.windows.net? binary-messageid=false”. |
| JMSPriority      | No está disponible actualmente        | Service Bus no admite la prioridad del mensaje.                                                                                                                                                                                                                                                                                                                                                                                                                             |
| JMSRedelivered   | No está disponible actualmente        | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| JMSReplyTo       | Message. ReplyTo               | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| JMSTimestamp     | Message.EnqueuedTimeUtc        | Conversión                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| JMSType          | Message.Properties[“jms-type”] | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |

#### <a name="service-bus-.net-apis-to-jms"></a>API de .NET del Bus de servicio a JMS

| .NET del Bus de servicio        | JMS              | Notas                   |
|-------------------------|------------------|-------------------------|
| ContentType             | -                  | No está disponible actualmente |
| CorrelationId           | JMSCorrelationID | -                         |
| EnqueuedTimeUtc         | JMSTimestamp     | Conversión              |
| Etiqueta                   | N/D              | No está disponible actualmente |
| MessageId               | JMSMessageID     | -                         |
| ReplyTo                 | JMSReplyTo       | -                         |
| ReplyToSessionId        | N/D              | No está disponible actualmente |
| ScheduledEnqueueTimeUtc | N/D              | No está disponible actualmente |
| SessionId               | N/D              | No está disponible actualmente |
| TimeToLive              | JMSExpiration    | Conversión              |
| Para                      | JMSDestination   | -                         |

## <a name="unsupported-features-and-restrictions"></a>Características no admitidas y restricciones

Existen las restricciones siguientes al usar JMS sobre AMQP 1.0 con el Bus de servicio:

-   Solo se permite un elemento **MessageProducer** o **MessageConsumer** por sesión. Si desea crear varios objetos **MessageProducer** o **MessageConsumer** en una aplicación, cree sesiones dedicadas para cada uno de ellos.

-   Actualmente no se admiten las suscripciones a tema volátiles.

-   No se admiten objetos **MessageSelector**.

-   No se admiten destinos temporales, por ejemplo, **TemporaryQueue** o **TemporaryTopic**, junto con las API **QueueRequestor** y **TopicRequestor** que los usan.

-   No se admiten las sesiones de transacción.

-   No se admiten las transacciones distribuidas.

## <a name="next-steps"></a>Pasos siguientes

¿Listo para obtener más información? Consulte los siguientes vínculos:

- [Información general sobre AMQP para Service Bus]
- [AMQP de Service Bus para Windows Server]

[AMQP de Service Bus para Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

[Información general sobre AMQP para Service Bus]: service-bus-amqp-overview.md
[Portal de Azure]: https://portal.azure.com



<!--HONumber=Oct16_HO2-->


