<properties linkid="dev-net-service-bus-amqp-overview" urlDisplayName="Azure Notification Hubs" pageTitle="Azure Notification Hubs" metaKeywords="Azure push notifications, Azure notification hubs, Azure messaging" description="Learn how to use push notifications in Azure. Code samples written in C# using the .NET API." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="Azure Notification Hubs" authors="sethm" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

# Centros de notificaciones de Azure

La compatibilidad con las notificaciones de inserción en Azure le permite tener acceso a una infraestructura multiplataforma y de escalamiento horizontal fácil de usar, que simplifica considerablemente la implementación de notificaciones de inserción tanto en aplicaciones de consumidor, como en aplicaciones empresariales para plataformas móviles.

## <span class="short-header">¿Qué son las notificaciones de inserción?</span>¿Qué son las notificaciones de inserción?

Los smartphones y las tabletas cuentan con la posibilidad de "notificar" a los usuarios cuando se ha producido un evento. En las aplicaciones de la Tienda Windows, la notificación puede resultar en una *notificación del sistema*: aparece una ventana sin modo, con un sonido, para indicar una nueva notificación de inserción. En los dispositivos con iOS de Apple, la notificación de inserción interrumpe de manera similar con un cuadro de diálogo, que solicita al usuario que vea o cierre la notificación. Un clic en **Ver** abre la aplicación que está recibiendo el mensaje.

Las notificaciones de inserción ayudan a que los dispositivos móviles muestren información actualizada mientras ahorran energía. Las notificaciones de inserción son un componente esencial de las aplicaciones de consumidor, donde se utilizan para aumentar el uso y la interacción con la aplicación. Las notificaciones también son útiles para las empresas cuando la información actualizada aumenta la capacidad de respuesta de los empleados ante eventos empresariales.

Algunos ejemplos específicos de escenarios de interacción móvil son:

1.  Actualización de un icono en Windows 8 o Windows Phone con información financiera actual.
2.  Alerta con una notificación del sistema a un usuario cuando se le ha asignado algún elemento de trabajo en una aplicación empresarial basada en el flujo de trabajo.
3.  Visualización de un distintivo con el número de clientes potenciales actuales en una aplicación CRM (como Microsoft Dynamics CRM).

## <span class="short-header">Funcionamiento de las notificaciones de inserción</span>Funcionamiento de las notificaciones de inserción

Las notificaciones de inserción se entregan a través de infraestructuras específicas para la plataforma llamadas *Sistemas de notificación de plataforma* (PNS). Un PNS ofrece funciones estrictamente esenciales (es decir, sin compatibilidad para difusión, personalización) y no tiene una interfaz común. Por ejemplo, para enviar una notificación a una aplicación de la Tienda Windows, un desarrollador debe ponerse en contacto con el Servicio de notificaciones de Windows (WNS) para enviar una notificación a un dispositivo iOS; luego, el mismo desarrollador debe ponerse en contacto con el Servicio de notificaciones de inserción de Apple (APNS) y volver a enviar el mensaje.

Sin embargo, en un alto nivel, todos los sistemas de notificación de plataforma siguen el mismo patrón:

1.  La aplicación cliente se pone en contacto con el PNS para recuperar el *identificador*. El tipo de identificador depende del sistema. En el caso de WNS, es un URI o un "canal de notificaciones". En el caso de APNS, es un token.
2.  La aplicación cliente almacena este identificador en el *back-end* de la aplicación para usarlo más adelante. En el caso de WNS, el back-end normalmente es un servicio en la nube. En el caso de Apple, el sistema se llama *proveedor*.
3.  Para enviar una notificación de inserción, el back-end de la aplicación se pone en contacto con el PNS a través del identificador para dirigirse a una instancia de aplicación cliente específica.
4.  El PNS reenvía la notificación al dispositivo que especifica el identificador.

![][0]

## <span class="short-header">Los desafíos de las notificaciones de inserción</span>Los desafíos de las notificaciones de inserción

A pesar de que estos sistemas son muy potentes, de todos modos dejan mucho trabajo al desarrollador de aplicaciones para poder implementar incluso escenarios comunes de notificaciones de inserción, como la difusión o el envío de notificaciones de inserción a un usuario.

Las notificaciones de inserción son una de las características más solicitadas en los servicios en la nube para las aplicaciones móviles. El motivo es que la infraestructura que se requiere para hacerlas funcionar es muy complejo y, en gran parte, no está relacionada con la lógica de negocios principal de la aplicación. Algunas de las dificultades que presenta la creación de una infraestructura de inserción a petición son:

-   **Dependencia de la plataforma.** A fin de enviar notificaciones a dispositivos en distintas plataformas, se deben codificar varias interfaces en el back-end. No solo son distintos los detalles a bajo nivel, sino que la presentación de la notificación (icono, notificación del sistema o distintivo) también depende de la aplicación. Estas diferencias pueden llevar a un código de back-end complejo y difícil de mantener.

-   **Escala.** Escalar esta infraestructura tiene dos aspectos:

1.  Según las directrices de PNS, se deben actualizar los tokens de dispositivo cada vez que se inicia la aplicación. Esto genera una gran cantidad de tráfico (y los consecuentes accesos a la base de datos) solo para mantener actualizados los tokens de dispositivo. Cuando la cantidad de dispositivos crece (posiblemente a millones), no es posible pasar por alto el costo de crear y mantener esta infraestructura.
2.  La mayoría de los PNS no es compatible con la difusión a varios dispositivos. Más allá de eso, la difusión a millones de dispositivos resulta en millones de llamadas a los PNS. La capacidad de escalar estas solicitudes no es algo trivial, porque normalmente los desarrolladores de aplicaciones desean mantener baja la latencia total (por ejemplo, el último dispositivo que recibe el mensaje no debería recibir la notificación 30 minutos después de enviadas las notificaciones, porque en muchos casos eso iría en contra del propósito de las propias notificaciones de inserción).

-   **Enrutamiento.** Los PNS brindan una forma de enviar un mensaje a un dispositivo. Sin embargo, en la mayoría de las aplicaciones, las notificaciones se dirigen a usuarios y/o grupos de interés (por ejemplo, todos los empleados asignados a cierta cuenta de cliente). De tal modo, a fin de enrutar las notificaciones a los dispositivos correctos, el back-end de la aplicación debe mantener un registro que asocie grupos de interés con tokens de dispositivo. Esta sobrecarga se agrega al tiempo plazo de comercialización total y a los costos de mantenimiento de una aplicación.

## <span class="short-header">¿Por qué usar los Centros de notificaciones?</span>¿Por qué usar los Centros de notificaciones?

Los centros de notificaciones proporcionan una infraestructura de notificaciones de inserción lista para usar compatible con:

-   **Varias plataformas.** Los centros de notificaciones proporcionan una interfaz común para enviar notificaciones a todas las plataformas compatibles. El back-end de la aplicación puede enviar notificaciones en formatos específicos para una plataforma o independientes de la plataforma. Los centros de notificaciones pueden enviar notificaciones de inserción a aplicaciones de la Tienda Windows, iOS, Android y Windows Phone.
-   **Enrutamiento público/Subenrutamiento** Cuando se envía su identificador a un centro de notificaciones, cada dispositivo puede especificar una o más *etiquetas*. Para obtener más información acerca de las etiquetas, consulte la siguiente sección. Las etiquetas no deben ser aprovisionadas previamente ni eliminadas. Las etiquetas brindan una manera simple de enviar notificaciones a usuarios o grupos de interés. Como las etiquetas pueden contener cualquier identificador específico para una aplicación (como identificadores de usuario o grupo), al usarlas se libera al back-end de la aplicación de la carga de tener que almacenar y administrar identificadores de dispositivo.
-   **Escala.** Los centros de notificaciones escalan hasta millones de dispositivos sin tener que volver a diseñar o particionar.

Los centros de notificaciones usan una infraestructura completa de notificaciones de inserción de varias plataformas y con escalamiento horizontal, además de reducir considerablemente el código específico de inserción que se ejecuta en el back-end de la aplicación. Los centros de notificaciones implementan toda la funcionalidad de una infraestructura de inserción. Los dispositivos solo son responsables de registrar sus identificadores de PNS, mientras que el back-end es responsable de enviar mensajes independientemente de la plataforma a usuarios o grupos de interés.

![][1]

## Pasos siguientes

Obtenga más información acerca de los Centros de notificaciones en estos temas:

-   **[Cómo utilizan los clientes los Centros de notificaciones][Cómo utilizan los clientes los Centros de notificaciones]**

-   **[Tutoriales y guías sobre los Centros de notificaciones][Tutoriales y guías sobre los Centros de notificaciones]**

-   **Tutoriales de introducción a los Centros de notificaciones** ([iOS][iOS], [Android][Android], [Windows Universal][Windows Universal], [Windows Phone][Windows Phone], [Kindle][Kindle], [Xamarin.iOS][Xamarin.iOS], [Xamarin.Android][Xamarin.Android])

Las referencias pertinentes para la API administrada de .NET referidas a las notificaciones de inserción se pueden encontrar en los siguientes temas:

-   [Microsoft.WindowsAzure.Messaging.NotificationHub][Microsoft.WindowsAzure.Messaging.NotificationHub]
-   [Microsoft.ServiceBus.Notifications][Microsoft.ServiceBus.Notifications]

  [0]: ./media/notification-hubs-overview/SBPushNotifications1.gif
  [1]: ./media/notification-hubs-overview/SBPushNotifications2.gif
  [Cómo utilizan los clientes los Centros de notificaciones]: http://azure.microsoft.com/es-es/services/notification-hubs
  [Tutoriales y guías sobre los Centros de notificaciones]: http://azure.microsoft.com/es-es/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/es-es/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/es-es/documentation/articles/notification-hubs-android-get-started
  [Windows Universal]: http://azure.microsoft.com/es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/es-es/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/es-es/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/es-es/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/es-es/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/es-es/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/es-es/library/microsoft.servicebus.notifications.aspx
