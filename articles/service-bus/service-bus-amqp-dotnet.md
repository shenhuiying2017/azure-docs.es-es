<properties 
    pageTitle="Bus de servicio con .NET y AMQP 1.0 | Microsoft Azure"
    description="Uso del Bus de servicio desde .NET con AMQP"
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
    ms.date="07/05/2016"
    ms.author="sethm" />

# Uso del Bus de servicio desde .NET con AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

## Descargar el SDK del bus de servicio

La compatibilidad con AMQP 1.0 está disponible en el SDK del Bus de servicio versión 2.1 o posterior. Asegúrese de tener la versión más reciente descargando los bits del Bus de servicio de [NuGet][].

## Configuración de aplicaciones .NET para usar AMQP 1.0

De manera predeterminada, la biblioteca de clientes .NET del bus de servicio se comunica con el servicio del bus de servicio utilizando un protocolo dedicado basado en SOAP. Para usar AMQP 1.0 en lugar del protocolo predeterminado, es necesario configurar de manera explícita la cadena de conexión del Bus de servicio tal y como se describe en la sección siguiente. Aparte de este cambio, el código de la aplicación permanece invariable al utilizar AMQP 1.0.

La versión actual incluye unas cuantas funciones de la API que no son compatibles con el uso de AMQP. Estas funciones incompatibles se enumeran más adelante en la sección [Características no admitidas, restricciones y diferencias de comportamiento](#unsupported-features-restrictions-and-behavioral-differences). Algunos de los parámetros de configuración avanzados también adquieren un significado diferente cuando se usa AMQP.

### Configuración mediante App.config

Es recomendable que las aplicaciones utilicen el archivo de configuración App.config para almacenar la configuración. En el caso de las aplicaciones del Bus de servicio, puede usar App.config para almacenar la configuración del valor **ConnectionString** del Bus de servicio. A continuación se muestra un archivo App.config de ejemplo:

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <appSettings>
	        <add key="Microsoft.ServiceBus.ConnectionString"
	             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
	    </appSettings>
	</configuration>

El valor del parámetro `Microsoft.ServiceBus.ConnectionString` es la cadena de conexión del Bus de servicio que se usa para configurar la conexión al Bus de servicio. El formato es como sigue:

	Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

Donde `[namespace]` y `SharedAccessKey` se obtienen en el [Portal de Azure][]. Para obtener más información, consulte [Utilización de las colas del Bus de servicio][].

Al usar AMQP, anexe la cadena de conexión a `;TransportType=Amqp`. Esta notación informa a la biblioteca de cliente que realice la conexión con el Bus de servicio mediante AMQP 1.0.

## Serialización de mensajes

Cuando se usa el protocolo predeterminado, el comportamiento de serialización predeterminado de la biblioteca de cliente de .NET consiste en usar el tipo [DataContractSerializer][] para serializar una instancia de [BrokeredMessage][] para el transporte entre la biblioteca de cliente y el servicio del Bus de servicio. Cuando se usa el modo de transporte de AMQP, la biblioteca de cliente emplea el sistema de tipos de AMQP para la serialización del [mensaje asincrónico][BrokeredMessage] en un mensaje de AMQP. Esta serialización permite que el mensaje se reciba e interprete por una aplicación receptora que se ejecuta potencialmente en una plataforma diferente, por ejemplo, una aplicación Java que usa la API de JMS para tener acceso al Bus de servicio.

Cuando se construye una instancia [BrokeredMessage][], puede proporcionar un objeto .NET como un parámetro al constructor para que actúe como el cuerpo del mensaje. Para los objetos que se pueden asignar a tipos primitivos de AMQP, el cuerpo se serializa en tipos de datos de AMQP. Si el objeto no se puede asignar directamente a un tipo primitivo de AMQP, es decir, un tipo personalizado definido por la aplicación, el objeto se serializa con [DataContractSerializer][] y los bytes serializados se envían en un mensaje de datos de AMQP.

Para facilitar la interoperabilidad con clientes que no sean de .NET, use solo los tipos de .NET que se puedan serializar directamente en tipos AMQP para el cuerpo del mensaje. En la siguiente tabla se detallan estos tipos y la asignación correspondiente al sistema de tipos de AMQP.

| Tipo de objeto de cuerpo de .NET | Tipo de AMQP asignado | Tipo de sección de cuerpo de AMQP |
|--------------------------------|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| booleano | boolean | Valor de AMQP |
| byte | ubyte | Valor de AMQP |
| ushort | ushort | Valor de AMQP |
| uint | uint | Valor de AMQP |
| ulong | ulong | Valor de AMQP |
| sbyte | byte | Valor de AMQP |
| short | short | Valor de AMQP |
| int | int | Valor de AMQP |
| long | long | Valor de AMQP |
| float | float | Valor de AMQP |
| double | double | Valor de AMQP |
| decimal | decimal128 | Valor de AMQP |
| char | char | Valor de AMQP |
| DateTime | timestamp | Valor de AMQP |
| Guid | uuid | Valor de AMQP |
| byte | binary | Valor de AMQP |
| string | string | Valor de AMQP |
| System.Collections.IList | list | Valor de AMQP: los elementos contenidos en la colección solo pueden ser los definidos en esta tabla. |
| System.Array | array | Valor de AMQP: los elementos contenidos en la colección solo pueden ser los definidos en esta tabla. |
| System.Collections.IDictionary | map | Valor de AMQP: los elementos contenidos en la colección solo pueden ser los definidos en esta tabla. Nota: solo se admiten claves de cadena. |
| Identificador URI | Cadena descrita (consulte la tabla siguiente) | Valor de AMQP |
| DateTimeOffset | Longitud descrita (consulte la tabla siguiente) | Valor de AMQP |
| TimeSpan | Longitud descrita (consulte a continuación) | Valor de AMQP |
| Stream | binary | Datos de AMQP (pueden ser varios) Las secciones de datos contienen los bytes sin formato que se leen desde el objeto de secuencia. |
| Otro objeto | binary | Datos de AMQP (pueden ser varios) Contiene el binario serializado del objeto que usa DataContractSerializer o un serializador proporcionado por la aplicación. |

| Tipo .NET | Mapped AMQP Described Type | Notas |
|----------------|-------------------------------------------------------------------------------------------------------------------------|-------------------------|
| Identificador URI | `<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` | Uri.AbsoluteUri |
| DateTimeOffset | `<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` | DateTimeOffset.UtcTicks |
| TimeSpan | `<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> ` | TimeSpan.Ticks |

## Características no admitidas, restricciones y diferencias de comportamiento

Actualmente no se admiten las siguientes características de la API de .NET de Bus de servicio al usar AMQP:

-   Transacciones

-   Envío a través de un destino de transferencia

También hay algunas pequeñas diferencias en el comportamiento de la API de .NET del Bus de servicio al usar AMQP, en comparación con el protocolo predeterminado:

-   La propiedad [OperationTimeout][] se omite.

-   `MessageReceiver.Receive(TimeSpan.Zero)` se implementa como `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.

## Control de la configuración del protocolo AMQP

Las API de .NET exponen varias opciones para controlar el comportamiento del protocolo AMQP:

-   **MessageReceiver.PrefetchCount**: controla el crédito inicial que se aplica a un vínculo. El valor predeterminado es 0.

-   **MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize**: controla el tamaño máximo del marco de AMQP ofrecido durante la negociación en el momento de apertura de la conexión. The default is 65.536 bytes.

-   **MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval**: si las transferencias se pueden definir por lotes, este valor determina el retraso máximo de las disposiciones de envío. Heredado por remitentes/receptores de forma predeterminada. El remitente/receptor individual puede invalidar el valor predeterminado, que es de 20 milisegundos.

-   **MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity**: controla si las conexiones de AMQP se establecen a través de una conexión SSL. El valor predeterminado es **true**.

## Pasos siguientes

¿Listo para obtener más información? Consulte los siguientes vínculos:

- [Información general sobre AMQP para el Bus de servicio]
- [Compatibilidad de AMQP 1.0 con los temas y las colas con particiones del Bus de servicio]
- [AMQP de Bus de servicio para Windows Server]

  [Utilización de las colas del Bus de servicio]: service-bus-dotnet-get-started-with-queues.md
  [DataContractSerializer]: https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: https://msdn.microsoft.com/library/azure/jj657638.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)]: https://msdn.microsoft.com/library/azure/jj657703.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[Portal de Azure]: https://portal.azure.com
[Información general sobre AMQP para el Bus de servicio]: service-bus-amqp-overview.md
[Compatibilidad de AMQP 1.0 con los temas y las colas con particiones del Bus de servicio]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP de Bus de servicio para Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

<!---HONumber=AcomDC_0831_2016-->