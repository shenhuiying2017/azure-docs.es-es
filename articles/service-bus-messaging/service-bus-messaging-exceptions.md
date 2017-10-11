---
title: "Excepciones de mensajería de Azure Service Bus | Microsoft Docs"
description: "Lista de excepciones de mensajería del Bus de servicio y las acciones sugeridas."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2017
ms.author: sethm
ms.openlocfilehash: 93300ba995f2a556cb90fc657db5cf9ad56b9846
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="service-bus-messaging-exceptions"></a>Excepciones de mensajería de Bus de servicio
En este artículo se enumeran algunas excepciones generadas por las API de mensajería del Bus de servicio de Microsoft Azure. Esta referencia está sujeta a cambios, de modo que compruebe las actualizaciones.

## <a name="exception-categories"></a>Categorías de excepciones
Las API de mensajería generan excepciones que pueden dividirse en las siguientes categorías, junto con la acción asociada que puede realizar para intentar corregirlas. Tenga en cuenta que el significado y las causas de una excepción pueden variar dependiendo del tipo de entidad de mensajería (colas/temas o Event Hubs):

1. Error de codificación de usuario ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Acción general: intente corregir el código antes de continuar.
2. Error de instalación/configuración ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) y [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx)). Acción general: revise la configuración y cámbiela si es necesario.
3. Excepciones transitorias ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Acción general: intente realizar de nuevo la operación o informe a los usuarios.
4. Otras excepciones ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception)). Acción general: específica del tipo de excepción; consulte la tabla de la siguiente sección. 

## <a name="exception-types"></a>Tipos de excepciones
En la tabla siguiente se describen los tipos de excepción de mensajería, sus causas y las acciones sugeridas que puede realizar.

| **Tipo de excepción** | **Descripción/causa/ejemplos** | **Acción sugerida** | **Nota sobre el reintento automático o inmediato** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |El servidor no respondió a la operación solicitada en el tiempo especificado que está controlado por [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). Puede que el servidor haya completado la operación solicitada. Esto se puede producir debido a un retraso de red o a otros retrasos de infraestructura. |Compruebe la coherencia del estado del sistema y vuelva a intentarlo si es necesario. Consulte [Excepciones de tiempo de espera](#timeoutexception). |El reintento podría resultar útil en algunos casos; agregue lógica de reintento al código. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |La operación del usuario solicitada no está permitida en el servidor o servicio. Consulte el mensaje de excepción para obtener detalles. Por ejemplo, [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) generará esta excepción si el mensaje se recibió en el modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) . |Compruebe el código y la documentación. Asegúrese de que la operación solicitada sea válida. |El reintento no le será de ayuda. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Se realiza un intento para invocar una operación en un objeto que ya se ha cerrado, anulado o eliminado. En raras ocasiones, la transacción de ambiente ya se ha eliminado. |Compruebe el código y asegúrese de que no invoca operaciones de un objeto desechado. |El reintento no le será de ayuda. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |El objeto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) no pudo adquirir un token, el token no es válido o no contiene las notificaciones necesarias para realizar la operación. |Asegúrese de que el proveedor de tokens se cree con los valores correctos. Compruebe la configuración del Servicio de control de acceso. |El reintento podría resultar útil en algunos casos; agregue lógica de reintento al código. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Uno o varios de los argumentos proporcionados para el método no son válidos.<br /> El URI proporcionado a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) o [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) contiene segmentos de ruta de acceso.<br /> El esquema de URI proporcionado a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) o [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) no es válido. <br />El valor de la propiedad es mayor de 32 KB. |Compruebe el código de llamada y asegúrese de que los argumentos sean correctos. |El reintento no le será de ayuda. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) |La entidad asociada a la operación no existe o se eliminó. |Asegúrese de que la entidad exista. |El reintento no le será de ayuda. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Se intenta recibir un mensaje con una secuencia numérica concreta. Este mensaje no se encuentra. |Asegúrese de que aún no se recibió el mensaje. Compruebe la cola de correo devuelto para ver si el mensaje se procesó como correo devuelto. |El reintento no le será de ayuda. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |El cliente no puede establecer una conexión al Bus de servicio. |Asegúrese de que el nombre de host proporcionado sea correcto y que este sea accesible. |El reintento podría resultar útil si hay problemas de conectividad intermitente. |
| [ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |El servicio no puede procesar la solicitud en este momento. |El cliente puede esperar durante un período de tiempo y volver a intentar realizar la operación. |El cliente puede volver a intentarlo tras un determinado intervalo de tiempo. Si el reintento genera otra excepción, compruebe el comportamiento de reintento de esa excepción. |
| [MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception) |El token de bloqueo asociado al mensaje expiró o no se encuentra. |Deseche el mensaje. |El reintento no le será de ayuda. |
| [SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception) |Se perdió el bloqueo asociado a esta sesión. |Anule el objeto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) . |El reintento no le será de ayuda. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Excepción de mensajería genérica que puede producirse en los siguientes casos:<br /> Se intentó crear [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) con un nombre o una ruta de acceso que pertenece a un tipo de entidad diferente (por ejemplo, un tema).<br />  Intento de enviar un mensaje mayor de 256 KB. El servidor o servicio encontró un error durante el procesamiento de la solicitud. Consulte el mensaje de excepción para obtener más detalles. Por lo general, se trata de una excepción transitoria. |Compruebe el código y asegúrese de que solo se usan objetos serializables en el cuerpo del mensaje (o use un serializador personalizado). Consulte la documentación de los tipos de valor de las propiedades admitidos y use solo los tipos compatibles. Compruebe la propiedad [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) . Si es **true**, puede volver a intentar la operación. |El comportamiento de reintento es indefinido y quizá no resulte útil. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Se intenta crear una entidad con un nombre que ya se usa en otra entidad de ese espacio de nombres de servicio. |Elimine la entidad existente o elija un nombre diferente para la entidad que quiere crear. |El reintento no le será de ayuda. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |La entidad de mensajería ha alcanzado su tamaño máximo permitido, o bien se superó el número máximo de conexiones en un espacio de nombres. |Cree espacio en la entidad recibiendo mensajes de esta o de sus subcolas. Consulte [QuotaExceededException](#quotaexceededexception). |El reintento podría resultar útil si los mensajes se eliminan mientras este se lleva a cabo. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |El Bus de servicio devuelve esta excepción si se intenta crear una acción de regla no válida. Si se produjo un error al procesar la acción de regla de un mensaje, el Bus de servicio adjunta esta excepción a dicho mensaje. |Compruebe si la acción de regla es correcta. |El reintento no le será de ayuda. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |El Bus de servicio devuelve esta excepción si se intenta crear un filtro no válido. Si se produjo un error al procesar el filtro de un mensaje, el Bus de servicio adjunta esta excepción a dicho mensaje. |Compruebe si el filtro es correcto. |El reintento no le será de ayuda. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Se intenta aceptar una sesión con un id. de sesión específico, pero actualmente esta se encuentra bloqueada por otro cliente. |Asegúrese de que otros clientes desbloqueen la sesión. |El reintento podría ser útil si la sesión se publica mientras este se lleva a cabo. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Hay demasiadas operaciones que forman parte de la transacción. |Reduzca el número de operaciones que forman parte de esta transacción. |El reintento no le será de ayuda. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) |Solicitud para realizar una operación en tiempo de ejecución en una entidad deshabilitada. |Active la entidad. |El reintento podría ser útil si la entidad se activa mientras este se lleva a cabo. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Service Bus devuelve esta excepción si envía un mensaje a un tema que tiene el filtrado previo habilitado y dicho mensaje no coincide con ninguno de los filtros. |Asegúrese de que coincida con al menos un filtro. |El reintento no le será de ayuda. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Una carga de mensaje supera el límite de 256 KB. Tenga en cuenta que el límite de 256 KB es el tamaño total del mensaje, que puede incluir propiedades del sistema y cualquier sobrecarga .NET. |Reduzca el tamaño de la carga del mensaje y vuelva a intentar la operación. |El reintento no le será de ayuda. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |La transacción de ambiente (*Transaction.Current*) no es válida. Puede se haya completado o anulado. La excepción interna puede proporcionar información adicional. | |El reintento no le será de ayuda. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Se intenta realizar una operación en una transacción dudosa o se intenta confirmar una transacción y esta se convierte en dudosa. |La aplicación debe controlar esta excepción (como caso especial), porque puede que ya se haya confirmado la transacción. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica que se ha superado una cuota de una entidad específica.

### <a name="queues-and-topics"></a>Colas y temas
Para las colas y los temas, suele tratarse del tamaño de la cola. La propiedad de mensaje de error contendrá más detalles, como en el ejemplo siguiente:

```
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

El mensaje indica que el tema superaba su límite de tamaño, en este caso 1 GB (el límite de tamaño predeterminado). 

### <a name="namespaces"></a>Espacios de nombres

Para espacios de nombres, [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) puede indicar que una aplicación ha superado el número máximo de conexiones en un espacio de nombres. Por ejemplo:

```
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Causas comunes
Hay dos causas comunes de este error: la cola de mensajes fallidos y receptores de mensajes que no funcionan.

1. **Cola de mensajes fallidos** Un lector no puede completar los mensajes, que se devuelven a la cola o tema al expirar el bloqueo. Esto puede suceder si el lector encuentra una excepción que impide que llame a [BrokeredMessage.Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx). Una vez que un mensaje se ha leído 10 veces, se mueve a la cola de mensajes fallidos de forma predeterminada. La propiedad [QueueDescription.MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx) controla este comportamiento, que tiene un valor predeterminado de 10. Al acumularse los mensajes en la cola de mensajes fallidos, estos ocupan espacio.
   
    Para resolver el problema, lea y complete los mensajes de la cola de mensajes fallidos, igual que haría si se encontraran en cualquier otra cola. La clase [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) incluso contiene un método [FormatDeadLetterPath](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.formatdeadletterpath.aspx) para ayudar a dar formato a la ruta de acceso de la cola de mensajes fallidos.
2. **Receptor detenido** Un receptor ha dejado de recibir mensajes de una cola o suscripción. Para identificar este aspecto, eche un vistazo a la propiedad [QueueDescription.MessageCountDetails](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.messagecountdetails.aspx) , que muestra el desglose completo de los mensajes. Si la propiedad [ActiveMessageCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagecountdetails.activemessagecount.aspx) es alta o creciente, los mensajes no se leen tan rápido como se escriben.

### <a name="event-hubs"></a>Centros de eventos
Centros de eventos tiene un límite de 20 grupos de consumidores por Centro de eventos. Cuando intenta crear más, recibe [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que la operación iniciada por el usuario está superando el tiempo de espera. 

Debe comprobar el valor de la propiedad [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit), porque si alcanza este límite también puede causar una excepción [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### <a name="queues-and-topics"></a>Colas y temas
Para las colas y los temas, el tiempo de espera se especifica en la propiedad [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout) como parte de la cadena de conexión o mediante [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). El propio mensaje de error puede variar, pero siempre contiene el valor de tiempo de espera especificado para la operación actual. 

### <a name="event-hubs"></a>Centros de eventos
Para Centros de eventos, el tiempo de espera se especifica como parte de la cadena de conexión o a través de [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). El propio mensaje de error puede variar, pero siempre contiene el valor de tiempo de espera especificado para la operación actual. 

### <a name="common-causes"></a>Causas comunes
Hay dos causas comunes de este error: configuración incorrecta o un error de servicio transitorio.

1. **Configuración incorrecta** : es posible que el tiempo de espera de la operación sea demasiado breve para la condición operativa. El valor predeterminado para el tiempo de espera de la operación en el SDK de cliente es 60 segundos. Compruebe si su código tiene el valor establecido en algo demasiado pequeño. Tenga en cuenta que la condición de la red y el uso de CPU pueden afectar al tiempo que tarda en completarse una operación particular, por lo que el tiempo de espera de la misma no debe establecerse en un valor muy pequeño.
2. **Error de servicio transitorio** A veces, el servicio Service Bus puede experimentar retrasos en el procesamiento de solicitudes; por ejemplo, durante períodos de tráfico elevado. En tales casos, puede reintentar su operación después de un retraso hasta que la operación se realice correctamente. Si la misma operación aún experimenta errores después de varios intentos, visite el [sitio de estado del servicio de Azure](https://azure.microsoft.com/status/) para ver si hay interrupciones del servicio conocidas.

## <a name="next-steps"></a>Pasos siguientes

Para obtener la referencia completa de la API de .NET para Service Bus, consulte la [referencia de la API de .NET de Azure](/dotnet/api/overview/azure/servicebus).

Para más información sobre [Service Bus](https://azure.microsoft.com/services/service-bus/), consulte los temas siguientes.

* [Introducción a la mensajería del Bus de servicio](service-bus-messaging-overview.md)
* [Elementos fundamentales de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Arquitectura del Bus de servicio](service-bus-architecture.md)

