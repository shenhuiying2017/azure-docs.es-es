---
title: Centros de notificaciones de Azure - preguntas frecuentes (FAQ)
description: "Preguntas más frecuentes sobre el diseño y la implementación de soluciones en los Centros de notificaciones"
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
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9f0e7f071f1aa8fdd95c4959eae884afc60644e9
ms.lasthandoff: 03/25/2017


---
# <a name="push-notifications-with-azure-notification-hubs---frequently-asked-questions"></a>Notificaciones push con los Centros de notificaciones de Azure: preguntas más frecuentes
## <a name="general"></a>General
### <a name="0--what-is-the-resource-structure-of-notification-hubs"></a>0.  ¿Cuál es la estructura de los recursos de Notification Hubs?

Notification Hubs tiene dos niveles de recursos, concentradores y espacios de nombre. Un concentrador es un recurso de inserción que puede contener la información de inserción multiplataforma de una aplicación. Un espacio de nombres es una colección de concentradores en una región.

La asignación recomendada general coloca un espacio de nombres para que coincida con una aplicación. Dentro del espacio de nombres, puede tener un concentrador de producción que funcione con la aplicación de producción, un concentrador de prueba que funcione con la aplicación de prueba, etc.

### <a name="1--what-is-the-price-model-for-notification-hubs"></a>1.  ¿Cuál es el modelo de precios para los Centros de notificaciones?
Los detalles más recientes pueden encontrarse en la página [Precios de Bases de datos centrales de notificaciones] . Notification Hubs se factura a nivel de espacio de nombres (vea la estructura de los recursos anterior para saber lo que es un espacio de nombres) y ofrece tres niveles:

* **Gratis**: Se trata de un buen punto de partida para explorar las funcionalidades de inserción; no se recomienda para las aplicaciones de producción. Se incluyen 500 dispositivos y 1 millón de inserciones por espacio de nombres al mes sin ninguna garantía de Acuerdo de Nivel de Servicio.
* **Básico**: Se recomienda este nivel o el Estándar para las aplicaciones de producción más pequeñas. Se incluyen 200 000 dispositivos y 10 millones de inserciones por espacio de nombres al mes como línea de base, con opciones de crecimiento de cuota.
* **Estándar**: Es el nivel recomendado para aplicaciones de producción de tamaño medio a grande. Se incluyen 10 millones de dispositivos y 10 millones de inserciones por espacio de nombres al mes como línea de base, con opciones de aumento de cuota y funcionalidades de telemetría completa.

Estas son algunas características formidables del nivel Estándar:
* *Telemetría completa*: Notification Hubs ofrece telemetría por mensaje para realizar el seguimiento de las solicitudes de inserción y comentarios del Sistema de notificación de plataforma para depurar.
* *Multiinquilino*: Puede trabajar con credenciales del Sistema de notificación de plataforma (PNS) en un nivel de espacio de nombres. Esto permite dividir fácilmente los inquilinos en los concentradores dentro del mismo espacio de nombres.
* *Inserción programada*: Puede programar que las notificaciones se envíen en cualquier momento.

### <a name="2--what-is-the-notification-hubs-sla"></a>2.  ¿Cuál es el SLA de los Centros de notificaciones?
Para los niveles de Notification Hubs **Básico** y **Estándar**, garantizamos que, al menos el 99,9 % del tiempo, las aplicaciones que estén configuradas correctamente podrán enviar notificaciones push o realizar operaciones de administración de registros con respecto a un Centro de notificaciones implementado en un nivel compatible. Para más información sobre nuestro SLA, visite la página [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/notification-hubs/) .

> [!NOTE]
> Dado que las notificaciones de inserción dependen de Sistemas de notificación de plataforma de terceros (APNS de Apple, FCM de Google, etc.), no hay ninguna garantía de Acuerdo de Nivel de Servicio para la entrega de estos mensajes. Una vez que Notification Hubs procesa por lotes los envíos a PNS (Acuerdo de Nivel de Servicio garantizado), la obtención de las inserciones entregadas depende de PNS (no se garantiza ningún Acuerdo de Nivel de Servicio).

### <a name="3--which-customers-are-using-notification-hubs"></a>3.  ¿Qué clientes utilizan los Centros de notificaciones?
Tenemos una gran cantidad de clientes que utilizan Notification Hubs. A continuación destacamos algunos:

* Sochi 2014: cientos de grupos de interés, más de 3 millones de dispositivos, más de 150 millones de notificaciones enviadas en 2 semanas. [Caso práctico: Sochi]
* Skanska [(caso práctico: Skanska)]
* Seattle Times [(caso práctico: Seattle Times)]
* Mural.ly [(caso práctico: Mural.ly)]
* 7Digital [(caso práctico: 7Digital)]
* Aplicaciones de Bing: decenas de millones de dispositivos, envío de 3 millones de notificaciones al día

### <a name="4-how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>4. ¿Cómo se puede subir o bajar de nivel mi concentrador o espacio de nombres?
Vaya a [Azure Portal], Espacios de nombres de Notification Hubs o Notification Hubs, haga clic en el recurso que desea actualizar y vaya a Plan de tarifa en el panel de navegación. Puede actualizar a cualquier nivel que desee, teniendo en cuenta algunas indicaciones:
* El plan de tarifa actualizado se aplicará a *todos* los concentradores del espacio de nombres con el que esté trabajando.
* Si está bajando el nivel y el número de dispositivo supera el límite del nivel al que está intentando cambiar, necesitará eliminar dispositivos para cumplir el límite para poder bajar de nivel.


## <a name="design--development"></a>Diseño y desarrollo
### <a name="1--which-server-side-platforms-do-you-support"></a>1.  ¿Qué plataformas de servicio se admiten?
Tenemos SDK de servidor para. NET, Java, Node.js, PHP y Python. Además, las API de Notification Hubs se basan en interfaces REST, por lo que puede optar por trabajar directamente con las API de REST si está trabajando con distintas plataformas o no desea una dependencia adicional. Pueden encontrar más detalles en la página [Cómo utilizar la interfaz REST de los Centros de notificación].

### <a name="2--which-client-platforms-do-you-support"></a>2.  ¿Qué plataformas de cliente se admiten?
Se admite el envío de notificaciones de inserción a [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android China (con Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) & [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), [Aplicaciones de Chrome](notification-hubs-chrome-push-notifications-get-started.md) y [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Encontrará más información en nuestra página [Tutoriales de introducción].

### <a name="3--do-you-support-smsemailweb-notifications"></a>3.  ¿Admiten las notificaciones de correo electrónico/web/SMS?
Notification Hubs está diseñado principalmente para enviar notificaciones a aplicaciones móviles y no proporciona funcionalidades de correo electrónico o SMS. Sin embargo, se pueden integrar plataformas de terceros que proporcionan estas funcionalidades junto con Notification Hubs para enviar notificaciones de inserción nativa mediante [Azure Mobile Apps].

Los Centros de notificaciones tampoco proporcionan un servicio de entrega de notificación push en el explorador listo para usarse. Los clientes pueden elegir implementarlo usando SignalR sobre las plataformas de servidor admitidas. Si desea enviar notificaciones a aplicaciones de explorador en el espacio aislado de Chrome, consulte [Introducción a Centros de notificaciones para aplicaciones Chrome].

### <a name="4----what-is-the-relation-between-azure-mobile-apps-and-azure-notification-hubs-and-when-do-i-use-what"></a>4.    ¿Cuál es la relación entre las Aplicaciones móviles de Azure y los Centros de notificaciones de Azure y en qué casos debo utilizar cada uno?
Si tiene un back-end de aplicación móvil existente y solo desea agregar la funcionalidad de enviar notificaciones push, debe utilizar los Centros de notificaciones de Azure. Si desea configurar su back-end de aplicación móvil desde cero, entonces debe considerar utilizar las Aplicaciones móviles de Azure. Una Aplicación móvil de Azure proporciona automáticamente un Centro de notificaciones para que pueda enviar notificaciones push fácilmente desde el back-end de aplicación móvil. Los precios de las Aplicaciones móviles de Azure incluyen los cargos de base para un Centro de notificaciones, y solo tendrá que pagar si supera las inserciones incluidas. Para más detalles sobre los costos puede consultar la página [Precios de servicio de aplicaciones] .

### <a name="5----how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>5.    ¿Cuántos dispositivos puedo admitir si envío notificaciones push a través de los Centros de notificaciones?
Consulte la página [Precios de Bases de datos centrales de notificaciones] para más detalles sobre el número de dispositivos compatibles.

Para determinados escenarios, si necesita admitir más de 10.000.000 dispositivos registrados, [póngase en contacto con nosotros](https://azure.microsoft.com/overview/contact-us/) directamente y le ayudaremos a escalar su solución.

### <a name="6----how-many-push-notifications-can-i-send-out"></a>6.    ¿Cuántas notificaciones de inserción puedo enviar?
Dependiendo del nivel seleccionado, Azure realizará un escalado vertical y automático en función del número de notificaciones que fluyan a través del sistema.

> [!NOTE]
> El costo de uso general puede crecer en función del número de notificaciones push servidas. Asegúrese de que es consciente de los límites de nivel existentes que se describen en la página [Precios de Bases de datos centrales de notificaciones] .
> 
> 

Nuestros clientes ya usan los Centros de notificaciones para enviar millones de notificaciones push al día. No es necesario hacer nada especial para escalar la cobertura de notificaciones push, siempre y cuando esté usando los Centros de notificaciones de Azure.

### <a name="7----how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>7.    ¿Cuánto tiempo pasa desde que las notificaciones push enviadas llegan a mi dispositivo?
Los Centros de notificaciones de Azure procesan por lo menos **el envío de 1 millón de notificaciones push en 1 minuto** en un escenario de uso normal, donde la carga entrante es bastante regular y no tiene picos. Esta velocidad puede variar según el número de etiquetas, la naturaleza de los envíos que llegan y otros factores externos.

Durante el tiempo estimado de entrega, el servicio calcula los destinos por plataforma y enruta mensajes a los servicios de entrega de notificaciones push correspondientes, basándose en las expresiones de etiquetas o en las etiquetas registradas. De aquí en adelante, es responsabilidad de los servicios de las notificaciones de inserción (PNS) enviar la notificación al dispositivo.

Un PNS no garantiza ningún SLA para la entrega de notificaciones; de todas formas, normalmente la gran mayoría de las notificaciones push se entregan a los dispositivos en cuestión de minutos (normalmente en un tiempo máximo de 10 minutos) desde el momento en que se envían a nuestra plataforma. Puede haber algunos valores atípicos que pueden tardar más tiempo.

> [!NOTE]
> Los Centros de notificaciones de Azure tienen una directiva para retirar las notificaciones push que no se puedan entregar a los PNS dentro de un plazo de 30 minutos. Este retraso puede ocurrir por una serie de motivos, más comúnmente porque el PNS está limitando su aplicación.
> 
> 

### <a name="8----is-there-any-latency-guarantee"></a>8.    ¿Hay alguna garantía de latencia?
Debido a la naturaleza de las notificaciones push (la entrega la realiza un servicio externo de notificaciones push específico para la plataforma), no hay ninguna garantía de latencia. Normalmente, la mayoría de las notificaciones push se entregan en un par de minutos.

### <a name="9----what-are-the-considerations-i-need-to-take-into-account-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>9.    ¿Cuáles son las consideraciones que tengo que tener en cuenta a la hora de diseñar una solución con espacios de nombres y Centros de notificaciones?
#### <a name="mobile-appenvironment"></a>Entorno/aplicación móvil
* Debe haber un Centro de notificaciones por entorno y aplicación móvil.
* En un escenario multiinquilino, cada inquilino debe tener un centro independiente.
* Nunca debe compartir el mismo centro de notificaciones entre entornos de producción y de prueba, ya que esto puede causar problemas posteriores al enviar notificaciones. Por ejemplo, Apple ofrece extremos de espacio aislado y de inserción de producción, cada uno con unas credenciales distintas.
* De forma predeterminada, puede enviar notificaciones de prueba a los dispositivos registrados a través del Portal de Azure o el componente de Azure integrado en Visual Studio. El umbral se establece en 10 dispositivos que se seleccionan de forma aleatoria desde el grupo de registro.

> [!NOTE]
> Si el grupo se ha configurado originalmente con un certificado de espacio aislado de Apple y, a continuación, se vuelve a configurar para usar un certificado de producción de Apple, los antiguos tokens del dispositivo dejan de ser válidos con el nuevo certificado y la inserción no se realiza correctamente. Es mejor separar los entornos de producción y de prueba y utilizar centros diferentes para entornos diferentes.
> 
> 

#### <a name="pns-credentials"></a>Credenciales PNS:
Cuando una aplicación móvil se registra con un portal para desarrolladores de una plataforma (por ejemplo, Apple, Google, etc.), obtendrá un identificador de aplicación y tokens de seguridad que el back-end de la aplicación necesita proporcionar a los servicios de notificación de inserción de la plataforma para poder enviar notificaciones de inserción a los dispositivos. Estos tokens de seguridad, que pueden ser en forma de certificados (por ejemplo, para Apple iOS o Windows Phone) o de claves de seguridad (Google Android o Windows, entre otros), deben configurarse en los Centros de notificaciones. Esto se hace normalmente en el nivel de concentrador de notificación, pero también puede realizarse en el nivel de espacio de nombres en un escenario de varios inquilinos.

#### <a name="namespaces"></a>Espacios de nombres
Los espacios de nombres pueden usarse para la agrupación de implementación.  También puede utilizarse para representar todos los Centros de notificaciones para todos los inquilinos de la misma aplicación en el escenario multiinquilino.

#### <a name="geo-distribution"></a>Distribución geográfica
La distribución geográfica no siempre es fundamental en el caso de las notificaciones push. Es necesario recordar que varios servicios de notificación push (como APNS, GCM, etc.), que son los que en última instancia entregan las notificaciones push a los dispositivos, no están distribuidos de manera uniforme.

Si tiene una aplicación que se usa a nivel mundial, puede crear varios centros en distintos espacios de nombres para sacar provecho de la disponibilidad del servicio de los Centros de notificaciones en diferentes regiones de Azure en todo el mundo.

> [!NOTE]
> Esto aumentará el costo de administración, especialmente en relación con los registros, por lo que no es muy recomendable y solo debe realizarse si es realmente necesario.
> 
> 

### <a name="10----should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>10.    ¿Debo hacer registros desde el back-end de aplicación o directamente a través de los dispositivos cliente?
Los registros desde el back-end de la aplicación son útiles cuando se tiene que realizar una autenticación de cliente antes de crear el registro, o cuando se tienen etiquetas que el back-end de aplicación tiene que crear o modificar basándose en alguna lógica de aplicación. Para más información, vea las páginas [Registro desde el back-end de la aplicación] y [Administración de registro].

### <a name="11----what-is-the-push-notification-delivery-security-model"></a>11.    ¿Cuál es el modelo de seguridad de entrega de las notificaciones push?
Azure Notification Hubs usa un modelo de seguridad basado en la [firma de acceso compartido (SAS)](../storage/storage-dotnet-shared-access-signature-part-1.md). Puede utilizar los tokens SAS en el nivel de espacio de nombres raíz o en el nivel detallado de los Centros de notificaciones. Estos tokens de SAS pueden establecerse con distintas reglas de autorización; por ejemplo, permisos de envío de mensajes, permisos de notificaciones de escucha, etc. Encontrará más detalles en el documento [Seguridad].

### <a name="12----how-should-i-handle-sensitive-payload-in-the-push-notifications"></a>12.    ¿Cómo debo controlar las cargas confidenciales en las notificaciones push?
Todas las notificaciones se entregan a los dispositivos mediante los servicios de notificación push (PNS) de las plataformas. Cuando un remitente envía una notificación a los centros de notificaciones de Azure, procesamos y pasamos la notificación a las PNS correspondientes.

Todas las conexiones desde el remitente a los Centros de notificaciones de Azure y a la PNS usan HTTPS.

> [!NOTE]
> Los Centros de notificaciones de Azure no registran la carga del mensaje de ninguna manera.
> 
> 

No obstante, para enviar cargas confidenciales, recomendamos un patrón de inserción seguro en el que el remitente envía una notificación "ping" con un identificador de mensaje al dispositivo sin la carga confidencial y, cuando la aplicación en el dispositivo recibe esta carga, llama a una API de aplicación segura directamente para capturar los detalles del mensaje. Una guía sobre cómo implementar el patrón descrito anteriormente está disponible en la página [Inserción segura de los Centros de notificaciones de Azure] .

## <a name="operations"></a>Operaciones
### <a name="1----what-is-the-disaster-recovery-dr-story"></a>1.    ¿Qué es el historial de recuperación ante desastres (DR)?
En nuestro extremo se incluye cobertura de recuperación ante desastres de metadatos (nombre del Centro de notificaciones, cadena de conexión, y otra información crítica). Cuando se desencadena un escenario de recuperación ante desastres, los datos de los registros son el **único segmento** de la infraestructura de los Centros de notificaciones que se perderá. Tiene que implementar una solución para volver a rellenar estos datos en el nuevo centro posterior recuperación.

* *Paso 1* : cree un Centro de notificaciones secundario en otro centro de datos. Puede crearlo sobre la marcha en el momento del evento de recuperación ante desastres, o bien puede crear uno desde cero. No hay mucha diferencia entre una y otra opción porque el aprovisionamiento del Centro de notificaciones es un proceso relativamente rápido de unos pocos segundos. Si tiene uno desde el principio, le protegerá contra la posibilidad de que el evento de recuperación ante desastres afecte a sus funciones de administración, por lo que esta opción es muy recomendable.
* *Paso 2*: hidrate el Centro de notificaciones secundario con los registros del Centro de notificaciones principal. No se recomienda intentar mantener los registros en ambos centros, ni intentar mantenerlos sincronizados a medida que llegan los registros. Esto normalmente no funciona bien debido a la tendencia inherente de los registros a caducar en el extremo del PNS. Los centros de notificaciones se limpian cuando recibimos comentarios del PNS de que los registros han caducado o no son válidos.  

Se recomienda usar un back-end de aplicación que:

* Mantiene un conjunto de registros determinado en su extremo para que pueda realizar una inserción masiva en el Centro de notificaciones secundario en caso de recuperación ante desastres

**O bien**

* obtenga un volcado normal de registros desde el centro principal como una copia de seguridad y, a continuación, realice una inserción masiva en el centro de notificaciones secundario.

> [!NOTE]
> La funcionalidad de exportación/importación de registros disponible en el nivel Estándar se describe en el documento [Procedimiento: cómo exportar y modificar registros en bloque] .
> 
> 

Si no tiene un back-end, cuando se inicia la aplicación en cualquiera de los dispositivos de destino, estos realizarán un nuevo registro en el Centro de notificaciones secundario y, finalmente, el Centro de notificaciones secundario tendrá todos los dispositivos activos registrados.

La desventaja es que habrá un período de tiempo durante el que los dispositivos en los que no se han abierto las aplicaciones, no recibirán notificaciones.

### <a name="2----is-there-any-audit-log-capability"></a>2.    ¿Hay alguna funcionalidad de registro de auditoría?
Todas las operaciones de administración de los centros de notificaciones van a los registros de operaciones, que se exponen en el [Portal de Azure clásico].

## <a name="monitoring--troubleshooting"></a>Supervisión y solución de problemas
### <a name="1----what-troubleshooting-capabilities-are-available"></a>1.    ¿Qué funcionalidades de solución de problemas hay disponibles?
Los Centros de notificaciones de Azure proporcionan varias características para la solución de problemas comunes, especialmente en el escenario muy común que aparece en relación a las notificaciones quitadas. Vea los detalles en este artículo [Centros de notificaciones de Azure: pautas de diagnóstico] .

### <a name="2----what-telemetry-features-are-available"></a>2.    ¿Qué características de telemetría hay disponibles?
Los Centros de notificaciones Azure permiten ver los datos de telemetría en el [Portal de Azure clásico]. Encontrar detalles de las métricas disponibles en la página [Centros de notificaciones: Metrics] .

> [!NOTE]
> El éxito de las notificaciones solo significan que las notificaciones push se han entregado al servicio de notificaciones push externo (por ejemplo, APNS de Apple, GCM para Google, etc.). Es el PNS el que entrega la notificación al dispositivos de destino. Normalmente, el PNS no muestra las métricas de entrega a terceros.  
> 
> 

También proporciona la funcionalidad de exportar los datos de telemetría mediante programación (en el nivel **Estándar** ). Consulte [el ejemplo de métricas de Centros de notificaciones] para más información.

[Portal de Azure clásico]: https://manage.windowsazure.com
[Precios de Bases de datos centrales de notificaciones]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[Caso práctico: Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[(caso práctico: Skanska)]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[(caso práctico: Seattle Times)]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[(caso práctico: Mural.ly)]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[(caso práctico: 7Digital)]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[Cómo utilizar la interfaz REST de los Centros de notificación]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH - Getting Started Tutorials]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Introducción a Centros de notificaciones para aplicaciones Chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Registro desde el back-end de la aplicación]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Administración de registro]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Seguridad]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Inserción segura de los Centros de notificaciones de Azure]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Centros de notificaciones de Azure: pautas de diagnóstico]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Centros de notificaciones: Metrics]: https://msdn.microsoft.com/library/dn458822.aspx
[el ejemplo de métricas de Centros de notificaciones]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[Procedimiento: cómo exportar y modificar registros en bloque]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile Apps]: https://azure.microsoft.com/services/app-service/mobile/
[Precios de servicio de aplicaciones]: https://azure.microsoft.com/pricing/details/app-service/

