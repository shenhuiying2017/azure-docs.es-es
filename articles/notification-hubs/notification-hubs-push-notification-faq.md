---
title: "Azure Notification Hubs: preguntas más frecuentes (P+F) | Microsoft Docs"
description: "Preguntas más frecuentes sobre el diseño y la implementación de soluciones en Notification Hubs"
services: notification-hubs
documentationcenter: mobile
author: ysxu
manager: erikre
keywords: "notificación push, notificaciones push, notificaciones push de iOS, notificaciones push de android, inserción de ios, inserción de android"
editor: 
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2017
ms.author: yuaxu
ms.openlocfilehash: d19a1b7c8d50ef0fde3cf65c9fd469bc34a27adc
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Notificaciones push en Azure Notification Hubs: preguntas más frecuentes
## <a name="general"></a>General
### <a name="what-is-the-resource-structure-of-notification-hubs"></a>¿Cuál es la estructura de los recursos de Notification Hubs?

Azure Notification Hubs tiene dos niveles de recursos, concentradores y espacios de nombres. Un concentrador es un recurso de inserción que puede contener la información de inserción multiplataforma de una aplicación. Un espacio de nombres es una colección de concentradores en una región.

La asignación recomendada hace coincidir un espacio de nombres con una aplicación. Dentro de un espacio de nombres, puede tener un concentrador de producción que funcione con la aplicación de producción, un concentrador de prueba que funcione con la aplicación de prueba, etc.

### <a name="what-is-the-price-model-for-notification-hubs"></a>¿Cuál es el modelo de precios para Notification Hubs?
Los detalles más recientes sobre los precios pueden encontrarse en la página [Precios de Notification Hubs]. Notification Hubs se factura a nivel de espacio de nombres. (Si necesita ver la definición de espacio de nombres, consulte "¿Cuál es la estructura de los recursos de Notification Hubs?") Notification Hubs ofrece tres niveles:

* **Gratis**: Este nivel es un buen punto de partida para explorar las funcionalidades de inserción. No se recomienda para las aplicaciones de producción. Se incluyen 500 dispositivos y 1 millón de inserciones por espacio de nombres por mes, sin garantía de Acuerdo de Nivel de Servicio (SLA).
* **Básico**: Este nivel (o el Estándar) se recomienda para las aplicaciones de producción más pequeñas. Se incluyen 200 000 dispositivos y 10 millones de inserciones por espacio de nombres por mes como línea base. Se incluyen opciones de aumento con cuota.
* **Estándar**: Es el nivel recomendado para aplicaciones de producción de tamaño medio a grande. Se incluyen 10 millones de dispositivos y 10 millones de inserciones por espacio de nombres por mes como línea base. Se incluyen opciones de aumento con cuota y funcionalidades de telemetría enriquecida.

Características del nivel Estándar:
* **Telemetría enriquecida**: Puede usar Telemetría por mensaje de Notification Hubs para realizar seguimiento de cualquier solicitud de inserción y comentarios del Sistema de notificación de plataforma para depurar.
* **Multiinquilinato**: Puede trabajar con credenciales del Sistema de notificación de plataforma a nivel de un espacio de nombres. Esta opción permite dividir fácilmente los inquilinos en los concentradores dentro del mismo espacio de nombres.
* **Inserción programada**: Puede programar que las notificaciones se envíen en cualquier momento.

### <a name="what-is-the-notification-hubs-sla"></a>¿Cuál es el SLA de Notification Hubs?
En el caso de los niveles Básico y Estándar de Notification Hubs, las aplicaciones configuradas correctamente pueden enviar notificaciones push o realizar operaciones de administración de registros al menos un 99,9 por ciento del tiempo. Para más información sobre el Acuerdo de Nivel de Servicio, vaya a la página [Contrato de nivel de servicio para Notification Hubs](https://azure.microsoft.com/support/legal/sla/notification-hubs/).

> [!NOTE]
> Dado que las notificaciones push dependen de Sistemas de notificación de plataforma de terceros (como APNs de Apple y FCM de Google), no hay ninguna garantía de Acuerdo de Nivel de Servicio para la entrega de estos mensajes. Una vez que Notification Hubs envía los lotes a los Sistemas de notificación de plataforma (Acuerdo de Nivel de Servicio garantizado), entregar las notificaciones es responsabilidad de los Sistemas de notificación de plataforma (no se garantiza ningún Acuerdo de Nivel de Servicio).

### <a name="which-customers-are-using-notification-hubs"></a>¿Qué clientes utilizan Notification Hubs?
Muchos clientes usan Notification Hubs. Los siguientes son algunos clientes destacados:

* Sochi 2014: cientos de grupos de interés, más de 3 millones de dispositivos y más de 150 millones de notificaciones enviadas en dos semanas. [Caso práctico: Sochi]
* Skanska. [Caso práctico: Skanska]
* Seattle Times. [Caso práctico: Seattle Times]
* Mural.ly. [Caso práctico: Mural.ly]
* 7Digital. [Caso práctico: 7Digital]
* Aplicaciones de Bing: decenas de millones de dispositivos envían 3 millones de notificaciones al día.

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>¿Cómo se puede subir o bajar de nivel mi concentrador o espacio de nombres?
Vaya a **[Azure Portal]** > **Espacios de nombres de Notification Hubs** o **Notification Hubs**. Seleccione el recurso que desea actualizar y, luego, vaya a **Plan de tarifa**. Tenga en cuenta los siguientes requisitos:

* El plan de tarifa actualizado se aplica a *todos* los concentradores del espacio de nombres con el que esté trabajando.
* Si el número de dispositivos supera el límite del nivel al que está cambiando, deberá eliminar dispositivos antes de bajar de nivel.


## <a name="design-and-development"></a>Diseño y desarrollo
### <a name="which-server-side-platforms-do-you-support"></a>¿Qué plataformas de servicio se admiten?
Hay SDK de servidor para .NET, Java, Node.js, PHP y Python. Las API de Notification Hubs se basan en interfaces REST, por lo que puede trabajar directamente con las API de REST si usa distintas plataformas o no desea una dependencia adicional. Para más información, vaya a la página [API de REST de Notification Hubs].

### <a name="which-client-platforms-do-you-support"></a>¿Qué plataformas de cliente se admiten?
Las notificaciones push son compatibles con [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android China (con Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) y [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), [Chrome Apps](notification-hubs-chrome-push-notifications-get-started.md) y [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Para más información, vaya a la página [Tutoriales de introducción a Notification Hubs].

### <a name="do-you-support-text-message-email-or-web-notifications"></a>¿Se admiten notificaciones por mensaje de texto, correo electrónico o web?
Notification Hubs está diseñado principalmente para enviar notificaciones a aplicaciones móviles. No ofrece funcionalidades de correo electrónico ni mensaje de texto. Sin embargo, se pueden integrar plataformas de terceros que proporcionan estas funcionalidades junto con Notification Hubs para enviar notificaciones push nativas mediante [Mobile Apps].

Notification Hubs tampoco proporciona un servicio de entrega de notificaciones push en el explorador listo para usarse. Los clientes pueden implementar esta característica con SignalR sobre las plataformas de servidor admitidas. Si desea enviar notificaciones a aplicaciones de explorador en el espacio aislado de Chrome, consulte el [tutorial de aplicaciones Chrome].

### <a name="how-are-mobile-apps-and-azure-notification-hubs-related-and-when-do-i-use-them"></a>¿Cómo se relacionan Mobile Apps y Azure Notification Hubs y cuándo las uso?
Si tiene un back-end de aplicación móvil existente y desea agregar solo la funcionalidad de enviar notificaciones push, puede usar Azure Notification Hubs. Si desea configurar su back-end de aplicación móvil desde cero, considere usar la característica Mobile Apps de Azure App Service. Una aplicación móvil proporciona un centro de notificaciones para que pueda enviar fácilmente notificaciones push desde el back-end de aplicación móvil. Los precios de Mobile Apps incluyen los gastos base de un centro de notificaciones. Solo se paga cuando se superan las inserciones incluidas. Para más detalles sobre los costos, vaya a la página [Precios de App Service].

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>¿Cuántos dispositivos puedo admitir si envío notificaciones push a través de Notification Hubs?
Consulte la página [Precios de Notification Hubs] para detalles sobre la cantidad de dispositivos admitidos.

Si necesita compatibilidad con más de 10 millones de dispositivos registrados, [póngase en contacto con nosotros](https://azure.microsoft.com/overview/contact-us/) directamente y le ayudaremos a escalar su solución.

### <a name="how-many-push-notifications-can-i-send-out"></a>¿Cuántas notificaciones de inserción puedo enviar?
Dependiendo del nivel seleccionado, Azure Notification Hubs realizará un escalado vertical y automático en función del número de notificaciones que fluyan a través del sistema.

> [!NOTE]
> El costo de uso general puede aumentar en función del número de notificaciones push servidas. Asegúrese de conocer los límites de nivel que se describen en la página [Precios de Notification Hubs].
> 
> 

Nuestros clientes usan Notification Hubs para enviar millones de notificaciones push a diario. No es necesario hacer nada especial para escalar la cobertura de las notificaciones push, siempre y cuando esté usando Azure Notification Hubs.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>¿Cuánto tiempo pasa desde que las notificaciones push enviadas llegan a mi dispositivo?
En un escenario de uso normal, donde la carga entrante es coherente y uniforme, Azure Notification Hubs puede procesar al menos *el envío de 1 millón de notificaciones push en un minuto*. Esta velocidad puede variar según el número de etiquetas, la naturaleza de los envíos entrantes y otros factores externos.

Durante el tiempo estimado de entrega, el servicio calcula los destinos por plataforma y enruta los mensajes Push Notification Service (PNS) según las etiquetas registradas o las expresiones de etiquetas. Enviar notificaciones al dispositivo es responsabilidad de PNS.

PNS no garantiza ningún Acuerdo de Nivel de Servicio por la entrega de notificaciones. Sin embargo, la mayoría de las notificaciones push se entregan a los dispositivos de destino en cuestión de minutos (habitualmente en 10 minutos) desde el momento en que se envían a Notification Hubs. Algunas notificaciones podrían tardar más.

> [!NOTE]
> Azure Notification Hubs tiene una directiva para retirar las notificaciones push que no se pueden entregar a PNS dentro de 30 minutos. Este retraso puede ocurrir por una serie de motivos, aunque más comúnmente se debe a que PNS está limitando la aplicación.
> 
> 

### <a name="is-there-any-latency-guarantee"></a>¿Hay alguna garantía de latencia?
Debido a la naturaleza de las notificaciones push (la entrega la realiza un PNS externo específico para la plataforma), no hay ninguna garantía de latencia. Habitualmente, la mayoría de las notificaciones push se entregan en cuestión de minutos.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>¿Qué es necesario considerar a la hora de diseñar una solución con espacios de nombres y centros de notificaciones?
#### <a name="mobile-appenvironment"></a>Entorno/aplicación móvil
* Use un centro de notificaciones por aplicación móvil por entorno.
* En un escenario multiinquilino, cada inquilino debe tener un centro independiente.
* No comparta nunca el mismo centro de notificaciones para entornos de producción y prueba. Si lo hace, podría tener problemas al enviar notificaciones. (Apple ofrece puntos de conexión de inserción de producción y espacio aislado, cada uno con credenciales independientes).
* De forma predeterminada, puede enviar notificaciones de prueba a los dispositivos registrados a través de Azure Portal o del componente de Azure integrado en Visual Studio. El umbral se establece en 10 dispositivos que se seleccionan de forma aleatoria desde el grupo de registro.

> [!NOTE]
> Si el centro se configuró originalmente con un certificado de espacio aislado de Apple y luego se reconfiguró para usar un certificado de producción de Apple, los tokens originales de dispositivo no son válidos. Los tokens no válidos generan errores en las inserciones. Separe los entornos de producción y prueba y use centros distintos para entornos diferentes.
> 
> 

#### <a name="pns-credentials"></a>Credenciales PNS:
Cuando una aplicación móvil se registra con un portal para desarrolladores de una plataforma (por ejemplo, Apple o Google), se envían tokens de seguridad y un identificador de aplicación. El back-end de aplicación proporciona estos tokens a l PNS de la plataforma para que se puedan enviar notificaciones push a los dispositivos. Los tokens de seguridad pueden tener el formato de certificados (por ejemplo, Apple iOS o Windows Phone) o claves de seguridad (por ejemplo, Google Android o Windows). Se deben configurar en los centros de notificaciones. La configuración habitualmente se hace en el nivel de centro de notificaciones, pero también se puede hacer en el nivel de espacio de nombres en un escenario multiinquilino.

#### <a name="namespaces"></a>Espacios de nombres
Los espacios de nombres pueden usarse para la agrupación de implementación. También se pueden usar para representar todos los centros de notificaciones para todos los inquilinos de la misma aplicación en un escenario multiinquilino.

#### <a name="geo-distribution"></a>Distribución geográfica
La distribución geográfica no siempre es fundamental en el caso de las notificaciones push. Los diversos PNS (por ejemplo, APNs o GCM) que entregan notificaciones push a los dispositivos no están distribuidos de manera uniforme.

Si tiene una aplicación que se usa globalmente, puede crear centros en distintos espacios de nombres; para ello, se usa el servicio de Notification Hubs en distintas regiones de Azure de todo el mundo.

> [!NOTE]
> No se recomienda esta disposición porque aumenta el costo de administración, en especial para los registros. Solo se debe hacer si es explícitamente necesario.
> 
> 

### <a name="should-i-do-registrations-from-the-app-back-end-or-directly-through-client-devices"></a>¿Debo hacer registros desde el back-end de aplicación o directamente a través de los dispositivos cliente?
Los registros desde el back-end de aplicación son útiles cuando tiene que autenticar clientes antes de crear el registro. También son útiles cuando se tienen etiquetas que el back-end de aplicación debe crear o modificar según la lógica de aplicación. Para más información, vaya a las páginas [Registro desde el back-end de la aplicación] y [Administración de registro].

### <a name="what-is-the-push-notification-delivery-security-model"></a>¿Cuál es el modelo de seguridad de entrega de las notificaciones push?
Azure Notification Hubs usa un modelo de seguridad basado en [firma de acceso compartido](../storage/common/storage-dotnet-shared-access-signature-part-1.md). Puede usar los tokens de firma de acceso compartido en el nivel de espacio de nombres raíz o en el nivel de centro de notificaciones detallado. Los tokens de firma de acceso compartido se pueden establecer para que sigan distintas reglas de autorización, por ejemplo, para enviar permisos de mensajes o para escuchar los permisos de notificaciones. Para más información, consulte el documento sobre el [modelo de seguridad de Notification Hubs].

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>¿Cómo debo controlar la carga confidencial de las notificaciones push?
Todas las notificaciones se entregan a los dispositivos de destino mediante el PNS de la plataforma. Cuando se envía una notificación a Azure Notification Hubs, se procesa y transmite al PNS respectivo.

Todas las conexiones, desde el remitente a Azure Notification Hubs al PNS, usan HTTPS.

> [!NOTE]
> Azure Notification Hubs no registra la carga de los mensajes de ninguna manera.
> 
> 

Para enviar cargas confidenciales, se recomienda usar un patrón de inserción segura. El remitente entrega una notificación de ping con un identificador de mensaje al dispositivo sin la carga confidencial. Cuando la aplicación del dispositivo recibe la carga, llama directamente a una API segura para capturar los detalles del mensaje. Para una guía sobre cómo implementar este patrón, vaya a la página del [tutorial sobre inserciones seguras de Notification Hubs].

## <a name="operations"></a>Operaciones
### <a name="what-support-is-provided-for-disaster-recovery"></a>¿Qué compatibilidad se proporciona para la recuperación ante desastres?
En nuestro extremo se proporciona cobertura de recuperación ante desastres de metadatos (el nombre de Notification Hubs, la cadena de conexión y otra información crítica). Cuando se desencadena un escenario de recuperación ante desastres, los datos de registro son el *único segmento* de la infraestructura de Notification Hubs que se pierde. Deberá implementar una solución para volver a rellenar estos datos en el nuevo centro posterior a la recuperación:

1. Cree un centro de notificaciones secundario en otro centro de datos. Se recomienda que cree uno desde el comienzo para protegerse de un evento de recuperación ante desastres que pudiera afectar sus funcionalidades de administración. También puede crear uno en el momento del evento de recuperación ante desastres.

2. Rellene el centro de notificaciones secundario con los registros del centro de notificaciones principal. No se recomienda que intente conservar los registros en ambos centros y mantenerlos sincronizados a medida que se ingresan registros. Esta práctica no funciona bien debido a la tendencia inherente que tienen los registros de expirar en el lado del PNS. Notification Hubs los limpia cuando recibe informaciones del PNS con respecto a los registros expirados o no válidos.  

Hay dos recomendaciones para los back-ends de aplicación:

* Use un back-end de aplicación que mantiene un conjunto determinado de registros en su extremo. Luego puede realizar una inserción masiva en el centro de notificaciones secundario.

* Use un back-end de aplicación que obtiene un volcado habitual de registros desde el centro de notificaciones principal como copia de seguridad. Luego puede realizar una inserción masiva en el centro de notificaciones secundario.

> [!NOTE]
> La funcionalidad de exportación/importación de registros disponible en el nivel Estándar se describe en el documento sobre [exportación e importación de registros].
> 
> 

Si no tiene back-end, cuando la aplicación se inicie en los dispositivos de destino, realizan un registro nuevo en el centro de notificaciones secundario. A la larga, el centro de notificaciones secundario tendrá registrados todos los dispositivos activos.

Habrá un período en el que los dispositivos que no tengan aplicaciones abiertas no recibirán notificaciones.

### <a name="is-there-audit-log-capability"></a>¿Hay alguna funcionalidad de registro de auditoría?
Todas las operaciones de administración de Notification Hubs van a los registros de operaciones, que se exponen en [Azure Portal].

## <a name="monitoring-and-troubleshooting"></a>Supervisión y solución de problemas
### <a name="what-troubleshooting-capabilities-are-available"></a>¿Qué funcionalidades de solución de problemas hay disponibles?
Azure Notification Hubs proporciona varias características de solución de problemas, especialmente para el escenario más común relacionado con las notificaciones quitadas. Para detalles, consulte las notas del producto sobre la [solución de problemas de Notification Hubs].

### <a name="what-telemetry-features-are-available"></a>¿Qué características de telemetría hay disponibles?
Azure Notification Hubs le permite ver los datos de telemetría en [Azure Portal]. Puede encontrar detalles sobre las métricas en la página [Métricas de Notification Hubs].

> [!NOTE]
> El éxito de las notificaciones significa simplemente que las notificaciones push se entregaron al PNS externo (por ejemplo APNs para Apple o GCM para Google). Entregar las notificaciones a los dispositivos de destino es responsabilidad del PNS. Habitualmente, el PNS no muestra las métricas de entrega a terceros.  
> 
> 

También proporciona la funcionalidad de exportar los datos de telemetría mediante programación (en el nivel Estándar). Para detalles, consulte el [ejemplo de métricas de Notification Hubs].

[Azure Portal]: https://portal.azure.com

            [Precios de Notification Hubs]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[Caso práctico: Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[Caso práctico: Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[Caso práctico: Seattle Times]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[Caso práctico: Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[Caso práctico: 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[API de REST de Notification Hubs]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Tutoriales de introducción a Notification Hubs]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[tutorial de aplicaciones Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Registro desde el back-end de la aplicación]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Administración de registro]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[modelo de seguridad de Notification Hubs]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[tutorial sobre inserciones seguras de Notification Hubs]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[solución de problemas de Notification Hubs]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Métricas de Notification Hubs]: https://msdn.microsoft.com/library/dn458822.aspx
[ejemplo de métricas de Notification Hubs]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[exportación e importación de registros]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Mobile Apps]: https://azure.microsoft.com/services/app-service/mobile/
[Precios de App Service]: https://azure.microsoft.com/pricing/details/app-service/
