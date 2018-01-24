---
title: "Excepciones de mensajería de Azure Event Hubs | Microsoft Docs"
description: "Lista de excepciones de mensajería y acciones sugeridas de Azure Event Hubs."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 2c6273de-0106-47e5-b45d-59040e51f2c5
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 964475ba8b42ac41707fa78468bfe551677c595f
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="event-hubs-messaging-exceptions"></a>Excepciones de mensajería de Event Hubs

En este artículo se enumeran algunas excepciones generadas por la biblioteca de API de mensajería de Azure Service Bus, que incluye las API de Event Hubs. Esta referencia está sujeta a cambios, de modo que compruebe las actualizaciones.

## <a name="exception-categories"></a>Categorías de excepciones

Las API de Event Hubs generan excepciones que pueden dividirse en las siguientes categorías, junto con la acción asociada que puede realizar para intentar corregirlas.

1. Error de codificación de usuario: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Acción general: intente corregir el código antes de continuar.
2. Error de instalación/configuración: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Acción general: revise la configuración y cámbiela si es necesario.
3. Excepciones transitorias: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Acción general: intente realizar de nuevo la operación o informe a los usuarios.
4. Otras excepciones: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Acción general: específica del tipo de excepción; consulte la tabla de la siguiente sección. 

## <a name="exception-types"></a>Tipos de excepciones
En la tabla siguiente se describen los tipos de excepción de mensajería, sus causas y las acciones sugeridas que puede realizar.

| **Tipo de excepción** | **Descripción/causa/ejemplos** | **Acción sugerida** | **Nota sobre el reintento automático o inmediato** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |El servidor no respondió a la operación solicitada en el tiempo especificado que está controlado por [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). Puede que el servidor haya completado la operación solicitada. Esto se puede producir debido a un retraso de red o a otros retrasos de infraestructura. |Compruebe la coherencia del estado del sistema y vuelva a intentarlo si es necesario.<br /> Consulte [TimeoutException](#timeoutexception). |El reintento podría resultar útil en algunos casos; agregue lógica de reintento al código. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |La operación del usuario solicitada no está permitida en el servidor o servicio. Consulte el mensaje de excepción para obtener detalles. Por ejemplo, [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) genera esta excepción si el mensaje se recibió en el modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) . |Compruebe el código y la documentación. Asegúrese de que la operación solicitada sea válida. |El reintento no le será de ayuda. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Se realiza un intento para invocar una operación en un objeto que ya se ha cerrado, anulado o eliminado. En raras ocasiones, la transacción de ambiente ya se ha eliminado. |Compruebe el código y asegúrese de que no invoca operaciones de un objeto desechado. |El reintento no le será de ayuda. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |El objeto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) no pudo adquirir un token, el token no es válido o no contiene las notificaciones necesarias para realizar la operación. |Asegúrese de que el proveedor de tokens se cree con los valores correctos. Compruebe la configuración de Access Control Service. |El reintento podría resultar útil en algunos casos; agregue lógica de reintento al código. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Uno o varios de los argumentos proporcionados para el método no son válidos. El URI proporcionado a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) o [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) contiene segmentos de ruta de acceso. El esquema de URI proporcionado a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) o [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) no es válido. El valor de la propiedad es mayor de 32 KB. |Compruebe el código de llamada y asegúrese de que los argumentos sean correctos. |El reintento no le será de ayuda. |
| [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /> [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) |La entidad asociada a la operación no existe o se eliminó. |Asegúrese de que la entidad exista. |El reintento no le será de ayuda. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |El cliente no puede establecer una conexión al Centro de eventos. |Asegúrese de que el nombre de host proporcionado sea correcto y que este sea accesible. |El reintento podría resultar útil si hay problemas de conectividad intermitente. |
| [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) |El servicio no puede procesar la solicitud en este momento. |El cliente puede esperar durante un período de tiempo y volver a intentar realizar la operación. <br /> Consulte [ServerBusyException](#serverbusyexception). |El cliente puede volver a intentarlo tras un determinado intervalo de tiempo. Si el reintento genera otra excepción, compruebe el comportamiento de reintento de esa excepción. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Excepción de mensajería genérica que puede producirse en los siguientes casos: intento de crear [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) con un nombre o una ruta de acceso que pertenece a un tipo de entidad diferente (por ejemplo, un tema). Intento de enviar un mensaje mayor de 256 KB. El servidor o servicio encontró un error durante el procesamiento de la solicitud. Consulte el mensaje de excepción para obtener más detalles. Por lo general, se trata de una excepción transitoria. |Compruebe el código y asegúrese de que solo se usan objetos serializables en el cuerpo del mensaje (o use un serializador personalizado). Consulte la documentación de los tipos de valor de las propiedades admitidos y use solo los tipos compatibles. Compruebe la propiedad [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) . Si es **true**, puede volver a intentar la operación. |El comportamiento de reintento es indefinido y quizá no resulte útil. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Se intenta crear una entidad con un nombre que ya se usa en otra entidad de ese espacio de nombres de servicio. |Elimine la entidad existente o elija un nombre diferente para la entidad que quiere crear. |El reintento no le será de ayuda. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |La entidad de mensajería alcanzó su tamaño máximo permitido. Esto puede suceder si ya ha abierto el número máximo de destinatarios (que es 5) en un nivel de grupo por consumidor. |Cree espacio en la entidad recibiendo mensajes de esta o de sus subcolas. <br /> Consulte [QuotaExceededException](#quotaexceededexception). |El reintento podría resultar útil si los mensajes se eliminan mientras este se lleva a cabo. |
|  | | | |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) |Solicitud para realizar una operación en tiempo de ejecución en una entidad deshabilitada. |Active la entidad. |El reintento podría ser útil si la entidad se activa mientras este se lleva a cabo. |
| [Microsoft.ServiceBus.Messaging.MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /> [Microsoft.Azure.EventHubs.MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Una carga de mensaje supera el límite de 256 KB. Tenga en cuenta que el límite de 256 KB es el tamaño total del mensaje, que puede incluir propiedades del sistema y cualquier sobrecarga. NET. |Reduzca el tamaño de la carga del mensaje y vuelva a intentar la operación. |El reintento no le será de ayuda. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica que se ha superado una cuota de una entidad específica.

Esto puede suceder si ya ha abierto el número máximo de destinatarios (5) en un nivel de grupo por consumidor.

### <a name="event-hubs"></a>Event Hubs
Event Hubs tiene un límite de 20 grupos de consumidores por Centro de eventos. Cuando intenta crear más, recibe [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que la operación iniciada por el usuario está superando el tiempo de espera. 

Para Event Hubs, el tiempo de espera se especifica como parte de la cadena de conexión o a través de [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). El propio mensaje de error puede variar, pero siempre contiene el valor de tiempo de espera especificado para la operación actual. 

### <a name="common-causes"></a>Causas comunes
Hay dos causas comunes de este error: configuración incorrecta o un error de servicio transitorio.

1. **Configuración incorrecta** : es posible que el tiempo de espera de la operación sea demasiado breve para la condición operativa. El valor predeterminado para el tiempo de espera de la operación en el SDK de cliente es 60 segundos. Compruebe si su código tiene el valor establecido en algo demasiado pequeño. Tenga en cuenta que la condición de la red y el uso de CPU pueden afectar al tiempo que tarda en completarse una operación particular, por lo que el tiempo de espera de la misma no debe establecerse en un valor muy pequeño.
2. **Error de servicio transitorio**: a veces, el servicio Event Hubs puede experimentar retrasos en el procesamiento de solicitudes; por ejemplo, durante períodos de tráfico elevado. En tales casos, puede reintentar su operación después de un retraso hasta que la operación se realice correctamente. Si la misma operación aún experimenta errores después de varios intentos, visite el [sitio de estado del servicio de Azure](https://azure.microsoft.com/status/) para ver si hay interrupciones del servicio conocidas.

## <a name="serverbusyexception"></a>ServerBusyException

Una excepción [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) o [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) indica que un servidor está sobrecargado. Hay dos códigos de error relevantes para esta excepción.

### <a name="error-code-50002"></a>Código de error 50002

Este error puede producirse por uno de estos dos motivos:

1. La carga no se distribuye uniformemente en todas las particiones del centro de eventos y una partición ha alcanzado la limitación de unidades de rendimiento local.
    
    Solución: revisar la estrategia de distribución de particiones o probar [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) puede que le sirva de ayuda.

2. El espacio de nombres de Event Hubs no tiene suficientes unidades de rendimiento (puede comprobar la pantalla **Métrica** en la ventana de espacio de nombres de Event Hubs en [Azure Portal](https://portal.azure.com) para confirmar). Tenga en cuenta que el portal muestra información agregada (1 minuto), pero el rendimiento se mide en tiempo real, por lo que esto es solo una estimación.

    Solución: aumentar las unidades de rendimiento en el espacio de nombres puede resultar útil. Puede hacerlo en este portal, en la ventana **Escala** de la pantalla de espacio de nombres de Event Hubs.

### <a name="error-code-50001"></a>Código de error 50001

Este error se produce raras veces. Se produce cuando el contenedor que ejecuta el código para su espacio de nombres hace un uso bajo de la CPU: no más de unos pocos segundos antes de que comience el equilibrador de carga de Event Hubs.


## <a name="next-steps"></a>pasos siguientes
Para más información acerca de Event Hubs, visite los vínculos siguientes:

* [Información general de Event Hubs](event-hubs-what-is-event-hubs.md)
* [Creación de un centro de eventos](event-hubs-create.md)
* [Preguntas más frecuentes sobre Event Hubs](event-hubs-faq.md)
