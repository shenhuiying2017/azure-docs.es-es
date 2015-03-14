<properties 
	pageTitle="Centros de notificaciones de Azure" 
	description="Obtenga información acerca de cómo usar las notificaciones de inserción en Azure. Ejemplos de código escritos en C# con la API de .NET." 
	authors="ggailey777" 
	manager="dwrede" 
	editor="" 
	services="notification-hubs" 
	documentationCenter=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="glenga"/>


#Centros de notificaciones de Azure


La compatibilidad con las notificaciones de inserción en Azure le permite tener acceso a una infraestructura multiplataforma y de escalamiento horizontal fácil de usar, que simplifica considerablemente la implementación de notificaciones de inserción tanto en aplicaciones de consumidor, como en aplicaciones empresariales para plataformas móviles.

<h2>¿Qué son las notificaciones de inserción?</h2>

Los smartphones y las tabletas cuentan con la posibilidad de "notificar" a los usuarios cuando se ha producido un evento. En las aplicaciones de la Tienda Windows, la notificación puede dar como resultado un _toast_: aparece una ventana no modal, con un sonido, para indicar una nueva inserción. En los dispositivos con iOS de Apple, la notificación de inserción interrumpe de manera similar con un cuadro de diálogo, que solicita al usuario que vea o cierre la notificación. Un clic en **Ver** abre la aplicación que está recibiendo el mensaje.

Las notificaciones de inserción ayudan a que los dispositivos móviles muestren información actualizada mientras ahorran energía. Las notificaciones de inserción son un componente esencial de las aplicaciones de consumidor, donde se utilizan para aumentar el uso y la interacción con la aplicación. Las notificaciones también son útiles para las empresas cuando la información actualizada aumenta la capacidad de respuesta de los empleados ante eventos empresariales. 

Algunos ejemplos específicos de escenarios de interacción móvil son:

1.  Actualización de un icono en Windows 8 o Windows Phone con información financiera actual.
2.  Alerta con una notificación del sistema a un usuario cuando se le ha asignado algún elemento de trabajo en una aplicación empresarial basada en el flujo de trabajo.
3.  Visualización de un distintivo con el número de clientes potenciales actuales en una aplicación CRM (como Microsoft Dynamics CRM).

<h2>Funcionamiento de las notificaciones de inserción</h2>

Las notificaciones de inserción se entregan a través de infraestructuras específicas para la plataforma llamadas Platform Notification Systems (PNS). Un PNS ofrece funciones estrictamente esenciales (es decir, sin compatibilidad para difusión, personalización) y no tiene una interfaz común. Por ejemplo, para enviar una notificación a una aplicación de la Tienda Windows, un desarrollador debe ponerse en contacto con el Servicio de notificaciones de Windows (WNS) para enviar una notificación a un dispositivo iOS; luego, el mismo desarrollador debe ponerse en contacto con el Servicio de notificaciones push de Apple (APNS) y volver a enviar el mensaje.

Sin embargo, en un alto nivel, todos los sistemas de notificación de plataforma siguen el mismo patrón:

1.  La aplicación cliente se pone en contacto con el PNS para recuperar el _identificador_. El tipo de identificador depende del sistema. En el caso de WNS, es un URI o un "canal de notificaciones". En el caso de APNS, es un token.
2.  La aplicación cliente almacena este identificador en el _back-end_ de la aplicación para usarlo más adelante. En el caso de WNS, el back-end normalmente es un servicio en la nube. En el caso de Apple, el sistema se llama _proveedor_.
3.  Para enviar una notificación de inserción, el back-end de la aplicación se pone en contacto con el PNS a través del identificador para dirigirse a una instancia de aplicación cliente específica.
4.  El PNS reenvía la notificación al dispositivo que especifica el identificador.

![][0]

<h2>Los desafíos de las notificaciones de inserción</h2>

A pesar de que estos sistemas son muy potentes, de todos modos dejan mucho trabajo al desarrollador de aplicaciones para poder implementar incluso escenarios comunes de notificaciones de inserción, como la difusión o el envío de notificaciones de inserción a un usuario.

Las notificaciones de inserción son una de las características más solicitadas en los servicios en la nube para las aplicaciones móviles. El motivo es que la infraestructura que se requiere para hacerlas funcionar es muy complejo y, en gran parte, no está relacionada con la lógica de negocios principal de la aplicación. Algunas de las dificultades que presenta la creación de una infraestructura de inserción a petición son:

- **Dependencia de la plataforma.** Para enviar notificaciones a dispositivos en distintas plataformas, se deben codificar varias interfaces en el back-end. No solo son distintos los detalles de bajo nivel, sino que la presentación de la notificación (icono, sistema o distintivo) también depende la plataforma. Estas diferencias pueden dar lugar a código de back-end complejo y difícil de mantener.

- **Scale.** Escalar esta infraestructura tiene dos aspectos:
1. Según las directrices de PNS, se deben actualizar los tokens de dispositivo cada vez que se inicia la aplicación. Esto genera una gran cantidad de tráfico (y los consecuentes accesos a la base de datos) solo para mantener actualizados los tokens de dispositivo. Cuando la cantidad de dispositivos crece (posiblemente a millones), no es posible pasar por alto el costo de crear y mantener esta infraestructura.
2.  La mayoría de los PNS no es compatible con la difusión a varios dispositivos. Más allá de eso, la difusión a millones de dispositivos resulta en millones de llamadas a los PNS. La capacidad de escalar estas solicitudes no es algo trivial, porque normalmente los desarrolladores de aplicaciones desean mantener baja la latencia total (por ejemplo, el último dispositivo que recibe el mensaje no debería recibir la notificación 30 minutos después de enviadas las notificaciones, porque en muchos casos eso iría en contra del propósito de las propias notificaciones de inserción).
- **Enrutamiento.** Pns proporcionan una manera de enviar un mensaje a un dispositivo. Sin embargo, en la mayoría de las aplicaciones, las notificaciones se dirigen a usuarios o grupos de interés (por ejemplo, todos los empleados asignados a una determinada cuenta de cliente). Por lo tanto, con el fin de enrutar las notificaciones a los dispositivos correctos, el back-end de la aplicación debe mantener un registro que asocia los grupos de interés con tokens de dispositivo. Esta sobrecarga se agrega al tiempo total para los costes de comercialización y mantenimiento de una aplicación.

<h2>¿Por qué usar los Centros de notificaciones?</h2>

Los Centros de notificaciones proporcionan una infraestructura de notificaciones de inserción lista para usar compatible con:

- **Varias plataformas.** Los Centros de notificaciones proporcionan una interfaz común para enviar notificaciones a todas las plataformas compatibles. El back-end de la aplicación puede enviar notificaciones en formatos específicos de la plataforma o independientes de la plataforma. Los Centros de notificaciones pueden enviar notificaciones de inserción para aplicaciones de Tienda Windows, iOS, Android y Windows Phone.
- **Enrutamiento Pub/Sub.** Cada dispositivo, cuando envía su identificador a un Centro de notificaciones, puede especificar una o más _etiquetas_. Para obtener más información acerca de las etiquetas, vea la sección siguiente. Las etiquetas no deben proporcionarse previamente o eliminarse. Las etiquetas ofrecen una manera sencilla de enviar notificaciones a usuarios o grupos de interés. Puesto que las etiquetas pueden contener cualquier identificador específico de la aplicación (por ejemplo, Id. de usuario o grupo), su uso libera al back-end de la aplicación de la carga de tener que almacenar y administrar identificadores de dispositivo.
- **Escala.** Los Centros de notificaciones escalan hasta millones de dispositivos sin tener que volver a diseñar o particionar.

Los Centros de notificaciones usan una infraestructura completa de notificaciones de inserción de varias plataformas y con escalamiento horizontal, además de reducir considerablemente el código específico de inserción que se ejecuta en el back-end de la aplicación. Los Centros de notificaciones implementan toda la funcionalidad de una infraestructura de inserción. Los dispositivos solo son responsables de registrar sus identificadores de PNS, mientras que el back-end es responsable de enviar mensajes independientemente de la plataforma a usuarios o grupos de interés.

![][1]

## Pasos siguientes

Obtenga más información acerca de los Centros de notificaciones en estos temas:

+ **[Cómo utilizan los clientes los Centros de notificaciones]**

+ **[Tutoriales y guías sobre los Centros de notificaciones]** 

+ **Tutoriales de introducción a los Centros de notificaciones** ([iOS], [Android], [Windows Universal], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android])

Las referencias pertinentes para la API administrada de .NET referidas a las notificaciones de inserción se pueden encontrar en los siguientes temas:

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications] 


  [0]: ./media/notification-hubs-overview/SBPushNotifications1.gif
  [1]: ./media/notification-hubs-overview/SBPushNotifications2.gif
  [Cómo utilizan los clientes los Centros de notificaciones]: http://azure.microsoft.com/ services/notification-hubs
  [Tutoriales y guías sobre los Centros de notificaciones]: http://azure.microsoft.com/ documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/ documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/ documentation/articles/notification-hubs-android-get-started
  [Windows Universal]: http://azure.microsoft.com/ documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/ documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/ documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/ documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/ documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  
<!--HONumber=45--> 
