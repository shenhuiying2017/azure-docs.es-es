---
title: Uso de Service Bus desde .NET con AMQP 1.0 | Microsoft Docs
description: Uso de Azure Service Bus desde .NET con AMQP
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 332bcb13-e287-4715-99ee-3d7d97396487
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: 0eb68c97ca26a862a79de9ffb83b1fc630ba2af4
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>Uso de Service Bus desde .NET con AMQP 1.0

La compatibilidad con AMQP 1.0 está disponible en el paquete de Service Bus versión 2.1 o posterior. Asegúrese de tener la versión más reciente descargando los bits de Service Bus desde [NuGet][NuGet].

## <a name="configure-net-applications-to-use-amqp-10"></a>Configuración de aplicaciones .NET para usar AMQP 1.0

De manera predeterminada, la biblioteca de clientes .NET de Service Bus se comunica con el servicio de Service Bus utilizando un protocolo dedicado basado en SOAP. Para usar AMQP 1.0 en lugar del protocolo predeterminado, es necesario configurar de manera explícita la cadena de conexión de Service Bus tal y como se describe en la sección siguiente. Aparte de este cambio, el código de la aplicación permanece invariable al utilizar AMQP 1.0.

La versión actual incluye unas cuantas funciones de la API que no son compatibles con el uso de AMQP. Estas funciones incompatibles se enumeran más adelante en la sección [Funciones no admitidas, restricciones y diferencias de comportamiento](#unsupported-features-restrictions-and-behavioral-differences). Algunos de los parámetros de configuración avanzados también adquieren un significado diferente cuando se usa AMQP.

### <a name="configuration-using-appconfig"></a>Configuración mediante App.config

Es recomendable que las aplicaciones utilicen el archivo de configuración App.config para guardar las opciones de configuración. En el caso de las aplicaciones de Service Bus, puede usar App.config para almacenar la cadena de conexión de Service Bus. A continuación se muestra un archivo App.config de ejemplo:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

El valor del parámetro `Microsoft.ServiceBus.ConnectionString` es la cadena de conexión de Service Bus que se usa para configurar la conexión a Service Bus. El formato es como sigue:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Donde `namespace` y `SAS key` se obtienen de [Azure Portal][Azure portal] al crear un espacio de nombres de Service Bus. Para más información, vea [Creación de un espacio de nombres de Service Bus mediante Azure Portal][Create a Service Bus namespace using the Azure portal].

Al usar AMQP, anexe la cadena de conexión a `;TransportType=Amqp`. Esta notación informa a la biblioteca de cliente que realice la conexión con Service Bus mediante AMQP 1.0.

## <a name="message-serialization"></a>Serialización de mensajes

Cuando se usa el protocolo predeterminado, el comportamiento de serialización predeterminado de la biblioteca de cliente de .NET consiste en usar el tipo [DataContractSerializer][DataContractSerializer] para serializar una instancia de [BrokeredMessage][BrokeredMessage] para el transporte entre la biblioteca de cliente y el servicio de Service Bus. Cuando se usa el modo de transporte de AMQP, la biblioteca de cliente emplea el sistema de tipos de AMQP para la serialización del [mensaje asincrónico][BrokeredMessage] en un mensaje de AMQP. Esta serialización permite que el mensaje se reciba e interprete por una aplicación receptora que se ejecuta potencialmente en una plataforma diferente, por ejemplo, una aplicación Java que usa la API de JMS para tener acceso a Service Bus.

Cuando se construye una instancia [BrokeredMessage][BrokeredMessage], puede proporcionar un objeto .NET como un parámetro al constructor para que actúe como el cuerpo del mensaje. Para los objetos que se pueden asignar a tipos primitivos de AMQP, el cuerpo se serializa en tipos de datos de AMQP. Si el objeto no se puede asignar directamente a un tipo primitivo de AMQP, es decir, un tipo personalizado definido por la aplicación, el objeto se serializa con [DataContractSerializer][DataContractSerializer] y los bytes serializados se envían en un mensaje de datos de AMQP.

Para facilitar la interoperabilidad con clientes que no sean de .NET, use solo los tipos de .NET que se puedan serializar directamente en tipos AMQP para el cuerpo del mensaje. En la siguiente tabla se detallan estos tipos y la asignación correspondiente al sistema de tipos de AMQP.

| Tipo de objeto de cuerpo de .NET | Tipo de AMQP asignado | Tipo de sección de cuerpo de AMQP |
| --- | --- | --- |
| booleano |boolean |Valor de AMQP |
| byte |ubyte |Valor de AMQP |
| ushort |ushort |Valor de AMQP |
| uint |uint |Valor de AMQP |
| ulong |ulong |Valor de AMQP |
| sbyte |byte |Valor de AMQP |
| short |short |Valor de AMQP |
| int |int |Valor de AMQP |
| long |long |Valor de AMQP |
| float |float |Valor de AMQP |
| double |double |Valor de AMQP |
| decimal |decimal128 |Valor de AMQP |
| char |char |Valor de AMQP |
| Datetime |timestamp |Valor de AMQP |
| Guid |uuid |Valor de AMQP |
| byte[] |binary |Valor de AMQP |
| cadena |cadena |Valor de AMQP |
| System.Collections.IList |list |Valor de AMQP: los elementos contenidos en la colección solo pueden ser los definidos en esta tabla. |
| System.Array |array |Valor de AMQP: los elementos contenidos en la colección solo pueden ser los definidos en esta tabla. |
| System.Collections.IDictionary |map |Valor de AMQP: los elementos contenidos en la colección solo pueden ser los definidos en esta tabla. Nota: solo se admiten claves de cadena. |
| Identificador URI |Cadena descrita (consulte la tabla siguiente) |Valor de AMQP |
| DateTimeOffset |Longitud descrita (consulte la tabla siguiente) |Valor de AMQP |
| TimeSpan |Longitud descrita (consulte a continuación) |Valor de AMQP |
| Stream |binary |Datos de AMQP (pueden ser varios) Las secciones de datos contienen los bytes sin formato que se leen desde el objeto de secuencia. |
| Otro objeto |binary |Datos de AMQP (pueden ser varios) Contiene el binario serializado del objeto que usa DataContractSerializer o un serializador proporcionado por la aplicación. |

| Tipo .NET | Mapped AMQP Described Type | Notas |
| --- | --- | --- |
| Identificador URI |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| timespan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> ` |TimeSpan.Ticks |

## <a name="unsupported-features-restrictions-and-behavioral-differences"></a>Características no admitidas, restricciones y diferencias de comportamiento

Actualmente no se admiten las siguientes características de la API de .NET de Service Bus al usar AMQP:

* Transacciones
* Envío a través de un destino de transferencia

También hay algunas pequeñas diferencias en el comportamiento de la API de .NET de Service Bus al usar AMQP, en comparación con el protocolo predeterminado:

* La propiedad [OperationTimeout][OperationTimeout] se omite.
* `MessageReceiver.Receive(TimeSpan.Zero)` se implementa como `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.
* La finalización de mensajes mediante tokens de bloqueo solo puede realizarse por los receptores del mensaje que reciben inicialmente los mensajes.

## <a name="control-amqp-protocol-settings"></a>Control de la configuración del protocolo AMQP

Las [API de .NET](/dotnet/api/) exponen varias opciones para controlar el comportamiento del protocolo AMQP:

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: controla el crédito inicial que se aplica a un vínculo. El valor predeterminado es 0.
* **[MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: controla el tamaño máximo del marco de AMQP ofrecido durante la negociación en el momento de apertura de la conexión. The default is 65.536 bytes.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: si las transferencias se pueden definir por lotes, este valor determina el retraso máximo de las disposiciones de envío. Heredado por remitentes/receptores de forma predeterminada. El remitente/receptor individual puede invalidar el valor predeterminado, que es de 20 milisegundos.
* **[MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: controla si las conexiones de AMQP se establecen a través de una conexión SSL. El valor predeterminado es **true**.

## <a name="next-steps"></a>pasos siguientes

¿Listo para obtener más información? Consulte los siguientes vínculos:

* [Información general sobre AMQP para Service Bus]
* [Guía del protocolo AMQP 1.0]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azureservicebus-4.0.0
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Información general sobre AMQP para Service Bus]: service-bus-amqp-overview.md
[Guía del protocolo AMQP 1.0]: service-bus-amqp-protocol-guide.md

