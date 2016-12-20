---
title: Excepciones de Relay | Microsoft Docs
description: Lista de excepciones de Relay y acciones sugeridas
services: service-bus-relay
documentationcenter: na
author: jtaubensee
manager: timlt
editor: tysonn
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3feece5fc2497aec59bd065c345b21e800c87fd2


---
# <a name="relay-exceptions"></a>Excepciones de Relay
En este artículo se enumeran algunas excepciones generadas por las API de Relay de Microsoft Azure. Esta referencia está sujeta a cambios, de modo que compruebe las actualizaciones.

## <a name="exception-categories"></a>Categorías de excepciones
Las API de Relay generan excepciones que pueden dividirse en las siguientes categorías, junto con la acción asociada que puede realizar para tratar de corregirlas.

1. Error de codificación de usuario ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Acción general: intente corregir el código antes de continuar.
2. Error de instalación/configuración ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitynotfoundexception.aspx) y [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx)). Acción general: revise la configuración y cámbiela si es necesario.
3. Excepciones transitorias ([Microsoft.ServiceBus.Messaging.MessagingException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx), [Microsoft.ServiceBus.Messaging.ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingcommunicationexception.aspx)). Acción general: intente realizar de nuevo la operación o informe a los usuarios.
4. Otras excepciones ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagelocklostexception.aspx), [Microsoft.ServiceBus.Messaging.SessionLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessionlocklostexception.aspx)). Acción general: específica del tipo de excepción; consulte la tabla de la siguiente sección. 

## <a name="exception-types"></a>Tipos de excepciones
En la tabla siguiente se describen los tipos de excepción de mensajería, sus causas y las acciones sugeridas que puede realizar.

| **Tipo de excepción** | **Descripción** | **Acción sugerida** | **Nota sobre el reintento automático o inmediato** |
| --- | --- | --- | --- |
| [Tiempo de espera](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |El servidor no respondió a la operación solicitada en el tiempo especificado que está controlado por [OperationTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx). Puede que el servidor haya completado la operación solicitada. Esto se puede producir debido a un retraso de red o a otros retrasos de infraestructura. |Compruebe la coherencia del estado del sistema y vuelva a intentarlo si es necesario. Consulte [Excepciones de tiempo de espera](#timeoutexception). |El reintento podría resultar útil en algunos casos; agregue lógica de reintento al código. |
| [Operación no válida](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |La operación del usuario solicitada no está permitida en el servidor o servicio. Consulte el mensaje de excepción para obtener detalles. Por ejemplo, [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) generará esta excepción si el mensaje se recibió en el modo **ReceiveAndDelete** . |Compruebe el código y la documentación. Asegúrese de que la operación solicitada sea válida. |El reintento no le será de ayuda. |
| [Operación cancelada](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Se realiza un intento para invocar una operación en un objeto que ya se ha cerrado, anulado o eliminado. En raras ocasiones, la transacción de ambiente ya se ha eliminado. |Compruebe el código y asegúrese de que no invoca operaciones de un objeto desechado. |El reintento no le será de ayuda. |
| [Acceso no autorizado](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |El objeto [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) no pudo adquirir un token, el token no es válido o no contiene las notificaciones necesarias para realizar la operación. |Asegúrese de que el proveedor de tokens se cree con los valores correctos. Compruebe la configuración del Servicio de control de acceso. |El reintento podría resultar útil en algunos casos; agregue lógica de reintento al código. |
| [Excepción de argumento](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [Argumento Null](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[Argumento fuera del intervalo](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Uno o varios de los argumentos proporcionados para el método no son válidos.<br /> El URI proporcionado a [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) o [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx) contiene segmentos de ruta de acceso.<br /> El esquema de URI proporcionado a [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) o [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx) no es válido. <br />El valor de la propiedad es mayor de 32 KB. |Compruebe el código de llamada y asegúrese de que los argumentos sean correctos. |El reintento no le será de ayuda. |
| [Servidor ocupado](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx) |El servicio no puede procesar la solicitud en este momento. |El cliente puede esperar durante un período de tiempo y volver a intentar realizar la operación. |El cliente puede volver a intentarlo tras un determinado intervalo de tiempo. Si el reintento genera otra excepción, compruebe el comportamiento de reintento de esa excepción. |
| [Cuota superada](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx) |La entidad de mensajería alcanzó su tamaño máximo permitido. |Cree espacio en la entidad recibiendo mensajes de esta o de sus subcolas. Consulte [QuotaExceededException](#quotaexceededexception). |El reintento podría resultar útil si los mensajes se eliminan mientras este se lleva a cabo. |
| [Tamaño de mensaje superado](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesizeexceededexception.aspx) |Una carga de mensaje supera el límite de 256 KB. Tenga en cuenta que el límite de 256 KB es el tamaño total del mensaje, que puede incluir propiedades del sistema y cualquier sobrecarga. NET. |Reduzca el tamaño de la carga del mensaje y vuelva a intentar la operación. |El reintento no le será de ayuda. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx) indica que se ha superado una cuota de una entidad específica.

Para Relay, esta excepción encapsula [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), lo que indica que el número máximo de agentes de escucha se ha superado para este punto de conexión. Esto se indica en el valor **MaximumListenersPerEndpoint** del mensaje de excepción.

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que la operación iniciada por el usuario está superando el tiempo de espera. 

Debe comprobar el valor de la propiedad [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit), porque si alcanza este límite también puede causar una excepción [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

Para Relay, puede que reciba excepciones de tiempo de espera al abrir por primera vez una conexión de remitente de la retransmisión. Hay dos causas comunes de esta excepción:

1. Puede que el valor [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) sea demasiado pequeño (incluso una fracción de un segundo).
2. Es posible que las escuchas de retransmisión locales no respondan (o puede que encuentren problemas relativos a las reglas de firewall que prohíban a las escuchas aceptar nuevas conexiones de cliente) y que el valor [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) sea inferior a unos 20 segundos.

Por ejemplo:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10. The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Causas comunes
Hay dos causas comunes de este error: configuración incorrecta o un error de servicio transitorio.

1. **Configuración incorrecta**
   : es posible que el tiempo de espera de la operación sea demasiado breve para la condición operativa. El valor predeterminado para el tiempo de espera de la operación en el SDK de cliente es 60 segundos. Compruebe si su código tiene el valor establecido en algo demasiado pequeño. Tenga en cuenta que la condición de la red y el uso de CPU pueden afectar al tiempo que tarda en completarse una operación particular, por lo que el tiempo de espera de la misma no debe establecerse en un valor muy pequeño.
2. **Error de servicio transitorio**
   : a veces, el servicio Centros de eventos puede experimentar retrasos en el procesamiento de solicitudes; por ejemplo, durante períodos de tráfico elevado. En tales casos, puede reintentar su operación después de un retraso hasta que la operación se realice correctamente. Si la misma operación aún experimenta errores después de varios intentos, visite el [sitio de estado del servicio de Azure](https://azure.microsoft.com/status/) para ver si hay interrupciones del servicio conocidas.

## <a name="next-steps"></a>Pasos siguientes:
* [Preguntas más frecuentes acerca de Relay](relay-faq.md)
* [Creación de un espacio de nombres](relay-create-namespace-portal.md)
* [Introducción a .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introducción a Node](relay-hybrid-connections-node-get-started.md)




<!--HONumber=Nov16_HO3-->


