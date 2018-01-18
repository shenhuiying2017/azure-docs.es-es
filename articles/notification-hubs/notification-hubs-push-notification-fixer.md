---
title: "Diagnóstico de notificaciones eliminadas de Azure Notification Hubs"
description: Aprenda a diagnosticar problemas comunes con las notificaciones eliminadas de Azure Notification Hubs.
services: notification-hubs
documentationcenter: Mobile
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 3925208fe56bcd9513ec4c0f21aa1e2dd8fbf9c5
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="diagnose-dropped-notifications-in-notification-hubs"></a>Diagnóstico de notificaciones eliminadas en Notification Hubs

Una de las preguntas más comunes de los clientes de Azure Notification Hubs es cómo solucionar problemas cuando las notificaciones que se envían desde una aplicación no aparecen en los dispositivos del cliente. Quieren saber dónde y por qué se eliminaron las notificaciones y cómo solucionar el problema. En este artículo se identifica por qué las notificaciones pueden ser eliminadas o no recibidas por los dispositivos. Aprenda a analizar y determinar la causa principal. 

Es fundamental entender primero cómo Notification Hubs inserta notificaciones en un dispositivo.

![Arquitectura de Notification Hubs][0]

En un flujo de notificaciones de envío típico, el mensaje se envía desde el *back-end de la aplicación* a Notification Hubs. Notification Hubs realiza algún procesamiento en todos los registros. El procesamiento tiene en cuenta las etiquetas y expresiones de etiquetas configuradas para determinar los "destinos". Los destinos son todos los registros que necesitan recibir la notificación push. Estos registros se pueden distribuir en cualquiera de nuestras plataformas compatibles o en todas ellas: iOS, Google, Windows, Windows Phone, Kindle y Baidu para Android de China.

Con los destinos establecidos, Notification Hubs inserta las notificaciones en el *servicio de notificaciones push* para la plataforma del dispositivo. Algunos ejemplos son Apple Push Notification Service (APN) de Apple y Firebase Cloud Messaging (FCM) de Google. Notification Hubs lleva a las notificaciones a dividirse en varios lotes de registros. Notification Hubs se autentica con el servicio de notificaciones push respectivo basado en las credenciales que se establecieron en Azure Portal, en **Configuración del centro de notificaciones**. Luego, el servicio de notificaciones de inserción reenvía las notificaciones a los respectivos *dispositivos cliente*. 

Tenga en cuenta que el tramo final de la entrega de notificaciones tiene lugar entre el servicio de notificaciones push de la plataforma y el dispositivo. Cualquiera de los cuatro componentes principales del proceso de notificación push (cliente, back end de la aplicación, Notification Hubs y servicio de notificaciones push de la plataforma) puede provocar que las notificaciones se eliminen. Para más información sobre la arquitectura de Notification Hubs, consulte [Introducción a Notification Hubs].

Durante la fase inicial de prueba o de ensayo puede ocurrir que no se entreguen las notificaciones. Las notificaciones eliminadas en esta fase podrían indicar un problema de configuración. Si se produce un error en la entrega de notificaciones durante producción, es posible que algunas o todas las notificaciones se eliminen. En este caso, se indica una aplicación más profunda o un problema de patrón de mensajería. 

La siguiente sección examina los escenarios en los que las notificaciones podrían resultar eliminadas, desde los más comunes hasta los menos frecuentes.

## <a name="notification-hubs-misconfiguration"></a>Configuración incorrecta de Notification Hubs
Para poder enviar notificaciones correctamente al servicio de notificaciones de inserción, Notification Hubs debe autenticarse a sí mismo en el contexto de la aplicación del desarrollador. Para ello, el desarrollador crea una cuenta de desarrollador con la plataforma respectiva (Google, Apple, Windows, etc.). Después, el desarrollador registra su aplicación en la plataforma donde obtiene las credenciales. 

Debe agregar las credenciales de plataforma en Azure Portal. Si ninguna notificación está llegando al dispositivo, el primer paso debe ser asegurarse de que las credenciales correctas estén configuradas en Notification Hubs. Las credenciales deben coincidir con la aplicación creada en una cuenta de desarrollador específica de plataforma. 

Para obtener instrucciones paso a paso para completar este proceso, consulte [Introducción a Azure Notification Hubs].

Estos son algunos errores comunes de configuración para comprobar:

* **General**
   
    * Asegúrese de que el nombre de su centro de notificaciones (escrito sin errores) sea el mismo en cada una de estas ubicaciones:

        * Donde se registra en el cliente.
        * Donde envía notificaciones desde el back-end.
        * Donde ha configurado las credenciales de servicios de notificaciones de inserción.
    
    * Asegúrese de que utiliza las cadenas de configuración de firmas de acceso compartido correctas en el cliente y en el back end de la aplicación. Por lo general, debe usar **DefaultListenSharedAccessSignature** en el cliente y **DefaultFullSharedAccessSignature** en el back-end de la aplicación (que concede permiso para enviar notificaciones a Notification Hubs).

* **Configuración de Apple Push Notification Service**
   
    Debe mantener dos centros diferentes: uno para producción y otro para prueba. Esto significa que debe cargar el certificado que va a utilizar en un entorno de espacio aislado en un centro distinto del certificado y del centro que va a utilizar en producción. No intente cargar diferentes tipos de certificados en el mismo centro, ya que podría provocar errores de notificación. 
    
    Si carga involuntariamente diferentes tipos de certificados en el mismo centro, le recomendamos que elimine el centro y que vuelva a empezar con uno nuevo. Si, por algún motivo, no puede eliminar el centro, elimine al menos todos los registros existentes del centro. 

* **Configuración de FCM** 
   
    1. Asegúrese de que la *clave de servidor* que obtuvo de Firebase coincide con la clave de servidor registrada en Azure Portal.
   
    ![Clave de servidor de Firebase][3]
   
    2. Asegúrese de que ha configurado el **identificador de proyecto** en el cliente. Puede obtener el valor del **identificador de proyecto** en el panel de Firebase.
   
    ![Identificador del proyecto de Firebase][1]

## <a name="application-issues"></a>Problemas de la aplicación
* **Etiquetas y expresiones de etiqueta**

    Si usa etiquetas o expresiones de etiqueta para segmentar su audiencia, es posible que cuando envía la notificación no se encuentre ningún destino en función de las etiquetas o expresiones de etiqueta que especifica en su llamada de envío. 
    
    Revise los registros para asegurarse de que haya etiquetas coincidentes cuando envíe una notificación. Después, compruebe la recepción de la notificación solo de los clientes que tienen esos registros. 
    
    Por ejemplo, si todos los registros con Notification Hubs se hicieron utilizando la etiqueta "Politics" y envía una notificación con la etiqueta "Sports", la notificación no se envía a ningún dispositivo. Un caso complejo podría incluir expresiones de etiqueta donde solo se registró con "Etiqueta A" O "Etiqueta B" pero, cuando envía notificaciones, los destinatarios son "Etiqueta A && Etiqueta B". En la sección de sugerencias de autodiagnóstico, más adelante en el artículo, le mostramos cómo revisar los registros y etiquetas. 

* **Problemas de plantillas**

    Si utiliza plantillas, asegúrese de seguir las directrices descritas en [Plantillas]. 

* **Registros no válidos**

    Si el centro de notificaciones se ha configurado correctamente y si alguna etiqueta o expresión de etiqueta se ha utilizado correctamente, se encuentran destinos válidos. Las notificaciones se enviarán a estos destinos. Después, Notification Hubs desactiva varios lotes de procesamiento en paralelo. Cada lote envía mensajes a un conjunto de registros. 

    > [!NOTE]
    > Puesto que el procesamiento se realiza en paralelo, no se garantiza el orden en el que se entregan las notificaciones. 

    Notification Hubs está optimizado para un modelo de entrega de mensajes "una vez como máximo". Intentamos una desduplicación para que ninguna notificación se entregue más de una vez a un dispositivo. Para asegurar esto, comprobamos los registros y nos aseguramos de que solo se envía un mensaje por identificador de dispositivo antes de enviar el mensaje a servicio de notificaciones push. 

    Como cada lote se envía al servicio de notificaciones push, que a su vez acepta y valida los registros, es posible que el servicio de notificaciones push detecte un error con uno o más de los registros de un lote. En este caso, el servicio de notificaciones push devuelve un error a Notification Hubs y el proceso se detiene. El servicio de notificaciones push elimina ese lote por completo. Esto ocurre así con APNS, que usa un protocolo de transmisión TCP. 

    Estamos optimizados para una entrega inmediata. Pero, en este caso, el registro de errores se quita de la base de datos. A continuación, volvemos a intentar la entrega de notificaciones para el resto de los dispositivos de ese lote.

    Para obtener más información del error del intento de entrega con error en un registro, pude usar las API de REST de Notification Hubs: [Telemetría por mensaje: Obtención de telemetría de mensaje de notificación](https://msdn.microsoft.com/library/azure/mt608135.aspx) y [Comentarios de PNS](https://msdn.microsoft.com/library/azure/mt705560.aspx). Para ver un ejemplo de código, consulte el [ejemplo de REST de envío](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample).

## <a name="push-notification-service-issues"></a>Problemas del servicio de notificaciones push
Una vez recibido el mensaje de notificación por parte del servicio de notificaciones push de la plataforma, es responsabilidad de dicho servicio entregar la notificación al dispositivo. En este punto, Notification Hubs queda aquí al margen y no tienen control sobre si la notificación se va a entregar al dispositivo, o cuándo se hará. 

Como los servicios de notificaciones de plataforma son bastante sólidos, las notificaciones tienden a llegar a los dispositivos en unos segundos desde el servicio de notificaciones push. Si se está limitando servicio de notificaciones push, Notification Hubs aplica una estrategia de retroceso exponencial. Si el servicio de notificaciones push sigue inaccesible durante 30 minutos, tenemos una directiva establecida para hacer que esos mensajes expiren y se eliminen permanentemente. 

Si un servicio de notificaciones push intenta entregar una notificación pero el dispositivo está sin conexión, el servicio de notificaciones push almacena la notificación durante un período de tiempo limitado. La notificación se entrega al dispositivo cuando este está disponible. 

Para cada aplicación, se almacena solo una notificación reciente. Si se envían varias notificaciones mientras el dispositivo está sin conexión, cada nueva notificación provoca que se descarte la anterior. Mantener solo la notificación más reciente se conoce como *fusionar notificaciones* en Apple Push Notification Service y *contraer* en GCM (que usa una clave de contracción). Si el dispositivo permanece sin conexión durante un período de tiempo prolongado, todas las notificaciones que se estaban almacenando para el dispositivo se descartan. Para más información, consulte [APNs overview] (Introducción a Apple Push Notification Service) y [Acerca de los mensajes de FCM].

Con Azure Notification Hubs, puede pasar una clave de fusión a través de un encabezado HTTP mediante el uso de SendNotification API genérica. Por ejemplo, para el .NET SDK, debería usar **SendNotificationAsync**. SendNotification API también toma los encabezados HTTP que se pasan tal cual al servicio de notificaciones push respectivo. 

## <a name="self-diagnosis-tips"></a>Sugerencias de autodiagnóstico
Estas son rutas de acceso para diagnosticar la causa principal de las notificaciones eliminadas en Notification Hubs:

### <a name="verify-credentials"></a>Comprobar las credenciales
* **Portal para desarrolladores del servicio de notificaciones push**
   
    Compruebe las credenciales en el portal para desarrolladores de los servicios de notificaciones push respectivos (APN, FCM, servicio de notificaciones de Windows, etc.). Para más información, consulte [Introducción a Azure Notification Hubs].

* **portal de Azure**
   
    Para revisar y comparar las credenciales con las que obtuvo en el portal para desarrolladores de los servicios de notificaciones push, en Azure Portal, vaya a la pestaña **Directivas de acceso**. 
   
    ![Directivas de acceso de Azure Portal][4]

### <a name="verify-registrations"></a>Verificar los registros
* **Visual Studio**
   
    Si utiliza Visual Studio para desarrollar, puede conectarse a Azure mediante el Explorador de servidores para ver y administrar muchos servicios de Azure, incluido Notification Hubs. Esto es especialmente útil para su entorno de desarrollo y prueba. 
   
    ![Explorador de servidores de Visual Studio][9]
   
    Puede ver y administrar todos los registros de su centro, clasificados por plataforma, registro nativo o de plantilla, etiqueta, identificador del servicio de notificaciones push, identificador de registro y fecha de expiración. También puede editar un registro en esta página. Esto es especialmente útil para editar etiquetas. 
   
    ![Registros de dispositivos de Visual Studio][8]
   
   > [!NOTE]
   > Utilice Visual Studio para editar registros solo durante las pruebas o el desarrollo, y con un número limitado de registros. Si necesita modificar sus registros en bloque, considere la posibilidad de usar la función para exportar o importar registros que se describe en [Procedimiento: cómo exportar y modificar registros en bloque](https://msdn.microsoft.com/library/dn790624.aspx).
   > 
   > 
* **Explorador de Service Bus**
   
    Muchos clientes usan el [Explorador de Service Bus] para consultar y administrar su centro de notificaciones. El Explorador de Service Bus es un proyecto de código abierto. Para ver ejemplos, consulte el [código del Explorador de Service Bus].

### <a name="verify-message-notifications"></a>Comprobar las notificaciones de mensajes
* **portal de Azure**
   
    Para enviar una notificación de prueba a los clientes sin que el back-end del servicio esté en funcionamiento, en **SOPORTE TÉCNICO Y SOLUCIÓN DE PROBLEMAS**, seleccione **Envío de prueba**. 
   
    ![Funcionalidad Envío de prueba en Azure][7]
* **Visual Studio**
   
    También puede enviar notificaciones de prueba desde Visual Studio.
   
    ![Funcionalidad Envío de prueba en Visual Studio][10]
   
    Para más información acerca del uso de Notification Hubs con el Explorador de servidores de Visual Studio, consulte estos artículos: 
   
   * [Cómo ver los registros de dispositivo de los centros de notificaciones]
   * [Deep dive: Visual Studio 2013 Update 2 RC and Azure SDK 2.3] (Análisis a fondo: Visual Studio 2013 Update 2 RC y Azure SDK 2.3)
   * [Announcing release of Visual Studio 2013 Update 3 and Azure SDK 2.4] (Anuncio del lanzamiento de Visual Studio 2013 Update 3 y Azure SDK 2.4)

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Depuración de las notificaciones incorrectas y revisión del resultado de la notificación
**Propiedad EnableTestSend**

Cuando se envía una notificación mediante Notification Hubs, inicialmente, la notificación se pone en cola para su procesamiento en Notification Hubs. Notification Hubs determina los destinos correctos y después envía la notificación al servicio de notificaciones push. Si usa la API de REST o cualquiera de los SDK de cliente, la devolución correcta de su llamada de envío solo implica que el mensaje se ha puesto en cola correctamente en Notification Hubs. No tiene ninguna información de lo que ocurrió cuando Notification Hubs envió finalmente el mensaje al servicio de notificaciones push. 

Si la notificación no llega al dispositivo cliente, es posible que se haya producido un error cuando Notification Hubs ha intentado enviar el mensaje al servicio de notificaciones push. Por ejemplo, el tamaño de la carga podría exceder del máximo permitido por el servicio de notificaciones push, o las credenciales configuradas en Notification Hubs podrían no ser válidas. 

Para obtener información sobre los errores del servicio de notificaciones push, puede utilizar la propiedad [EnableTestSend]. Esta propiedad se habilita automáticamente cuando envía mensajes de prueba desde el portal o el cliente de Visual Studio. Puede utilizar esta propiedad para ver la información de depuración detallada. También puede utilizar la propiedad a través de las API. Actualmente, se puede utilizar en .NET SDK. Finalmente, se agregará a todos los SDK de cliente. 

Para usar la propiedad **EnableTestSend** con la llamada REST, anexe un parámetro de cadena de consulta llamado *test* al final de su llamada de envío. Por ejemplo:  

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

**Ejemplo (SDK para .NET)**

Este es un ejemplo de uso de .NET SDK para enviar una notificación emergente nativa (notificación del sistema):

```csharp
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
```

Al final de la ejecución, **result.State** indica simplemente **En cola**. Los resultados no proporcionan ninguna información sobre qué ha ocurrido con la notificación push. 

A continuación, puede usar la propiedad booleana **EnableTestSend**. Use la propiedad **EnableTestSend** mientras se inicializa **NotificationHubClient** para obtener un estado detallado sobre los errores del servicio de notificaciones push que se producen cuando se envía la notificación. La llamada de envío tarda un tiempo adicional en devolverse porque solo se devuelve después de que Notification Hubs haya entregado la notificación al servicio de notificaciones push para determinar el resultado. 

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

**Salida de ejemplo**

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong

Este mensaje indica que o bien credenciales no válidas se han configurado en Notification Hubs, o hay un problema con los registros en el centro. Se recomienda que elimine este registro y deje que el cliente vuelva a crear el registro antes de enviar el mensaje. 

> [!NOTE]
> El uso de la propiedad **EnableTestSend** está muy limitada. Utilice esta opción únicamente en un entorno de desarrollo y pruebas y con un conjunto limitado de registros. Nosotros enviamos notificaciones de depuración únicamente a 10 dispositivos. También contamos con un límite en el procesamiento de envíos de depuración de 10 por minuto. 
> 
> 

### <a name="review-telemetry"></a>Revisar la telemetría
* **Uso de Azure Portal**
   
    En el portal, puede obtener una rápida introducción de toda la actividad que tiene lugar en el centro de notificaciones. 
   
    1. En la pestaña **Información general**, puede ver una vista agregada de registros, notificaciones y errores por plataforma. 
   
        ![Panel de introducción a Notification Hubs][5]
   
    2. En la pestaña **Supervisión**, puede agregar muchas otras métricas específicas de la plataforma para obtener una visión más profunda. Puede ver específicamente cualquier error relacionado con el servicio de notificaciones push que se devuelve cuando Notification Hubs intenta enviar la notificación al servicio de notificaciones push. 
   
        ![Registro de actividad de Azure Portal][6]
   
    3. Comience por revisar **los mensajes entrantes**, **las operaciones de registro** y las **notificaciones correctas**. Después, vaya a la pestaña por plataforma para revisar los errores específicos del servicio de notificaciones push. 
   
    4. Si la configuración de autenticación para el centro de notificaciones no es correcta, se muestra el mensaje **Error de autenticación PNS**. Es un buen indicio de que se deben comprobar las credenciales del servicio de notificaciones push. 

* **Acceso mediante programación**

    Para más información sobre el acceso mediante programación, consulte estos artículos: 

    * [Acceso de telemetría mediante programación]  
    * [Ejemplo de acceso de telemetría mediante API] 

    > [!NOTE]
    > Varias características relacionadas con la telemetría, como la exportación e importación de registros y el acceso a la telemetría a través de API, solo están disponibles en el nivel de servicio Estándar. Si intenta utilizar estas características desde el nivel de servicio Gratis o Básico, recibirá un mensaje de excepción si utiliza el SDK y un error HTTP 403 (Prohibido) si utiliza las funciones directamente desde las API de REST. 
    >
    >Para usar las características relacionadas con la telemetría, asegúrese primero en Azure Portal que está usando el nivel de servicio Estándar.  
> 
> 

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/FCMConfigure.png
[3]: ./media/notification-hubs-diagnosing/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Introducción a Notification Hubs]: notification-hubs-push-notification-overview.md
[Introducción a Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Plantillas]: https://msdn.microsoft.com/library/dn530748.aspx 
[APNs overview]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html (Introducción a Apple Push Notification Service)
[Acerca de los mensajes de FCM]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: http://msdn.microsoft.com/library/dn790624.aspx
[Explorador de Service Bus]: https://msdn.microsoft.com/library/dn530751.aspx#sb_explorer
[código del Explorador de Service Bus]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Cómo ver los registros de dispositivo de los centros de notificaciones]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[Deep dive: Visual Studio 2013 Update 2 RC and Azure SDK 2.3]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs  (Análisis a fondo: Visual Studio 2013 Update 2 RC y Azure SDK 2.3)
[Announcing release of Visual Studio 2013 Update 3 and Azure SDK 2.4]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/  (Anuncio del lanzamiento de Visual Studio 2013 Update 3 y Azure SDK 2.4)
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Acceso de telemetría mediante programación]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Ejemplo de acceso de telemetría mediante API]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

