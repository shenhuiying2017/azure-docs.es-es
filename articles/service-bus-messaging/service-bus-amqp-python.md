---
title: Uso de Service Bus desde Python con AMQP 1.0 | Microsoft Docs
description: Uso del Bus de servicio desde Python con AMQP
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 375396e7-cbec-4d25-9b98-63ef8de75fef
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 97e90f5429fe4f2535a246db8dfbe81c772b3c88


---
# <a name="using-service-bus-from-python-with-amqp-10"></a>Uso del Bus de servicio desde Python con AMQP 1.0
[!INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton-Python es un lenguaje Python que se enlaza con Proton-C; es decir, Proton-Python se implementa como un contenedor en torno a un motor implementado en C.

## <a name="download-the-proton-client-library"></a>Descarga de la biblioteca de cliente de Proton
Puede descargar Proton-C y los enlaces asociados (incluido Python) desde [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). La descarga está en formato de código fuente. Para compilar el código, siga las instrucciones incluidas en el paquete descargado.

Tenga en cuenta que en el momento de redactar este artículo, la compatibilidad con SSL en Proton-C solo está disponible para los sistemas operativos Linux. Dado que Bus de servicio de Azure requiere el uso de SSL, en este momento solo puede usarse Proton-C (y los enlaces de lenguaje) para acceder al Bus de servicio desde Linux. Se está trabajando para habilitar Proton-C con SSL en Windows, así que consulte periódicamente si existen actualizaciones.

## <a name="working-with-service-bus-queues-topics-and-subscriptions-from-python"></a>Uso de colas, temas y suscripciones de Service Bus desde Python
En el código siguiente se muestra cómo enviar y recibir mensajes de una entidad de mensajería de Bus de servicio.

### <a name="send-messages-using-proton-python"></a>Envío de mensajes con Proton-Python
En el código siguiente se muestra cómo enviar un mensaje a una entidad de mensajería de Bus de servicio.

```
messenger = Messenger()
message = Message()
message.address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"

message.body = u"This is a text string"
messenger.put(message)
messenger.send()
```

### <a name="receive-messages-using-proton-python"></a>Recepción de mensajes con Proton-Python
En el código siguiente se muestra cómo recibir un mensaje desde una entidad de mensajería de Bus de servicio.

```
messenger = Messenger()
address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"
messenger.subscribe(address)

messenger.start()
messenger.recv(1)
message = Message()
if messenger.incoming:
      messenger.get(message)
messenger.stop()
```

## <a name="messaging-between-net-and-proton-python"></a>Mensajería entre .NET y Proton-Python
### <a name="application-properties"></a>Propiedades de la aplicación
#### <a name="proton-python-to-service-bus-net-apis"></a>Proton-Python a las API .de NET del Bus de servicio
Los mensajes de Proton-Python admiten propiedades de la aplicación de los siguientes tipos: **int**, **long**, **float**, **uuid**, **bool**, **string**. En el siguiente código de Python se muestra cómo establecer propiedades en un mensaje con cada uno de estos tipos de propiedades.

```
message.properties[u"TestString"] = u"This is a string"    
message.properties[u"TestInt"] = 1
message.properties[u"TestLong"] = 1000L
message.properties[u"TestFloat"] = 1.5    
message.properties[u"TestGuid"] = uuid.uuid1()    
```

En la API de .NET de Service Bus, las propiedades de la aplicación de mensajes se transmiten en la colección **Properties** de [BrokeredMessage][BrokeredMessage]. En el código siguiente se muestra cómo se leen las propiedades de aplicación de un mensaje recibido de un cliente Python.

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

En la tabla siguiente se muestra cómo se asignan los tipos de propiedad de Python a los tipos de propiedad de .NET.

| Tipo de propiedad de Python | Tipo de propiedad de .NET |
| --- | --- |
| int |int |
| float |double |
| long |int64 |
| uuid |guid |
| booleano |booleano |
| string |string |

#### <a name="service-bus-net-apis-to-proton-python"></a>API de .NET del Bus de servicio a Proton-Python
El tipo [BrokeredMessage][BrokeredMessage] admite propiedades de aplicación de los siguientes tipos: **byte**, **sbyte**, **char**, **short**, **ushort**, **int**, **uint**, **long**, **ulong**, **float**, **double**, **decimal**, **bool**, **Guid**, **string**, **Uri**, **DateTime**, **DateTimeOffset** y **TimeSpan**. El código .NET siguiente muestra cómo establecer las propiedades en un objeto [BrokeredMessage][BrokeredMessage] con cada uno de estos tipos de propiedades.

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
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

El código Python siguiente muestra cómo se leen las propiedades de la aplicación de un mensaje recibido desde un cliente de .NET del Bus de servicio.

```
if message.properties != None:
   for k,v in message.properties.items():         
         print "--   %s : %s (%s)" % (k, str(v), type(v))         
```

En la tabla siguiente se muestra cómo se asignan los tipos de propiedad de .NET a los tipos de propiedad de Python.

| Tipo de propiedad de .NET | Tipo de propiedad de Python | Notas |
| --- | --- | --- |
| byte |int |- |
| sbyte |int |- |
| char |char |Clase Proton-Python |
| short |int |- |
| ushort |int |- |
| int |int |- |
| uint |int |- |
| long |int |- |
| ulong |long |Clase Proton-Python |
| float |float |- |
| double |float |- |
| Decimal |String |El Decimal no es compatible actualmente con Proton. |
| booleano |booleano |- |
| Guid |uuid |Clase Proton-Python |
| string |string |- |
| DateTime |timestamp |Clase Proton-Python |
| Datetimeoffset |DescribedType |DateTimeOffset.UtcTicks asignado al tipo AMQP:<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type> |
| TimeSpan |DescribedType |Timespan.Ticks asignado al tipo AMQP:<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> |
| Identificador URI |DescribedType |Uri.AbsoluteUri asignado al tipo AMQP:<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type> |

### <a name="standard-properties"></a>Propiedades estándar
En las tablas siguientes se muestra la asignación entre las propiedades de mensajes estándar Proton-PHP y las propiedades de mensajes estándar [BrokeredMessage][BrokeredMessage].

#### <a name="proton-python-to-service-bus-net-apis"></a>Proton-Python a las API .de NET del Bus de servicio
| Proton-Python | .NET del Bus de servicio | Notas |
| --- | --- | --- |
| duradero |N/D |El Bus de servicio solo admite mensajes duraderos. |
| prioridad |N/D |El Bus de servicio solo admite una única prioridad de mensaje. |
| Ttl |Message.TimeToLive |Conversión, el TTL de Proton-Python se define en milisegundos. |
| first\_acquirer |N/D |- |
| delivery\_count |N/D |- |
| Id |Message.MessageID |- |
| user\_id |N/D |- |
| address |Message.To |- |
| subject |Message.Label |- |
| reply\_to |Message.ReplyTo |- |
| correlation\_id |Message.CorrelationID |- |
| content\_type |Message.ContentType |- |
| content\_encoding |N/D |- |
| expiry\_time |N/D |- |
| creation\_time |N/D |- |
| group\_id |Message.SessionId |- |
| group\_sequence |N/D |- |
| reply\_to\_group\_id |Message.ReplyToSessionId |- |
| formato |N/D |- |

| .NET del Bus de servicio | Proton | Notas |
| --- | --- | --- |
| ContentType |Message.content\_type |- |
| CorrelationId |Message.correlation\_id |- |
| EnqueuedTimeUtc |N/D |- |
| Etiqueta |Message.subject |- |
| MessageId |Message.id |- |
| ReplyTo |Message.reply\_to |- |
| ReplyToSessionId |Message.reply\_to\_group\_id |- |
| ScheduledEnqueueTimeUtc |N/D |- |
| SessionId |Message.group\_id |- |
| TimeToLive |Message.ttl |Conversión, el TTL de Proton-Python se define en milisegundos. |
| Para |Message.address |- |

## <a name="next-steps"></a>Pasos siguientes
¿Listo para obtener más información? Consulte los siguientes vínculos:

* [Información general sobre AMQP para Service Bus]
* [AMQP de Service Bus para Windows Server]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP de Service Bus para Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

[Información general sobre AMQP para Service Bus]: service-bus-amqp-overview.md



<!--HONumber=Nov16_HO3-->


