---
title: "Excepciones de mensajería de Event Hubs | Microsoft Docs"
description: "Lista de excepciones de mensajería y acciones sugeridas de Centros de eventos de Azure."
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
ms.date: 10/14/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c17328ffee6191022fcfd9588b3d30b0cea23148


---
# <a name="event-hubs-messaging-exceptions"></a>Excepciones de mensajería de Centros de eventos
En este artículo se enumeran algunas excepciones generadas por las API de mensajería del Bus de servicio de Azure. Este conjunto de API incluye Centros de eventos. Esta referencia está sujeta a cambios, de modo que compruebe las actualizaciones.

## <a name="exception-categories"></a>Categorías de excepciones
Las API de mensajería generan excepciones que pueden dividirse en las siguientes categorías, junto con la acción asociada que puede realizar para intentar corregirlas. Tenga en cuenta que el significado y las causas de una excepción pueden variar dependiendo del tipo de entidad de mensajería (retransmisiones, colas/temas, Centros de eventos):

1. Error de codificación de usuario ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Acción general: intente corregir el código antes de continuar.
2. Error de instalación/configuración ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitynotfoundexception.aspx) y [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx)). Acción general: revise la configuración y cámbiela si es necesario.
3. Excepciones transitorias ([Microsoft.ServiceBus.Messaging.MessagingException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx), [Microsoft.ServiceBus.Messaging.ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingcommunicationexception.aspx)). Acción general: intente realizar de nuevo la operación o informe a los usuarios.
4. Otras excepciones ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagelocklostexception.aspx), [Microsoft.ServiceBus.Messaging.SessionLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessionlocklostexception.aspx)). Acción general: específica del tipo de excepción; consulte la tabla de la siguiente sección. 

## <a name="exception-types"></a>Tipos de excepciones
En la tabla siguiente se describen los tipos de excepción de mensajería, sus causas y las acciones sugeridas que puede realizar.

| **Tipo de excepción** | **Descripción/causa/ejemplos** | **Acción sugerida** | **Nota sobre el reintento automático o inmediato** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |El servidor no respondió a la operación solicitada en el tiempo especificado que está controlado por [OperationTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx). Puede que el servidor haya completado la operación solicitada. Esto se puede producir debido a un retraso de red o a otros retrasos de infraestructura. |Compruebe la coherencia del estado del sistema y vuelva a intentarlo si es necesario. |El reintento podría resultar útil en algunos casos; agregue lógica de reintento al código. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |La operación del usuario solicitada no está permitida en el servidor o servicio. Consulte el mensaje de excepción para obtener detalles. Por ejemplo, [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) genera esta excepción si el mensaje se recibió en el modo **ReceiveAndDelete** . |Compruebe el código y la documentación. Asegúrese de que la operación solicitada sea válida. |El reintento no le será de ayuda. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Se realiza un intento para invocar una operación en un objeto que ya se ha cerrado, anulado o eliminado. En raras ocasiones, la transacción de ambiente ya se ha eliminado. |Compruebe el código y asegúrese de que no invoca operaciones de un objeto desechado. |El reintento no le será de ayuda. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |El objeto [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) no pudo adquirir un token, el token no es válido o no contiene las notificaciones necesarias para realizar la operación. |Asegúrese de que el proveedor de tokens se cree con los valores correctos. Compruebe la configuración del Servicio de control de acceso. |El reintento podría resultar útil en algunos casos; agregue lógica de reintento al código. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Uno o varios de los argumentos proporcionados para el método no son válidos. El URI proporcionado a [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) o [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx) contiene segmentos de ruta de acceso. El esquema de URI proporcionado a [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) o [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx) no es válido. El valor de la propiedad es mayor de 32 KB. |Compruebe el código de llamada y asegúrese de que los argumentos sean correctos. |El reintento no le será de ayuda. |
| [MessagingEntityNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitynotfoundexception.aspx) |La entidad asociada a la operación no existe o se eliminó. |Asegúrese de que la entidad exista. |El reintento no le será de ayuda. |
| [MessageNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagenotfoundexception.aspx) |Se intenta recibir un mensaje con una secuencia numérica concreta. Este mensaje no se encuentra. |Asegúrese de que aún no se recibió el mensaje. Compruebe la cola de correo devuelto para ver si el mensaje se procesó como correo devuelto. |El reintento no le será de ayuda. |
| [MessagingCommunicationException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingcommunicationexception.aspx) |El cliente no puede establecer una conexión al Centro de eventos. |Asegúrese de que el nombre de host proporcionado sea correcto y que este sea accesible. |El reintento podría resultar útil si hay problemas de conectividad intermitente. |
| [ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx) |El servicio no puede procesar la solicitud en este momento. |El cliente puede esperar durante un período de tiempo y volver a intentar realizar la operación. |El cliente puede volver a intentarlo tras un determinado intervalo de tiempo. Si el reintento genera otra excepción, compruebe el comportamiento de reintento de esa excepción. |
| [MessageLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagelocklostexception.aspx) |El token de bloqueo asociado al mensaje expiró o no se encuentra. |Deseche el mensaje. |El reintento no le será de ayuda. |
| [SessionLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessionlocklostexception.aspx) |Se perdió el bloqueo asociado a esta sesión. |Anule el objeto [MessageSession](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.aspx) . |El reintento no le será de ayuda. |
| [MessagingException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx) |Excepción de mensajería genérica que puede producirse en los siguientes casos: intento de crear [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) con un nombre o una ruta de acceso que pertenece a un tipo de entidad diferente (por ejemplo, un tema). Intento de enviar un mensaje mayor de 256 KB. El servidor o servicio encontró un error durante el procesamiento de la solicitud. Consulte el mensaje de excepción para obtener más detalles. Por lo general, se trata de una excepción transitoria. |Compruebe el código y asegúrese de que solo se usan objetos serializables en el cuerpo del mensaje (o use un serializador personalizado). Consulte la documentación de los tipos de valor de las propiedades admitidos y use solo los tipos compatibles. Compruebe la propiedad [IsTransient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.istransient.aspx) . Si es **true**, puede volver a intentar la operación. |El comportamiento de reintento es indefinido y quizá no resulte útil. |
| [MessagingEntityAlreadyExistsException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentityalreadyexistsexception.aspx) |Se intenta crear una entidad con un nombre que ya se usa en otra entidad de ese espacio de nombres de servicio. |Elimine la entidad existente o elija un nombre diferente para la entidad que quiere crear. |El reintento no le será de ayuda. |
| [QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx) |La entidad de mensajería alcanzó su tamaño máximo permitido. Esto puede suceder si ya ha abierto el número máximo de destinatarios (que es 5) en un nivel de grupo por consumidor. |Cree espacio en la entidad recibiendo mensajes de esta o de sus subcolas. |El reintento podría resultar útil si los mensajes se eliminan mientras este se lleva a cabo. |
|  | | | |
| [SessionCannotBeLockedException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessioncannotbelockedexception.aspx) |Se intenta aceptar una sesión con un id. de sesión específico, pero actualmente esta se encuentra bloqueada por otro cliente. |Asegúrese de que otros clientes desbloqueen la sesión. |El reintento podría ser útil si la sesión se publica mientras este se lleva a cabo. |
| [TransactionSizeExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transactionsizeexceededexception_methods.aspx) |Hay demasiadas operaciones que forman parte de la transacción. |Reduzca el número de operaciones que forman parte de esta transacción. |El reintento no le será de ayuda. |
| [MessagingEntityDisabledException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitydisabledexception.aspx) |Solicitud para realizar una operación en tiempo de ejecución en una entidad deshabilitada. |Active la entidad. |El reintento podría ser útil si la entidad se activa mientras este se lleva a cabo. |
| [MessageSizeExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesizeexceededexception.aspx) |Una carga de mensaje supera el límite de 256 KB. Tenga en cuenta que el límite de 256 KB es el tamaño total del mensaje, que puede incluir propiedades del sistema y cualquier sobrecarga. NET. |Reduzca el tamaño de la carga del mensaje y vuelva a intentar la operación. |El reintento no le será de ayuda. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |La transacción de ambiente (*Transaction.Current*) no es válida. Puede se haya completado o anulado. La excepción interna puede proporcionar información adicional. | |El reintento no le será de ayuda. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Se intenta realizar una operación en una transacción dudosa o se intenta confirmar una transacción y esta se convierte en dudosa. |La aplicación debe controlar esta excepción (como caso especial), porque puede que ya se haya confirmado la transacción. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx) indica que se ha superado una cuota de una entidad específica.

Esto puede suceder si ya ha abierto el número máximo de destinatarios (5) en un nivel de grupo por consumidor.

### <a name="event-hubs"></a>Event Hubs
Centros de eventos tiene un límite de 20 grupos de consumidores por Centro de eventos. Cuando intenta crear más, recibe [QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx). 

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que la operación iniciada por el usuario está superando el tiempo de espera. 

### <a name="event-hubs"></a>Centros de eventos
Para Centros de eventos, el tiempo de espera se especifica como parte de la cadena de conexión o a través de [ServiceBusConnectionStringBuilder](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.aspx). El propio mensaje de error puede variar, pero siempre contiene el valor de tiempo de espera especificado para la operación actual. 

### <a name="common-causes"></a>Causas comunes
Hay dos causas comunes de este error: configuración incorrecta o un error de servicio transitorio.

1. **Configuración incorrecta**
   : es posible que el tiempo de espera de la operación sea demasiado breve para la condición operativa. El valor predeterminado para el tiempo de espera de la operación en el SDK de cliente es 60 segundos. Compruebe si su código tiene el valor establecido en algo demasiado pequeño. Tenga en cuenta que la condición de la red y el uso de CPU pueden afectar al tiempo que tarda en completarse una operación particular, por lo que el tiempo de espera de la misma no debe establecerse en un valor muy pequeño.
2. **Error de servicio transitorio**
   : a veces, el servicio Event Hubs puede experimentar retrasos en el procesamiento de solicitudes; por ejemplo, durante períodos de tráfico elevado. En tales casos, puede reintentar su operación después de un retraso hasta que la operación se realice correctamente. Si la misma operación aún experimenta errores después de varios intentos, visite el [sitio de estado del servicio de Azure](https://azure.microsoft.com/status/) para ver si hay interrupciones del servicio conocidas.

## <a name="next-steps"></a>Pasos siguientes
Para obtener la referencia completa de la API de .NET de Bus de servicio y Centros de eventos, consulte la [Referencia de Azure en MSDN](https://msdn.microsoft.com/library/azure/mt419900.aspx).




<!--HONumber=Nov16_HO3-->


