---
title: Bus de servicio y PHP con AMQP 1.0 | Microsoft Docs
description: Uso del Bus de servicio desde PHP con AMQP
services: service-bus
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2016
ms.author: sethm

---
# <a name="using-service-bus-from-php-with-amqp-1.0"></a>Uso del Bus de servicio desde PHP con AMQP 1.0
[!INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton-PHP es un lenguaje PHP que se enlaza con Proton-C; es decir, Proton-PHP se implementa como un contenedor en torno a un motor implementado en C.

## <a name="downloading-the-proton-client-library"></a>Descarga de la biblioteca de cliente de Proton
Puede descargar Proton-C y los enlaces asociados (incluido PHP) desde [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). La descarga está en formato de código fuente. Para compilar el código, siga las instrucciones incluidas en el paquete descargado.

> [!IMPORTANT]
> En el momento de redactar este artículo, la compatibilidad con SSL en Proton-C solo está disponible para los sistemas operativos Linux. Dado que Bus de servicio de Azure requiere el uso de SSL, en este momento solo puede usarse Proton-C (y los enlaces de lenguaje) para acceder al Bus de servicio desde Linux. Se está trabajando para habilitar Proton-C con SSL en Windows, así que consulte periódicamente si existen actualizaciones.
> 
> 

## <a name="working-with-service-bus-queues,-topics,-and-subscriptions-from-php"></a>Uso de colas, temas y suscripciones de Service Bus desde PHP
En el código siguiente se muestra cómo enviar y recibir mensajes de una entidad de mensajería de Bus de servicio.

### <a name="sending-messages-using-proton-php"></a>Envío de mensajes con Proton-PHP
En el código siguiente se muestra cómo enviar un mensaje a una entidad de mensajería de Bus de servicio.

```
$messenger = new Messenger();
$message = new Message();
$message->address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";

$message->body = "This is a text string";
$messenger->put($message);
$messenger->send();
```

### <a name="receiving-messages-using-proton-php"></a>Recepción de mensajes con Proton-PHP
En el código siguiente se muestra cómo recibir un mensaje desde una entidad de mensajería de Bus de servicio.

```
$messenger = new Messenger();
$address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";
$messenger->subscribe($address);

$messenger->start();
$messenger->recv(1);

if($messenger->incoming())
{
   $message = new Message();
   $messenger->get($message);      
}

$messenger->stop();
```

## <a name="messaging-between-.net-and-proton-php"></a>Mensajería entre .NET y Proton-PHP
### <a name="application-properties"></a>Propiedades de la aplicación
#### <a name="protonphp-to-service-bus-.net-apis"></a>ProtonPHP a las API de .NET de Bus de servicio
Los mensajes de Proton-PHP admiten propiedades de aplicación de los tipos siguientes: **integer**, **double**, **Boolean**, **string** y **object**. En el siguiente código PHP se muestra cómo establecer propiedades en un mensaje con cada uno de estos tipos de propiedades.

```
$message->properties["TestInt"] = 1;    
$message->properties["TestDouble"] = 1.5;      
$message->properties["TestBoolean"] = False;
$message->properties["TestString"] = "Service Bus";    
$message->properties["TestObject"] = new UUID("1234123412341234");   
```

En las API de .NET de Service Bus, las propiedades de la aplicación de mensajes se realizan en la colección **Properties** de [BrokeredMessage][BrokeredMessage]. En el código siguiente se muestra cómo se leen las propiedades de aplicación de un mensaje recibido de un cliente PHP.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}if (message.Properties.Keys.Count > 0)
{
foreach (string name in message.Properties.Keys)
{
  Object value = message.Properties[name];
  Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
}
Console.WriteLine();
}
```

En la tabla siguiente se muestra cómo se asignan los tipos de propiedad de PHP a los tipos de propiedad de .NET.

| Tipo de propiedad de PHP | Tipo de propiedad de .NET |
| --- | --- |
| integer |int |
| double |double |
| boolean |booleano |
| string |string |
| objeto |Objeto |

#### <a name="service-bus-.net-apis-to-php"></a>API de .NET de Bus de servicio a PHP
El tipo [BrokeredMessage][BrokeredMessage] admite propiedades de la aplicación de los siguientes tipos: **byte**, **sbyte**, **char**, **short**, **ushort**, **int**, **uint**, **long**, **ulong**, **float**, **double**, **decimal**, **bool**, **Guid**, **string**, **Uri**, **DateTime**, **DateTimeOffset** y **TimeSpan**. El código .NET siguiente muestra cómo establecer las propiedades en un objeto [BrokeredMessage][BrokeredMessage] con cada uno de estos tipos de propiedades.

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

El código PHP siguiente muestra cómo se leen las propiedades de la aplicación de un mensaje recibido desde un cliente de .NET del Bus de servicio.

```
if ($message->properties != null)
{
  foreach($message->properties as $key => $value)
  {
    printf("-- %s : %s (%s) \n", $key, $value, gettype($value));                       
  }         
}
```

En la tabla siguiente se muestra cómo se asignan los tipos de propiedad de JHP a los tipos de propiedad de .NET.

| Tipo de propiedad de .NET | Tipo de propiedad de PHP | Notas |
| --- | --- | --- |
| byte |integer |- |
| sbyte |integer |- |
| char |Char |Clase Proton-PHP |
| short |integer |- |
| ushort |integer |- |
| int |integer |- |
| uint |Entero |- |
| long |integer |- |
| ulong |integer |- |
| float |double |- |
| double |double |- |
| Decimal |string |El Decimal no es compatible actualmente con Proton. |
| booleano |boolean |- |
| Guid |UUID |Clase Proton-PHP |
| string |string |- |
| DateTime |integer |- |
| Datetimeoffset |DescribedType |DateTimeOffset.UtcTicks asignado al tipo AMQP:<type name="datetime-offset" class=restricted source="long"> <descriptor name="com.microsoft:datetime-offset" /></type> |
| TimeSpan |DescribedType |Timespan.Ticks asignado al tipo AMQP:<type name="timespan" class=restricted source="long"> <descriptor name="com.microsoft:timespan" /></type> |
| Identificador URI |DescribedType |Uri.AbsoluteUri asignado al tipo AMQP:<type name="uri" class=restricted source="string"> <descriptor name="com.microsoft:uri" /></type> |

### <a name="standard-properties"></a>Propiedades estándar
En las tablas siguientes se muestra la asignación entre las propiedades de mensajes estándares de Proton-PHP y las propiedades de mensajes estándar de [BrokeredMessage][BrokeredMessage].

| Proton-PHP | .NET del Bus de servicio | Notas |
| --- | --- | --- |
| Duradero |N/D |El Bus de servicio solo admite mensajes duraderos. |
| Prioridad |N/D |El Bus de servicio solo admite una única prioridad de mensaje. |
| Ttl |Message.TimeToLive |Conversión, el TTL de Proton-PHP se define en milisegundos. |
| first\_acquirer |- |- |
| delivery\_count |- |- |
| Id |Message.Id |- |
| user\_id |- |- |
| Dirección |Message.To |- |
| Asunto |Message.Label |- |
| reply\_to |Message.ReplyTo |- |
| correlation\_id |Message.CorrelationId |- |
| content\_type |Message.ContentType |- |
| content\_encoding |N/D |- |
| expiry\_time |Message.ExpiresAtUTC |- |
| creation\_time |N/D |- |
| group\_id |Message.SessionId |- |
| group\_sequence |- |- |
| reply\_to\_group\_id |Message.ReplyToSessionId |- |
| Formato |N/D |- |

#### <a name="service-bus-.net-apis-to-proton-php"></a>API de .NET de Bus de servicio a Proton-PHP
| .NET del Bus de servicio | Proton-PHP | Notas |
| --- | --- | --- |
| ContentType |Message-\>content\_type |- |
| CorrelationId |Message-\>correlation\_id |- |
| EnqueuedTimeUtc |Message-\>annotations[x-opt-enqueued-time] |- |
| Etiqueta |Message-\>subject |- |
| MessageId |Message-\>id |- |
| ReplyTo |Message-\>reply\_to |- |
| ReplyToSessionId |Message-\>reply\_to\_group\_id |- |
| ScheduledEnqueueTimeUtc |Message-\>annotations ["x-opt-scheduled-enqueue-time"] |- |
| SessionId |Message-\>group\_id |- |
| TimeToLive |Message-\>ttl |Conversión, el TTL de Proton-PHP se define en milisegundos. |
| Para |Message-\>address |- |

## <a name="next-steps"></a>Pasos siguientes
¿Listo para obtener más información? Consulte los siguientes vínculos:

* [Información general sobre AMQP para Service Bus]
* [AMQP de Service Bus para Windows Server]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP de Service Bus para Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[Información general sobre AMQP para Service Bus]: service-bus-amqp-overview.md



<!--HONumber=Oct16_HO2-->


