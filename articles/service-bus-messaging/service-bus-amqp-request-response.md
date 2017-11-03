---
title: El protocolo AMQP 1.0 de las operaciones de respuesta o solicitud de Azure Service Bus | Microsoft Docs
description: Lista de operaciones de respuesta/solicitud de Microsoft Azure Service Bus
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: sethm
ms.openlocfilehash: 31f5a11cda1827a0a50fd2be0b7260daedf26e24
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2017
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>El protocolo AMQP 1.0 de Microsoft Azure Service Bus: operaciones de respuesta/solicitud

En este tema se define la lista de operaciones de respuesta/solicitud de Microsoft Azure Service Bus. Esta información se basa en la versión 1.0 de Administración de AMQP (fase de borrador).  
  
Si desea obtener una guía detallada a nivel de conexión del protocolo AMQP 1.0, que describe cómo se implementa Service Bus y se construye sobre la especificación técnica de OASIS AMQP, consulte la [Guía del protocolo AMQP 1.0 en Service Bus de Azure y Event Hubs][Guía del protocolo AMQP 1.0].  
  
## <a name="concepts"></a>Conceptos  
  
### <a name="entity-description"></a>Descripción de entidad  

Una descripción de entidad hace referencia a un objeto [Clase QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription), [Clase TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription) o [Clase SubscriptionDescription](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) de Service Bus.  
  
### <a name="brokered-message"></a>Mensaje asincrónico  

Representa un mensaje de Service Bus que se asigna a un mensaje de AMQP. La asignación se define en la [guía del protocolo AMQP de Service Bus](service-bus-amqp-protocol-guide.md).  
  
## <a name="attach-to-entity-management-node"></a>Asociación al nodo de administración de entidades  

Todas las operaciones descritas en este documento siguen un patrón de solicitud/respuesta, se limitan a una entidad y requieren asociarse a un nodo de administración de entidades.  
  
### <a name="create-link-for-sending-requests"></a>Creación de un vínculo para enviar solicitudes  

Crea un vínculo al nodo de administración para enviar solicitudes.  
  
```  
requestLink = session.attach(     
role: SENDER,   
    target: { address: "<entity address>/$management" },   
    source: { address: ""<my request link unique address>" }   
)  
  
```  
  
### <a name="create-link-for-receiving-responses"></a>Creación de un vínculo para recibir respuestas  

Crea un vínculo para recibir respuestas del nodo de administración.  
  
```  
responseLink = session.attach(    
role: RECEIVER,   
    source: { address: "<entity address>/$management" }   
    target: { address: "<my response link unique address>" }   
)  
  
```  
  
### <a name="transfer-a-request-message"></a>Transferencia de un mensaje de solicitud  

Transfiere un mensaje de solicitud.  
  
```  
requestLink.sendTransfer(  
        Message(  
                properties: {  
                        message-id: <request id>,  
                        reply-to: "<my response link unique address>"  
                },  
                application-properties: {  
                        "operation" -> "<operation>",  
                },  
        )  
```  
  
### <a name="receive-a-response-message"></a>Recepción de un mensaje de respuesta  

Recibe el mensaje de respuesta del vínculo de respuesta.  
  
```  
responseMessage = responseLink.receiveTransfer()  
```  
  
El mensaje de respuesta está en el formato siguiente:
  
```  
Message(  
properties: {     
        correlation-id: <request id>  
    },  
    application-properties: {  
            "statusCode" -> <status code>,  
            "statusDescription" -> <status description>,  
           },         
)  
  
```  
  
### <a name="service-bus-entity-address"></a>Dirección de entidades de Service Bus  

Las direcciones de las entidades de Service Bus deben tener el siguiente formato:  
  
|Tipo de entidad|Dirección|Ejemplo|  
|-----------------|-------------|-------------|  
|queue|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|topic|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|subscription|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Operaciones de mensajes  
  
### <a name="message-renew-lock"></a>Bloqueo de renovación de mensajes  

Extiende el bloqueo de un mensaje cuando se especifique en la descripción de la entidad.  
  
#### <a name="request"></a>Solicitud  

El mensaje de solicitud debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|operación|string|Sí|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|No|Tiempo de espera de funcionamiento del servidor en milisegundos.|  
  
 El cuerpo del mensaje de solicitud debe constar de una sección con el valor de AMQP que contiene una asignación con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|Matriz de UUID|Sí|Tokens de bloqueo de renovación de mensajes.|  
  
#### <a name="response"></a>Respuesta  

El mensaje de respuesta debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sí|Código de respuesta HTTP [RFC2616]<br /><br /> 200: operación realizada correctamente; en caso contrario, significa que se ha producido un error.|  
|statusDescription|string|No|Descripción del estado.|  
  
El cuerpo del mensaje de respuesta debe constar de una sección con el valor de AMQP que contiene una asignación con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|expirations|Matriz de marca de tiempo|Sí|Nueva expiración de tokenes de bloqueo de mensajes correspondientes a los tokens de bloqueo de solicitudes.|  
  
### <a name="peek-message"></a>Inspección de mensajes  

Inspecciona los mensajes sin bloquearlos.  
  
#### <a name="request"></a>Solicitud  

El mensaje de solicitud debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|operación|string|Sí|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|No|Tiempo de espera de funcionamiento del servidor en milisegundos.|  
  
El cuerpo del mensaje de solicitud debe constar de una sección con el **valor de AMQP** que contiene una **asignación** con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|Sí|Número de secuencia desde la que se iniciará la inspección.|  
|`message-count`|int|Sí|Número máximo de mensajes que se inspeccionarán.|  
  
#### <a name="response"></a>Respuesta  

El mensaje de respuesta debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sí|Código de respuesta HTTP [RFC2616]<br /><br /> 200: operación realizada correctamente; hay más mensajes.<br /><br /> 0xCC: sin contenido; no hay más mensajes.|  
|statusDescription|string|No|Descripción del estado.|  
  
El cuerpo del mensaje de respuesta debe constar de una sección con el **valor de AMQP** que contiene una **asignación** con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|messages|Lista de asignaciones|Sí|Lista de mensajes en el que cada asignación representa un mensaje.|  
  
La asignación que representa un mensaje debe contener las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|Mensaje|Matriz de byte|Sí|Mensaje codificado con AMQP 1.0.|  
  
### <a name="schedule-message"></a>Programación de mensajes  

Programa mensajes.  
  
#### <a name="request"></a>Solicitud  

El mensaje de solicitud debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|operación|string|Sí|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|No|Tiempo de espera de funcionamiento del servidor en milisegundos.|  
  
El cuerpo del mensaje de solicitud debe constar de una sección con el **valor de AMQP** que contiene una **asignación** con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|messages|Lista de asignaciones|Sí|Lista de mensajes en el que cada asignación representa un mensaje.|  
  
La asignación que representa un mensaje debe contener las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|message-id|string|Sí|`amqpMessage.Properties.MessageId` como cadena|  
|session-id|string|Sí|`amqpMessage.Properties.GroupId as string`|  
|partition-key|string|Sí|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|  
|Mensaje|Matriz de byte|Sí|Mensaje codificado con AMQP 1.0.|  
  
#### <a name="response"></a>Response  

El mensaje de respuesta debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sí|Código de respuesta HTTP [RFC2616]<br /><br /> 200: operación realizada correctamente; en caso contrario, significa que se ha producido un error.|  
|statusDescription|string|No|Descripción del estado.|  
  
El cuerpo del mensaje de respuesta debe constar de una sección con el **valor de AMQP** que contiene una asignación con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|Matriz de long|Sí|Número de secuencia de los mensajes programados. El número de secuencia se utiliza para realizar cancelaciones.|  
  
### <a name="cancel-scheduled-message"></a>Cancelación de mensajes programados  

Cancela mensajes programados.  
  
#### <a name="request"></a>Solicitud  

El mensaje de solicitud debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|operación|string|Sí|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|No|Tiempo de espera de funcionamiento del servidor en milisegundos.|  
  
El cuerpo del mensaje de solicitud debe constar de una sección con el **valor de AMQP** que contiene una **asignación** con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|Matriz de long|Sí|Números de secuencia de mensajes programados que se van a cancelar.|  
  
#### <a name="response"></a>Response  

El mensaje de respuesta debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sí|Código de respuesta HTTP [RFC2616]<br /><br /> 200: operación realizada correctamente; en caso contrario, significa que se ha producido un error.|  
|statusDescription|string|No|Descripción del estado.|  
  
El cuerpo del mensaje de respuesta debe constar de una sección con el **valor de AMQP** que contiene una asignación con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|Matriz de long|Sí|Número de secuencia de los mensajes programados. El número de secuencia se utiliza para realizar cancelaciones.|  
  
## <a name="session-operations"></a>Operaciones de sesiones  
  
### <a name="session-renew-lock"></a>Bloqueo de renovación de sesiones  

Extiende el bloqueo de un mensaje cuando se especifique en la descripción de la entidad.  
  
#### <a name="request"></a>Solicitud  

El mensaje de solicitud debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|operación|string|Sí|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|No|Tiempo de espera de funcionamiento del servidor en milisegundos.|  
  
El cuerpo del mensaje de solicitud debe constar de una sección con el **valor de AMQP** que contiene una **asignación** con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|session-id|string|Sí|Identificador de sesión.|  
  
#### <a name="response"></a>Response  

El mensaje de respuesta debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sí|Código de respuesta HTTP [RFC2616]<br /><br /> 200: operación realizada correctamente; hay más mensajes.<br /><br /> 0xCC: sin contenido; no hay más mensajes.|  
|statusDescription|string|No|Descripción del estado.|  
  
El cuerpo del mensaje de respuesta debe constar de una sección con el **valor de AMQP** que contiene una asignación con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|expiration|timestamp|Sí|Nueva expiración.|  
  
### <a name="peek-session-message"></a>Inspección de mensajes de sesiones  

Inspecciona mensajes de sesiones sin bloquear.  
  
#### <a name="request"></a>Solicitud  

El mensaje de solicitud debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|operación|string|Sí|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|No|Tiempo de espera de funcionamiento del servidor en milisegundos.|  
  
El cuerpo del mensaje de solicitud debe constar de una sección con el **valor de AMQP** que contiene una **asignación** con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|long|Sí|Número de secuencia desde la que se iniciará la inspección.|  
|message-count|int|Sí|Número máximo de mensajes que se inspeccionarán.|  
|session-id|string|Sí|Identificador de sesión.|  
  
#### <a name="response"></a>Response  

El mensaje de respuesta debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sí|Código de respuesta HTTP [RFC2616]<br /><br /> 200: operación realizada correctamente; hay más mensajes.<br /><br /> 0xCC: sin contenido; no hay más mensajes.|  
|statusDescription|string|No|Descripción del estado.|  
  
El cuerpo del mensaje de respuesta debe constar de una sección con el **valor de AMQP** que contiene una asignación con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|messages|Lista de asignaciones|Sí|Lista de mensajes en el que cada asignación representa un mensaje.|  
  
 La asignación que representa un mensaje debe contener las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|Mensaje|Matriz de byte|Sí|Mensaje codificado con AMQP 1.0.|  
  
### <a name="set-session-state"></a>Establecimiento del estado de sesiones  

Establece el estado de una sesión.  
  
#### <a name="request"></a>Solicitud  

El mensaje de solicitud debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|operación|string|Sí|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|No|Tiempo de espera de funcionamiento del servidor en milisegundos.|  
  
El cuerpo del mensaje de solicitud debe constar de una sección con el **valor de AMQP** que contiene una **asignación** con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|session-id|string|Sí|Identificador de sesión.|  
|session-state|Matriz de bytes|Sí|Datos binarios opacos.|  
  
#### <a name="response"></a>Response  

El mensaje de respuesta debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sí|Código de respuesta HTTP [RFC2616]<br /><br /> 200: operación realizada correctamente; en caso contrario, significa que se ha producido un error.|  
|statusDescription|string|No|Descripción del estado.|  
  
### <a name="get-session-state"></a>Obtención del estado de sesiones  

Obtiene el estado de una sesión.  
  
#### <a name="request"></a>Solicitud  

El mensaje de solicitud debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|operación|string|Sí|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|No|Tiempo de espera de funcionamiento del servidor en milisegundos.|  
  
El cuerpo del mensaje de solicitud debe constar de una sección con el **valor de AMQP** que contiene una **asignación** con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|session-id|string|Sí|Identificador de sesión.|  
  
#### <a name="response"></a>Response  

El mensaje de respuesta debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sí|Código de respuesta HTTP [RFC2616]<br /><br /> 200: operación realizada correctamente; en caso contrario, significa que se ha producido un error.|  
|statusDescription|string|No|Descripción del estado.|  
  
El cuerpo del mensaje de respuesta debe constar de una sección con el **valor de AMQP** que contiene una **asignación** con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|session-state|Matriz de bytes|Sí|Datos binarios opacos.|  
  
### <a name="enumerate-sessions"></a>Enumeración de sesiones  

Enumera las sesiones de una entidad de mensajería.  
  
#### <a name="request"></a>Solicitud  

El mensaje de solicitud debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|operación|string|Sí|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|No|Tiempo de espera de funcionamiento del servidor en milisegundos.|  
  
El cuerpo del mensaje de solicitud debe constar de una sección con el **valor de AMQP** que contiene una **asignación** con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|last-updated-time|timestamp|Sí|Filtro para incluir solo las sesiones actualizadas después de un momento dado.|  
|skip|int|Sí|Omite un número de sesiones.|  
|top|int|Sí|Número máximo de sesiones.|  
  
#### <a name="response"></a>Response  

El mensaje de respuesta debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sí|Código de respuesta HTTP [RFC2616]<br /><br /> 200: operación realizada correctamente; hay más mensajes.<br /><br /> 0xCC: sin contenido; no hay más mensajes.|  
|statusDescription|string|No|Descripción del estado.|  
  
El cuerpo del mensaje de respuesta debe constar de una sección con el **valor de AMQP** que contiene una **asignación** con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|skip|int|Sí|Número de sesiones omitidas si el código de estado es 200.|  
|sessions-ids|Matriz de cadenas|Sí|Matriz de identificadores de sesiones si el código de estado es 200.|  
  
## <a name="rule-operations"></a>Operaciones de regla  
  
### <a name="add-rule"></a>Agregar regla  
  
#### <a name="request"></a>Solicitud  

El mensaje de solicitud debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|operación|string|Sí|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|No|Tiempo de espera de funcionamiento del servidor en milisegundos.|  
  
El cuerpo del mensaje de solicitud debe constar de una sección con el **valor de AMQP** que contiene una **asignación** con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|rule-name|string|Sí|Nombre de la regla, sin incluir los nombres de las suscripciones y los temas.|  
|rule-description|map|Sí|Descripción de la regla tal y como se especifica en la sección siguiente.|  
  
La asignación **rule-description** debe incluir las siguientes entradas, donde **sql-filter** y **correlation-filter** son mutuamente excluyentes:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|Sí|`sql-filter`, tal y como se especifica en la sección siguiente.|  
|correlation-filter|map|Sí|`correlation-filter`, tal y como se especifica en la sección siguiente.|  
|sql-rule-action|map|Sí|`sql-rule-action`, tal y como se especifica en la sección siguiente.|  
  
La asignación sql-filter debe incluir las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|expresión|string|Sí|Expresión de filtro SQL.|  
  
La asignación **correlation-filter** debe incluir, al menos, una de las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|correlation-id|string|No||  
|message-id|string|No||  
|to|string|No||  
|reply-to|string|No||  
|label|string|No||  
|session-id|string|No||  
|reply-to-session-id|string|No||  
|content-type|string|No||  
|propiedades|map|No|Se asigna [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Properties) a Service Bus.|  
  
La asignación**sql-rule-action** debe incluir las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|expresión|string|Sí|Expresión de acción SQL.|  
  
#### <a name="response"></a>Response  

El mensaje de respuesta debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sí|Código de respuesta HTTP [RFC2616]<br /><br /> 200: operación realizada correctamente; en caso contrario, significa que se ha producido un error.|  
|statusDescription|string|No|Descripción del estado.|  
  
### <a name="remove-rule"></a>Eliminación de reglas  
  
#### <a name="request"></a>Solicitud  

El mensaje de solicitud debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|operación|string|Sí|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|No|Tiempo de espera de funcionamiento del servidor en milisegundos.|  
  
El cuerpo del mensaje de solicitud debe constar de una sección con el **valor de AMQP** que contiene una **asignación** con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|rule-name|string|Sí|Nombre de la regla, sin incluir los nombres de las suscripciones y los temas.|  
  
#### <a name="response"></a>Response  

El mensaje de respuesta debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sí|Código de respuesta HTTP [RFC2616]<br /><br /> 200: operación realizada correctamente; en caso contrario, significa que se ha producido un error.|  
|statusDescription|string|No|Descripción del estado.|  
  
## <a name="deferred-message-operations"></a>Operaciones de mensajes diferidos  
  
### <a name="receive-by-sequence-number"></a>Recepción por número de secuencia  

Recibe mensajes diferidos por número de secuencia.  
  
#### <a name="request"></a>Solicitud  

El mensaje de solicitud debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|operación|string|Sí|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|No|Tiempo de espera de funcionamiento del servidor en milisegundos.|  
  
El cuerpo del mensaje de solicitud debe constar de una sección con el **valor de AMQP** que contiene una **asignación** con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|Matriz de long|Sí|Número de secuencias.|  
|receiver-settle-mode|ubyte|Sí|Modo de **liquidación de receptor** tal y como se especifica en AMQP Core 1.0.|  
  
#### <a name="response"></a>Response  

El mensaje de respuesta debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sí|Código de respuesta HTTP [RFC2616]<br /><br /> 200: operación realizada correctamente; en caso contrario, significa que se ha producido un error.|  
|statusDescription|string|No|Descripción del estado.|  
  
El cuerpo del mensaje de respuesta debe constar de una sección con el **valor de AMQP** que contiene una **asignación** con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|messages|Lista de asignaciones|Sí|Lista de mensajes donde cada asignación representa un mensaje.|  
  
La asignación que representa un mensaje debe contener las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|lock-token|uuid|Sí|Token de bloqueo si el valor de `receiver-settle-mode` es 1.|  
|Mensaje|Matriz de byte|Sí|Mensaje codificado con AMQP 1.0.|  
  
### <a name="update-disposition-status"></a>Actualización del estado de disposición  

Actualiza el estado de disposición de los mensajes diferidos.  
  
#### <a name="request"></a>Solicitud  

El mensaje de solicitud debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|operación|string|Sí|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|No|Tiempo de espera de funcionamiento del servidor en milisegundos.|  
  
El cuerpo del mensaje de solicitud debe constar de una sección con el **valor de AMQP** que contiene una **asignación** con las siguientes entradas:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|disposition-status|string|Sí|completed<br /><br /> abandoned<br /><br /> suspended|  
|lock-tokens|Matriz de UUID|Sí|Tokens de bloqueo de mensajes para actualizar el estado de disposición.|  
|deadletter-reason|string|No|Puede establecerse si el estado de disposición es **suspended**.|  
|deadletter-description|string|No|Puede establecerse si el estado de disposición es **suspended**.|  
|properties-to-modify|map|No|Lista de propiedades de mensajes asincrónicos de Service Bus que se van a modificar.|  
  
#### <a name="response"></a>Response  

El mensaje de respuesta debe incluir las siguientes propiedades de la aplicación:  
  
|Clave|Tipo de valor|Obligatorio|Contenido del valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sí|Código de respuesta HTTP [RFC2616]<br /><br /> 200: operación realizada correctamente; en caso contrario, significa que se ha producido un error.|  
|statusDescription|string|No|Descripción del estado.|

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre AMQP y Service Bus, visite los siguientes vínculos:

* [Información general sobre AMQP para Service Bus]
* [Guía del protocolo AMQP 1.0]
* [AMQP de Service Bus para Windows Server]

[Información general sobre AMQP para Service Bus]: service-bus-amqp-overview.md
[Guía del protocolo AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP de Service Bus para Windows Server]: https://msdn.microsoft.com/library/dn574799.asp