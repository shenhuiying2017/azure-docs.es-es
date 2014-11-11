<properties linkid="develop-mobile-tutorials-get-started-with-push-js" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (JavaScript)" metaKeywords="" description="Learn how to use push notifications in your Windows Store app with Azure Mobile Services." metaCanonical="http://www.windowsazure.com/es-es/develop/mobile/tutorials/get-started-with-push-dotnet/" disqusComments="1" umbracoNaviHide="1" title="Get started with push notifications in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Introducción a las notificaciones de inserción en Servicios móviles con Visual Studio 2012

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/es-es/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012" title="C# para Tienda Windows">C# para Tienda Windows</a><a href="/es-es/develop/mobile/tutorials/get-started-with-push-js-vs2012" title="JavaScript para Tienda Windows" class="current">JavaScript para Tienda Windows</a><a href="/es-es/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a><a href="/es-es/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a><a href="/es-es/develop/mobile/tutorials/get-started-with-push-android" title="Android">Android</a><a href="/es-es/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/es-es/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a> 
</div>

Este tema muestra cómo puede utilizar Servicios móviles de Azure para enviar notificaciones de inserción a una aplicación de la Tienda Windows.
En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con el servicio de notificaciones de inserción de Windows (WNS). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

<div class="dev-callout"><b>Nota:</b>
    <p>Este tutorial agrega notificaciones de inserci&oacute;n a una aplicaci&oacute;n de la Tienda Windows creadas en Visual Studio 2012. Visual Studio 2013 incluye nuevas caracter&iacute;sticas que facilitan la configuraci&oacute;n de notificaciones de inserci&oacute;n en su aplicaci&oacute;n de la Tienda Windows con Servicios m&oacute;viles. Para obtener la versi&oacute;n de Visual Studio 2013, consulte <a href="/es-es/develop/mobile/tutorials/get-started-with-push-js">Introducci&oacute;n a las notificaciones de inserci&oacute;n</a>.</p>
</div>

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Registro de la aplicación para notificaciones de inserción y configuración de Servicios móviles][Registro de la aplicación para notificaciones de inserción y configuración de Servicios móviles]
2.  [Creación de la tabla Registros][Creación de la tabla Registros]
3.  [Incorporación de notificaciones de inserción a la aplicación][Incorporación de notificaciones de inserción a la aplicación]
4.  [Actualización de scripts para enviar notificaciones de inserción][Actualización de scripts para enviar notificaciones de inserción]
5.  [Inserción de datos para recibir notificaciones][Inserción de datos para recibir notificaciones]

Este tutorial requiere lo siguiente:

-   Microsoft Visual Studio 2012 Express para Windows 8
-   Activación de la cuenta de la Tienda Windows

Este tutorial se basa en el tutorial [Introducción a los datos][Introducción a los datos]. Antes de empezar este tutorial, primero debe completar [este tutorial][Introducción a los datos].

## <a name="register"></a><span class="short-header">Registro de la aplicación</span>Registro de la aplicación para la Tienda Windows

Para poder enviar notificaciones de inserción a las aplicaciones de la Tienda Windows desde Servicios móviles, debe enviar su aplicación a la Tienda Windows. A continuación, debe configurar el servicio móvil para que se integre con WNS.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

El servicio móvil y la aplicación están ahora configurados para funcionar con WNS. A continuación, creará una tabla para almacenar los registros.

## <a name="create-table"></a>Creación de una tabla

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

## <a name="add-push"></a><span class="short-header">Incorporación de notificaciones de inserción</span>Incorporación de notificaciones de inserción a la aplicación

1.  Abra el archivo default.js e inserte el siguiente fragmento de código en la sobrecarga del método **app.OnActivated**, justo después del método **args.setPromise**:

        // Get the channel for the application.
        var channel;
        var channelOperation = Windows.Networking.PushNotifications
            .PushNotificationChannelManager
            .createPushNotificationChannelForApplicationAsync()
            .then(function (newChannel) {
                channel = newChannel;
            });

    Este código adquiere y almacena un canal de notificaciones de inserción cada vez que se inicia la aplicación.

2.  Inserte el siguiente código después del código que crea la instancia **MobileServiceClient**:

        // Insert the new channel URI into the Registrations table.
        var registrationsTable = client.getTable('Registrations');
        registrationsTable.insert({ handle: channel.uri });         

    Este código inserta el canal actual en la tabla Registrations.

3.  Abra el archivo Package.appxmanifest y asegúrese de que en la pestaña **IU de la aplicación**, la opción **Capacidad de aviso** esté definida en **Sí**.

    ![][0]

    Esto asegura que la aplicación puede generar notificaciones del sistema.

## <a name="update-scripts"></a><span class="short-header">Actualización del script de inserción</span>Actualización del script de inserción registrado en el Portal de administración

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

1.  Haga clic en **TodoItem**, en **Script** y seleccione **Insert**.

    ![][1]

2.  Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Save**:

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    request.respond();
                    sendNotifications();
                }
            });

            function sendNotifications() {
                var registrationsTable = tables.getTable('Registrations');
                registrationsTable.read({
                    success: function(registrations) {
                        registrations.forEach(function(registration) {
                            push.wns.sendToastText04(registration.handle, {
                                text1: item.text
                            }, {
                                success: function(pushResponse) {
                                    console.log("Sent push:", pushResponse);
                                }
                            });
                        });
                    }
                });
            }
        }

    Este script de inserción envía una notificación de inserción (con el texto del elemento insertado) a todos los canales almacenados en la tabla **Registros**.

## <a name="test"></a><span class="short-header">Prueba de la aplicación</span>Pruebas de notificaciones de inserción en su aplicación

1.  En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

2.  En la aplicación, escriba un texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

    ![][2]

    Tenga en cuenta que una vez finalizada la inserción, la aplicación recibe una notificación de inserción de WNS.

    ![][3]

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial demuestra la funcionalidad de notificación de inserción básica que brinda Servicios móviles. Si su aplicación requiere funcionalidades más avanzadas, como enviar notificaciones entre plataformas, enrutamiento basado en suscripción o volúmenes de tamaño muy grande, considere utilizar Centros de notificaciones de Azure con su servicio móvil. Para obtener más información, consulte uno de los siguientes temas de Centros de notificaciones:

-   [Introducción a los Centros de notificaciones][Introducción a los Centros de notificaciones]
    
	Aprenda a aprovechar Centros de notificaciones en su aplicación de la Tienda Windows.

-   [Envío de notificaciones a los suscriptores][Envío de notificaciones a los suscriptores]
    
	Sepa cómo los usuarios se pueden registrar y recibir notificaciones de inserción de categorías que les interesan.

-   [Envío de notificaciones a los usuarios][Envío de notificaciones a los usuarios]
    
	Obtenga información sobre cómo enviar notificaciones de inserción desde un servicio móvil a usuarios específicos en cualquier dispositivo.

-   [Envío de notificaciones entre plataformas a los usuarios][Envío de notificaciones entre plataformas a los usuarios]
    
	Obtenga información sobre cómo usar las plantillas para enviar notificaciones de inserción desde un servicio móvil sin tener que diseñar cargas específicas de plataforma en el back-end.

Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a los datos][Introducción a los datos]
    
	Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

-   [Introducción a la autenticación][Introducción a la autenticación]
    
	Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

-   [Referencia del script del servidor de Servicios móviles][Referencia del script del servidor de Servicios móviles]
    
	Obtenga más información acerca del registro y uso de scripts de servidor.

-   [Referencia conceptual de Servicios móviles con HTML/JavaScript][Referencia conceptual de Servicios móviles con HTML/JavaScript]
    
	Obtenga más información acerca de cómo utilizar los Servicios móviles con HTML y JavaScript.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Registro de la aplicación para notificaciones de inserción y configuración de Servicios móviles]: #register
  [Creación de la tabla Registros]: #create-table
  [Incorporación de notificaciones de inserción a la aplicación]: #add-push
  [Actualización de scripts para enviar notificaciones de inserción]: #update-scripts
  [Inserción de datos para recibir notificaciones]: #test
  [Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-js
  [mobile-services-register-windows-store-app]: ../includes/mobile-services-register-windows-store-app.md
  [mobile-services-create-new-push-table]: ../includes/mobile-services-create-new-push-table.md
  [0]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-app-enable-toast-win8.png
  [mobile-services-update-registrations-script]: ../includes/mobile-services-update-registrations-script.md
  [1]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-insert-script-push2.png
  [2]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-quickstart-push1.png
  [3]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-quickstart-push2.png
  [Introducción a los Centros de notificaciones]: /es-es/manage/services/notification-hubs/getting-started-windows-dotnet/
  [Envío de notificaciones a los suscriptores]: /es-es/manage/services/notification-hubs/breaking-news-dotnet/
  [Envío de notificaciones a los usuarios]: /es-es/manage/services/notification-hubs/notify-users/
  [Envío de notificaciones entre plataformas a los usuarios]: /es-es/manage/services/notification-hubs/notify-users-xplat-mobile-services/
  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-js
  [Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Referencia conceptual de Servicios móviles con HTML/JavaScript]: /es-es/develop/mobile/how-to-guides/work-with-html-js-client/
