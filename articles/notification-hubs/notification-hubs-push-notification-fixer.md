---
title: "Centros de notificaciones de Azure: pautas de diagnóstico"
description: "Instrucciones sobre cómo diagnosticar problemas comunes con los Centros de notificaciones de Azure."
services: notification-hubs
documentationcenter: Mobile
author: ysxu
manager: erikre
editor: 
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 32e3a2e6f840afd865375a622cfae0d33ba65090
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="azure-notification-hubs---diagnosis-guidelines"></a>Centros de notificaciones de Azure: pautas de diagnóstico
## <a name="overview"></a>Información general
Una de las preguntas más comunes que escucharemos de los clientes de los Centros de notificaciones de Azure es que no comprenden por qué las notificaciones enviadas desde el back-end de su aplicación no aparecen en el dispositivo cliente, dónde y por qué se han perdido las notificaciones y cómo solucionar este problema. En este artículo repasaremos los diversos motivos de por qué las notificaciones podrían haberse perdido o no terminar en los dispositivos. También examinaremos formas en que puede analizar y descubrir la causa raíz. 

En primer lugar, es vital comprender el modo en que los Centros de notificaciones de Azure insertan las notificaciones en los dispositivos.
![][0]

En un flujo típico de envío de notificaciones, el mensaje se envía desde el **back-end de la aplicación** al **Centro de notificaciones de Azure (NH)**, el cual, a su vez, lleva a cabo algún tipo de procesamiento en todos los registros teniendo en cuenta las etiquetas y expresiones de etiqueta para determinar los "destinos", es decir, todos los registros que deben recibir la notificación de inserción. Estos registros se pueden distribuir en cualquiera de nuestras plataformas compatibles o en todas ellas: iOS, Google, Windows, Windows Phone, Kindle y Baidu para Android de China. Una vez que se establecen los destinos, el Centro de notificaciones inserta las notificaciones, divididas entre varios lotes de registros, en el **servicio de notificaciones de inserción (PNS)** específico de la plataforma del dispositivo, por ejemplo, APNS para Apple, GCM para Google, etc. NH se autentica con el PNS respectivo según las credenciales definidas en el Portal de Azure clásico en la página Configurar centro de notificaciones. Luego, el PNS reenvía las notificaciones a los respectivos **dispositivos cliente**. Esta es la manera recomendada por la plataforma de entregar las notificaciones de inserción y, tenga en cuenta, que el tramo final de la entrega de notificaciones tiene lugar entre el PNS de la plataforma y el dispositivo. Por lo tanto, hay cuatro componentes principales: *cliente*, *back-end de la aplicación*, *Azure Notification Hubs (NH)* y *servicios de notificación de inserción (PNS)*, y cualquiera de ellos puede provocar que se eliminen las notificaciones. Se puede encontrar más información sobre esta arquitectura en [Información general de Notification Hubs].

La no entrega de las notificaciones puede tener lugar durante la fase inicial de prueba/ensayo, lo que podría indicar un problema de configuración, o se puede producir en producción, cuando todas o algunas notificaciones se pierden, lo que puede indicar un problema más serio con la aplicación o con el patrón de mensajería. En la siguiente sección, examinaremos diversos escenarios de pérdida de notificaciones, desde el más común al más raro. Algunos los encontrará obvios y otros no tanto. 

## <a name="azure-notifications-hub-mis-configuration"></a>Error de configuración del Centro de notificaciones de Azure
Los Centros de notificaciones de Azure deben autenticarse a sí mismos en el contexto de la aplicación del desarrollador para poder enviar notificaciones correctamente a los PNS respectivos. Esto es posible gracias al desarrollador, que crea una cuenta de desarrollador con la plataforma respectiva (Google, Apple, Windows, etc.) y luego registra su aplicación donde obtiene las credenciales que es necesario configurar en el portal en la sección de configuración de los Centros de notificaciones. Si no se logra entregar ninguna notificación, el primer paso debe ser asegurarse de que se han configurado las credenciales correctas en el Centro de notificaciones y que coinciden con las de la aplicación creada con su cuenta de desarrollador específica de la plataforma. Encontrará útiles nuestros [tutoriales introductorios] para recorrer este proceso paso a paso. A continuación se indican algunos errores de configuración comunes:

1. **General**
   
    a) Asegúrese de que el nombre de su centro de notificaciones (escrito sin errores) sea el mismo:
   
   * Es donde se está registrando desde el cliente. 
   * Es donde está enviando las notificaciones desde el back-end.  
   * Es donde ha configurado las credenciales del PNS y 
   * Son las credenciales de SAS de dicho centro las que ha configurado en el cliente y en el back-end. 
     
     b) Asegúrese de que usa las cadenas de configuración de SAS correctas en el cliente y en el back-end de la aplicación. Como norma general, debe estar usando **DefaultListenSharedAccessSignature** en el cliente y **DefaultFullSharedAccessSignature** en el back-end de la aplicación (que concede permiso para poder enviar una notificación al Centro de notificaciones).
2. **Configuración del Servicio de notificaciones push de Apple (APNS)**
   
    Debe mantener dos centros diferentes: uno para producción y otro para fines de prueba. Esto supone cargar el certificado que va a usar en el entorno de espacio aislado en un centro y el certificado que va a usar en producción en otro centro diferente. No intente cargar diferentes tipos de certificados en el mismo centro ya que se pueden producir errores de notificación después. Si se encuentra en una situación en la que ha cargado por accidente diferentes tipos de certificados en el mismo centro, se recomienda eliminar el centro y empezar de nuevo. Si, por algún motivo, no puede eliminar el centro, elimine al menos todos los registros existentes del centro. 
3. **Configuración del Servicio de mensajería en la nube de Google (GCM)** 
   
    a) Asegúrese de que ha habilitado "Google Cloud Messaging for Android" (Servicio de mensajería en la nube de Google para Android) en su proyecto de nube. 
   
    ![][2]
   
    b) Asegúrese de que crea una "Server Key"(Clave de servidor) mientras obtiene las credenciales que usará el centro de notificaciones para autenticarse con GCM. 
   
    ![][3]
   
    c) Asegúrese de que ha configurado el "Project ID" (Id. de proyecto) en el cliente, una entidad totalmente numérica que puede obtener en el panel:
   
    ![][1]

## <a name="application-issues"></a>Problemas de la aplicación
1) **Etiquetas/expresiones de etiqueta**

Si está usando etiquetas o expresiones de etiqueta para segmentar su audiencia, siempre es posible que cuando envía la notificación no se encuentre ningún destino en función de las etiquetas o expresiones de etiqueta que especifica en su llamada de envío. Lo mejor es revisar sus registros para asegurarse de que haya etiquetas que coincidan con el momento en envía su notificación y luego comprobar la recepción de la notificación solo de los clientes con esos registros. Por ejemplo, Si todos los registros en el Centro de notificaciones se realizaron con la etiqueta "Política" y se envía una notificación con la etiqueta "Deportes", no se enviará a ningún dispositivo. Un caso complejo podría incluir expresiones de etiqueta donde solo se registró con "Etiqueta A" O "Etiqueta B" pero cuando envía notificaciones, los destinatarios son "Etiqueta A && Etiqueta B". En la sección de consejos de autodiagnóstico que se describe a continuación, existen maneras de revisar los registros junto con las etiquetas que tienen. 

2) **Problemas de plantillas**

Si va a usar plantillas, asegúrese entonces de seguir las instrucciones que se describen en [Instrucciones para plantillas]. 

3) **Registros no válidos**

Suponiendo que el Centro de notificaciones se configuró correctamente y que las etiquetas o expresiones de etiqueta se usaron correctamente, de modo que se encontraron destinos válidos a los que enviar las notificaciones, NH activa varios lotes de procesamiento en paralelo, y cada uno de ellos envía mensajes a un conjunto de registros. 

> [!NOTE]
> Dado que realizamos el procesamiento en paralelo, no garantizamos el orden en que se entregarán las notificaciones. 
> 
> 

Ahora el Centro de notificaciones de Azure está optimizado para un modelo de entrega de mensajes "una vez como máximo". Esto significa que intentamos una desduplicación para que ninguna notificación se entregue más de una vez a un dispositivo. Para comprobar esto, examinamos los registros y nos aseguramos de que solo se envía un mensaje por identificador de dispositivo antes de enviar realmente el mensaje al PNS. Como cada lote se envía al PNS, el cual, a su vez, acepta y valida los registros, puede que el PNS detecte un error con uno o varios de los registros de un lote, devuelva un error al Centro de notificaciones de Azure y detenga el procesamiento con la consiguiente pérdida de ese lote completamente. Esto ocurre así con APNS, que usa un protocolo de transmisión TCP. Aunque ofrecemos optimización para la entrega como máximo una vez, en este caso quitamos el registro defectuoso de nuestra base de datos y, a continuación, volvemos a intentar la entrega de la notificación para el resto de los dispositivos en ese lote.

Puede obtener información del error del intento de entrega con error en un registro con las API de REST de Azure Notification Hubs: [Telemetría por mensaje: Obtención de telemetría de mensaje de notificación](https://msdn.microsoft.com/library/azure/mt608135.aspx) y [Comentarios de PNS](https://msdn.microsoft.com/library/azure/mt705560.aspx). Consulte [SendRESTExample](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample) para el código de ejemplo.

## <a name="pns-issues"></a>Problemas del PNS
Una vez que el PNS respectivo ha recibido el mensaje de notificación, es responsabilidad suya entregar la notificación al dispositivo. Los Centros de notificaciones de Azure quedan aquí al margen y no tienen control sobre cuándo o si la notificación se va a entregar al dispositivo. Dado que los servicios de notificaciones de plataforma son bastante robustos, las notificaciones tienden a llegar a los dispositivos en unos segundos desde el PNS. Sin embargo, si el PNS limita las peticiones, los Centros de notificaciones de Azure aplican una estrategia de interrupción exponencial y, si sigue siendo imposible comunicarse con el PNS durante 30 m, contamos con una directiva que expira y elimina esos mensajes permanentemente. 

Si un PNS intenta entregar una notificación pero el dispositivo está sin conexión, el PNS almacena la notificación por un período de tiempo limitado, y la entrega al dispositivo cuando vuelve a estar disponible. Solo se almacena una notificación reciente de una aplicación en particular. Si se envían varias notificaciones mientras el dispositivo está sin conexión, cada nueva notificación provoca que se descarte la anterior. Este comportamiento de mantener solo la notificación más reciente se conoce como fusionar notificaciones en APNS y contraer en GCM (que usa una clave de contracción). Si el dispositivo permanece sin conexión durante un período de tiempo prolongado, todas las notificaciones que se estaban almacenando para él se descartan. Origen: [Instrucciones para APNS] & [Instrucciones para GCM]

Con los Centros de notificaciones de Azure, puede pasar una clave de fusión a través de un encabezado HTTP mediante la `SendNotification` API genérica (por ejemplo, para el SDK de .NET – `SendNotificationAsync`) que también toma los encabezados HTTP que se pasan como están al PNS respectivo. 

## <a name="self-diagnose-tips"></a>Sugerencias de autodiagnóstico
A continuación, examinaremos las diversas formas de diagnosticar y encontrar la causa raíz de los problemas con los Centros de notificaciones:

### <a name="verify-credentials"></a>Comprobar las credenciales
1. **Portal para desarrolladores de PNS**
   
    Compruébelas en el portal para desarrolladores del PNS respectivo (APNS, GCM, WNS, etc.) mediante [tutoriales introductorios].
2. **Portal de Azure clásico**
   
    Vaya a la pestaña Configurar para revisar y comprobar que las credenciales coinciden con las obtenidas en el portal para desarrolladores del PNS. 
   
    ![][4]

### <a name="verify-registrations"></a>Verificar los registros
1. **Visual Studio**
   
    Si utiliza Visual Studio para desarrollar, puede conectarse a Microsoft Azure y ver y administrar un montón de servicios de Azure, incluidos los Centros de notificaciones, desde el "Explorador de servidores". Esto es especialmente útil para su entorno de desarrollo y prueba. 
   
    ![][9]
   
    Puede ver y administrar todos los registros de su centro que están perfectamente clasificados por plataforma, registro nativo o de plantilla, etiqueta, identificador de PNS, id. de registro y fecha de expiración. También puede editar un registro sobre la marcha, lo que resulta de utilidad si, por ejemplo, desea editar alguna etiqueta. 
   
    ![][8]
   
   > [!NOTE]
   > Las funciones de Visual Studio para editar registros se deben usar únicamente durante las fases de desarrollo y prueba con un número limitado de registros. Si surge la necesidad de corregir sus registros en masa, considere la posibilidad de usar la función para exportar o importar registros que se describe en [Exportación e importación de registros](https://msdn.microsoft.com/library/dn790624.aspx)
   > 
   > 
2. **Explorador de Bus de servicio**
   
    Muchos clientes usan el explorador de Bus de servicio que se describe en [Explorador de Bus de servicio] para ver y administrar sus centros de notificaciones. Se trata de un proyecto de código abierto disponible en code.microsoft.com, [Código del explorador de Bus de servicio]

### <a name="verify-message-notifications"></a>Comprobar las notificaciones de mensajes
1. **Portal de Azure clásico**
   
    Puede ir a la pestaña "Depurar" para enviar notificaciones de prueba a sus clientes sin necesidad de que ningún back-end de servicios esté funcionando. 
   
    ![][7]
2. **Visual Studio**
   
    También puede enviar notificaciones de prueba desde la comodidad de Visual Studio:
   
    ![][10]
   
    Puede leer más información sobre la funcionalidad de explorador para el Centro de notificaciones de Azure en Visual Studio aquí: 
   
   * [Introducción al Explorador de servidores de VS]
   * [Entrada del blog del Explorador de servidores de VS - 1]
   * [Entrada del blog del Explorador de servidores de VS - 2]

### <a name="debug-failed-notifications-review-notification-outcome"></a>Depurar las notificaciones incorrectas y revisar el resultado de la notificación
**Propiedad EnableTestSend**

Cuando envía una notificación a través de los Centros de notificaciones, inicialmente solo se pone en cola a la espera de que el Centro de notificaciones realice el procesamiento para calcular todos sus destinos y luego la envía finalmente al PNS. Esto significa que, cuando usa la API de REST o cualquiera de los SDK de cliente, la devolución correcta de su llamada de envío solo implica que el mensaje se ha puesto en cola correctamente en el Centro de notificaciones. No se proporciona información de lo que ha pasado cuando el Centro de notificaciones llegó a enviar finalmente el mensaje al PNS. Si su notificación no llega al dispositivo cliente, existe la posibilidad de que cuando el Centro de notificaciones intentara entregar el mensaje al PNS, hubiera un error, por ejemplo, que el tamaño de la carga excediera el máximo permitido por el PNS o que las credenciales configuradas en el Centro de notificaciones no fueran válidas, etc. Para formarse una idea sobre los errores del PNS, hemos introducido una propiedad llamada [característica EnableTestSend]. Esta propiedad se habilita automáticamente cuando envía mensajes de prueba desde el portal o el cliente de Visual Studio y, por tanto, le permite ver información de depuración detallada. Puede usar esta propiedad mediante API, tomando el ejemplo del SDK para .NET donde ahora está disponible, y con el tiempo se agregará a todos los SDK de cliente. Para usar esta propiedad con la llamada REST, solo tiene que anexar un parámetro querystring llamado "test" al final de su llamada de envío. 

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*Ejemplo (SDK para .NET)*

Imagine que utiliza el SDK para .NET para enviar una notificación del sistema nativa:

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);

`result.State` simplemente indica `Enqueued` al final de al ejecución sin informar de lo que ha pasado con la inserción. Ahora puede utilizar la propiedad booleana `EnableTestSend` mientras inicializa `NotificationHubClient` y puede obtener un estado detallado sobre los errores del PNS encontrados mientras se enviaba la notificación. Aquí la llamada de envío tarda más tiempo de lo normal en devolverse porque solo lo hace después de que el Centro de notificaciones ha entregado la notificación al PNS para determinar el resultado. 

    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }

*Salida de ejemplo*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong

Este mensaje indica que se han configurado credenciales no válidas en el centro de notificaciones o que existe un problema con los registros en el centro y la acción recomendada sería eliminar este registro y dejar que el cliente lo volviera a crear antes de enviar el mensaje. 

> [!NOTE]
> Tenga en cuenta que el uso de esta propiedad está muy limitado y, por tanto, solo se debe usar en entornos de desarrollo y prueba con un conjunto limitado de registros. Nosotros solo enviamos notificaciones de depuración a 10 dispositivos. También contamos con un límite en el procesamiento de envíos de depuración de 10 por minuto. 
> 
> 

### <a name="review-telemetry"></a>Revisar la telemetría
1. **Uso del Portal de Azure clásico**
   
    El portal le permite obtener una visión general rápida de toda la actividad que tiene lugar en el centro de notificaciones. 
   
    a) Desde la pestaña "Panel", puede tener una vista agregada de registros, notificaciones y errores por plataforma. 
   
    ![][5]
   
    b) También puede agregar muchas otras métricas específicas de la plataforma desde la pestaña "Supervisar" para así examinar más detenidamente cualquier error específico del PNS devuelto cuando el centro de notificaciones intenta enviar la notificación al PNS. 
   
    ![][6]
   
    c) Comience por revisar los **Mensajes entrantes**, las **Operaciones de registro** y las **Notificaciones correctas** y luego vaya a la pestaña Por plataforma para revisar los errores específicos del PNS. 
   
    d) Si no tiene configurado correctamente el centro de notificaciones con la configuración de autenticación, verá el error de autenticación del PNS. Es un buen indicio de que se deben comprobar las credenciales del PNS. 

2) **Acceso mediante programación**

Más detalles aquí: 

* [Acceso de telemetría mediante programación]
* [Ejemplo de acceso de telemetría mediante API] 

> [!NOTE]
> Varias características relacionadas con la telemetría, como **exportación e importación de registros**, **acceso a telemetría a través de API**, etc., están solamente disponibles en el nivel Estándar. Si intenta usar estas características en el nivel Gratuito o Básico, recibirá un mensaje de excepción al respecto mientras usa el SDK y un error HTTP 403 (Prohibido) cuando las usa directamente desde las API de REST. Asegúrese de que ha pasado al nivel Estándar a través del Portal de Azure clásico.  
> 
> 

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Información general de Notification Hubs]: notification-hubs-push-notification-overview.md
[tutoriales introductorios]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Instrucciones para plantillas]: https://msdn.microsoft.com/library/dn530748.aspx 
[Instrucciones para APNS]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4
[Instrucciones para GCM]: http://developer.android.com/google/gcm/adv.html
[Export/Import Registrations]: http://msdn.microsoft.com/library/dn790624.aspx
[Explorador de Bus de servicio]: http://msdn.microsoft.com/library/dn530751.aspx
[Código del explorador de Bus de servicio]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Introducción al Explorador de servidores de VS]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[Entrada del blog del Explorador de servidores de VS - 1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[Entrada del blog del Explorador de servidores de VS - 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[característica EnableTestSend]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx
[Acceso de telemetría mediante programación]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Ejemplo de acceso de telemetría mediante API]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

