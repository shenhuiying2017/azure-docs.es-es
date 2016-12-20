---
title: "Preguntas más frecuentes sobre Relay | Microsoft Docs"
description: Respuestas a algunas preguntas frecuentes sobre Relay de Azure.
services: service-bus-relay
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: jotaub,sethm
translationtype: Human Translation
ms.sourcegitcommit: 849d7995e9c74bc929c4f791ae9155ca18ddb77b
ms.openlocfilehash: 12702f585605a88ed6e753a0ff301095e8629e05


---
# <a name="relay-faq"></a>Preguntas más frecuentes sobre Relay
En este artículo se responden algunas preguntas frecuentes sobre Relay de Microsoft Azure. También puede visitar [Preguntas más frecuentes de soporte técnico de Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para información general sobre los precios y el soporte técnico de Azure.

## <a name="general-questions"></a>Preguntas generales
### <a name="what-is-azure-relay"></a>¿Qué es Relay de Azure?
El [servicio Relay](relay-what-is-it.md) de Azure facilita las aplicaciones híbridas, ya que permite exponer de forma segura los servicios que se encuentran en una red corporativa en la nube pública sin tener que abrir una conexión de firewall y sin que sea necesario realizar cambios molestos en una infraestructura de red corporativa.

### <a name="what-is-a-relay-namespace"></a>¿Qué es el espacio de nombres de Relay?
Un [espacio de nombres](relay-create-namespace-portal.md) proporciona un contenedor con un ámbito para el desvío de recursos de Relay en la aplicación. Es necesario crear uno para usar Relay y es uno de los primeros pasos a realizar para empezar.

### <a name="what-happened-to-the-previously-named-relay-service"></a>¿Qué ha ocurrido con el servicio anteriormente llamado Relay?
El servicio anteriormente llamado **Relay** se conoce ahora como *WCF Relay*. Este servicio se puede seguir usando de la manera habitual. Conexiones híbridas es una versión actualizada de un servicio trasplantado de BizTalk. Tanto WCF Relay como Conexiones híbridas seguirán siendo admitidos en el futuro.

## <a name="pricing"></a>Precios
En esta sección responde a algunas preguntas frecuentes sobre la estructura de precios de Relay. También puede visitar [Preguntas más frecuentes de soporte técnico de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para obtener información general sobre los precios de Microsoft Azure. Para obtener más información sobre los precios de Relay, consulte [Precios del Bus de servicio](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>¿Cómo se cobra por Conexiones híbridas y WCF Relay?
Para obtener más información sobre los precios de Relay, consulte [Precios de Service Bus][Información general sobre precios]. Además de los precios indicados, se le cobrará por las transferencias de datos asociadas para salidas del centro de datos en el que se aprovisiona la aplicación.

### <a name="how-am-i-billed-for-hybrid-connections"></a>¿Cómo se factura Conexiones híbridas?
Aquí se muestran tres escenarios de ejemplo:

1. Si tiene un único agente de escucha, por ejemplo, una instancia del administrador de conexiones híbridas instalada y en ejecución continua durante todo el mes, y envía 3 GB de datos a través de la conexión durante el transcurso del mes, el precio total será de 5 dólares.
2. Si tiene un único agente de escucha, por ejemplo, una instancia del administrador de conexiones híbridas instalada y en ejecución continua durante todo el mes, y envía 10 GB de datos a través de la conexión durante el transcurso del mes, el precio total será 7,50 dólares por la conexión y los primeros 5 GB+ 2,50 dólares por los 5 GB de datos adicionales.
3. Si tiene dos instancias, A y B, del administrador de Conexiones híbridas instaladas y en ejecución continua durante todo el mes, y envía 3 GB de datos a través de la conexión A y 6 GB a través de la conexión B, el precio total será 10,50 dólares: 5 dólares por la conexión A + 5 dólares por la conexión B + 0,50 dólares (por el sexto GB de la conexión B).

Tenga en cuenta que los precios usados en los ejemplos son aplicables solo durante el período de versión preliminar y están sujetos a cambios durante la disponibilidad general de Conexiones híbridas.

### <a name="how-are-wcf-relay-hours-calculated"></a>¿Cómo se calculan las horas de WCF Relay?
Las horas de retransmisión se facturan según la cantidad acumulada de tiempo que permanece "abierta" cada retransmisión de Service Bus. Se crea una instancia de una retransmisión y se abre implícitamente en una determinada dirección de Service Bus (URL del espacio de nombres del servicio) siempre que un servicio WCF habilitado para la retransmisión, o "agente de escucha de retransmisión", se conecte primero a esa dirección. Se cierra solo cuando el último agente de escucha se desconecta de su dirección. Por tanto, para la facturación, una retransmisión se considera "abierta" desde el momento en que se conecta el primer agente de escucha de retransmisión hasta el momento en que el último agente de escucha de retransmisión se desconecta de la dirección de Service Bus de dicha retransmisión.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-given-relay"></a>¿Qué ocurre si tengo más de un agente de escucha conectado a una retransmisión determinada?
En algunos casos, una sola retransmisión puede tener varios agentes de escucha conectados. Una retransmisión se considera "abierta" cuando al menos un agente de escucha de retransmisión está conectado a ella. Si se agregan más agentes de escucha a una retransmisión "abierta", obtendrá horas de retransmisión extra. El número de remitentes de retransmisión (clientes que invocan o envían mensajes a retransmisiones) conectados a una retransmisión tampoco no tiene ningún efecto en el cálculo de horas de retransmisión.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>¿Cómo se calcula el contador de mensajes para retransmisiones de WCF?
**Esto solo se aplica a las retransmisiones de WCF y no afecta a Conexiones híbridas**.

En general, los mensajes facturables se calculan para retransmisiones con el mismo método que se ha descrito anteriormente para entidades asincrónicas (colas, temas y suscripciones). Sin embargo, hay algunas diferencias importantes:

El envío de un mensaje a Service Bus Relay se trata como un envío "completo a través" al agente de escucha de retransmisión que recibe el mensaje, en lugar de un envío a la retransmisión de Bus de servicio seguido de una entrega al agente de escucha de retransmisión. Por lo tanto, una invocación de servicio de tipo solicitud-respuesta (de hasta 64 kB) a un agente de escucha de retransmisión producirá dos mensajes facturables: un mensaje facturable por la solicitud y un mensaje facturable por la respuesta (siempre que la respuesta sea también \< = 64 KB). Esto difiere del uso de una cola para mediar entre un cliente y un servicio. En el último caso, el mismo patrón de solicitud-respuesta requeriría el envío de una solicitud a la cola, seguido de una eliminación de la cola/entrega de la cola al servicio, seguido de un envío de respuesta a otra cola y una eliminación de cola/entrega desde esa cola al cliente. Con las mismas suposiciones de tamaño (\< = 64 kB) todo el tiempo, el patrón de cola promediado produciría cuatro mensajes facturables, dos veces el número facturado para implementar el mismo patrón mediante la retransmisión. Por supuesto, existen ventajas en el uso de colas para lograr este patrón, como la duración y la nivelación de la carga. Estas ventajas pueden justificar el gasto adicional.

Las retransmisiones que se abren mediante el enlace WCF netTCPRelay tratan los mensajes no como mensajes individuales, sino como un flujo de datos que fluye a través del sistema. En otras palabras, solo el remitente y el agente de escucha tienen visibilidad en el marco de los mensajes individuales enviados o recibidos mediante este enlace. Por lo tanto, para las retransmisiones con el enlace netTCPRelay, todos los datos se tratan como una secuencia con el fin de calcular los mensajes facturables. En este caso, el Bus de servicio calculará la cantidad total de datos enviados o recibidos a través de una retransmisión individual cada 5 minutos y divide ese total por 64 kB para determinar el número de mensajes facturables para la retransmisión en cuestión durante ese período de tiempo.

## <a name="quotas"></a>Cuotas
| Nombre de cuota | Scope | Tipo | Comportamiento cuando se supera | Valor |
| --- | --- | --- | --- | --- |
| Agentes de escucha simultáneos en una retransmisión |Entidad |Estática |Las solicitudes posteriores de conexiones adicionales se rechazarán y el código de llamada recibirá una excepción. |25 |
| Agentes de escucha simultáneos |En todo el sistema |Estática |Las solicitudes posteriores de conexiones adicionales se rechazarán y el código de llamada recibirá una excepción. |2.000 |
| Conexiones de retransmisión simultáneas por todos los puntos de conexión de retransmisión en un espacio de nombres de servicio |En todo el sistema |Estática |- |5.000 |
| Puntos de conexión de retransmisión por espacio de nombres de servicio |En todo el sistema |Estática |- |10.000 |
| Tamaño de mensaje de las retransmisiones [NetOnewayRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.netonewayrelaybinding.aspx) y [NetEventRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.neteventrelaybinding.aspx) |En todo el sistema |Estática |Se rechazarán los mensajes entrantes que superen estas cuotas y el código de llamada recibirá una excepción. |64 KB |
| Tamaño de mensaje de las retransmisiones [HttpRelayTransportBindingElement](https://msdn.microsoft.com/library/microsoft.servicebus.httprelaytransportbindingelement.aspx) y [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) |En todo el sistema |Estática |- |Ilimitado |

### <a name="does-relay-have-any-usage-quotas"></a>¿Tiene Relay cuotas de uso?
De forma predeterminada, para cualquier servicio en la nube, Microsoft establece una cuota de uso mensual agregada que se calcula en todas las suscripciones del cliente. Dado que entendemos que puede necesitar más de estos límites, póngase en contacto con el servicio de atención al cliente en cualquier momento para que podamos conocer sus necesidades y ajustar estos límites adecuadamente. Para el Bus de servicio, las cuotas de uso agregado son las siguientes:

* 5 millardos de mensajes
* 2 millones de horas de retransmisión

Aunque nos reservamos el derecho de deshabilitar una cuenta de cliente que supere sus cuotas de uso en un mes determinado, se proporcionará una notificación por correo electrónico y se realizarán varios intentos para ponerse en contacto con un cliente antes de llevar a cabo cualquier acción. Los clientes que superen estas cuotas todavía será responsables de los cargos que superen las cuotas.

### <a name="naming-restrictions"></a>Restricciones de nomenclatura
Un espacio de nombres de Relay solo puede tener entre 6 y 50 caracteres.

## <a name="subscription-and-namespace-management"></a>Administración de suscripción y espacio de nombres
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>¿Cómo se migra un espacio de nombres a otra suscripción de Azure?
Puede utilizar los comandos de PowerShell (los encontrará [aquí](../service-bus-messaging/service-bus-powershell-how-to-provision.md#migrate-a-namespace-to-another-azure-subscription)) para mover un espacio de nombres de una suscripción de Azure a otra. Para ejecutar la operación, el espacio de nombres tiene que estar ya activo. Además, el usuario que ejecuta los comandos tiene que ser administrador en las suscripciones de origen y de destino.

## <a name="troubleshooting"></a>Solución de problemas
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-their-suggested-actions"></a>¿Cuáles son algunas de las excepciones generadas por las API de Relay de Azure y sus acciones sugeridas?
El artículo [Excepciones de Relay][Excepciones de Relay] describe algunas excepciones con las acciones que se sugieren.

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>¿Qué es una firma de acceso compartido y qué lenguajes admiten la generación de una firma?
Las firmas de acceso compartido son un mecanismo de autenticación basado en URI y valores hash seguros SHA-256. Para más información sobre cómo generar sus propias firmas en Node, PHP, Java y C\#, consulte el artículo [Las firmas de acceso compartido][Las firmas de acceso compartido].

[Información general sobre precios]: https://azure.microsoft.com/pricing/details/service-bus/
[Excepciones de Relay]: relay-exceptions.md
[Las firmas de acceso compartido]: ../service-bus-messaging/service-bus-sas-overview.md

## <a name="next-steps"></a>Pasos siguientes
* [Creación de un espacio de nombres](relay-create-namespace-portal.md)
* [Introducción a .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introducción a Node](relay-hybrid-connections-node-get-started.md)



<!--HONumber=Nov16_HO3-->


