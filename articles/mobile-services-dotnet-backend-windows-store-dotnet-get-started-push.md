<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc" solutions="" manager="" editor="" />

Introducción a las notificaciones de inserción en Servicios móviles
===================================================================

[C\# para Tienda Windows](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push "C# para Tienda Windows")[JavaScript para Tienda Windows](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push "JavaScript para Tienda Windows")

[Back-end de .NET](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push "Back-end de .NET") | [Back-end de JavaScript](/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/ "Back-end de JavaScript")

Este tema muestra cómo puede usar Servicios móviles de Azure con un back-end de .NET para enviar notificaciones de inserción a una aplicación de la Tienda Windows. Este tutorial le permite habilitar notificaciones de inserción con los Centros de notificaciones de Azure para el proyecto de inicio rápido. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción desde el back-end de .NET con los Centros de notificación cada vez que se inserte un registro. El centro de notificaciones que cree es gratuito con el servicio móvil, puede administrarse independientemente del servicio móvil y pueden utilizarlo otras aplicaciones y servicios.

> [WACOM.NOTE]La integración de los Servicios móviles con los Centros de notificación está actualmente en vista previa y únicamente está disponible para las plataformas Windows. Mientras tanto, todavía puede enviar notificaciones de inserción a iOS y dispositivos Android desde el servicio de back-end de .NET mediante el centro de notificaciones conectado, tal como se describe en **Introducción a los Centros de notificaciones** ([iOS](/es-es/documentation/articles/notification-hubs-ios-get-started)/[Android](/es-es/documentation/articles/notification-hubs-android-get-started)).

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Registro de la aplicación con WNS y configuración de Servicios móviles](#register)
2.  [Actualización de la aplicación para registrarse a fin de recibir notificaciones](#update-app)
3.  [Actualización del servidor para enviar notificaciones de inserción](#update-server)
4.  [Inserción de datos para recibir notificaciones de inserción](#test)

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de empezar este tutorial, primero debe completar [Introducción a los Servicios móviles](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started) o [Introducción a los datos](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data) para conectar su proyecto al servicio móvil. Si no se conecta un servicio móvil, el Asistente para agregar notificaciones de inserción se encarga de crear esta conexión.

Registro de la aplicación con WNS y configuración de Servicios móviles
----------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-javascript-backend-register-windows-store-app](../includes/mobile-services-javascript-backend-register-windows-store-app.md)]

El servicio móvil y la aplicación están ahora configurados para funcionar con WNS y los Centros de notificaciones. A continuación, actualizará la aplicación de Tienda Windows para el registro de notificaciones.

Actualización de la aplicación para registrarse a fin de recibir notificaciones
-------------------------------------------------------------------------------

Para que la aplicación pueda recibir notificaciones de inserción, debe registrar un canal de notificaciones.

1.  En Visual Studio, abra el archivo App.xaml.cs y agregue las siguientes instrucciones `using`:

         using Windows.Networking.PushNotifications;
         using Windows.UI.Popups;

2.  Agregue el siguiente método `InitNotificationAsync` a la clase **App** para crear un canal de notificaciones de inserción y registrarse para obtener notificaciones de inserción.

         private async void InitNotificationsAsync()
         {
             // Solicitar un canal de notificaciones de inserción.
             var channel = await PushNotificationChannelManager
                 .CreatePushNotificationChannelForApplicationAsync();

             // Registrarse para notificaciones con el nuevo canal.
             System.Exception exception = null;
             try
             {
                 await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
             }
             catch (System.Exception ex)
             {
                 exception = ex;
             }
             if (exception != null)
             {
                 var dialog = new MessageDialog(exception.Message, "Registering Channel URI");
                 dialog.Commands.Add(new UICommand("OK"));
                 await dialog.ShowAsync();
             }
         }

    Este código recupera el valor de ChannelURI de la aplicación desde WNS y, a continuación, lo registra para notificaciones de inserción.

3.  En la parte superior del controlador de eventos **OnLaunched** en App.xaml.cs, agregue la siguiente llamada al nuevo método **InitNotificationsAsync**:

         InitNotificationsAsync();

    Esto garantiza que se solicitará registro cada vez que se cargue la página. En la aplicación, es posible que solo desee realizar este registro de manera periódica para asegurarse de que el registro esté actualizado.

4.  En Visual Studio, abra el archivo Package.appxmanifest y asegúrese de que la opción **Capacidad de aviso** esté definida en **Sí** en la pestaña **IU de la aplicación**. Guarde el archivo.

      ![][1]

      Esto asegura que la aplicación puede generar notificaciones del sistema. 

Actualización del servidor para enviar notificaciones de inserción
------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

Pruebas de notificaciones de inserción en su aplicación
-------------------------------------------------------

1.  En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

2.  En la aplicación, escriba un texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

      ![][2]

      Tenga en cuenta que una vez finalizada la inserción, la aplicación recibe una notificación de inserción de WNS.

      ![][3]

Pasos siguientes
----------------

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de la Tienda Windows para trabajar con datos en Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Introducción a los Centros de notificaciones](/es-es/manage/services/notification-hubs/getting-started-windows-dotnet/)
    Aprenda a aprovechar Centros de notificaciones en su aplicación de la Tienda Windows.

-   [Envío de notificaciones a los suscriptores](/es-es/manage/services/notification-hubs/breaking-news-dotnet/)
    Sepa cómo los usuarios se pueden registrar y recibir notificaciones de inserción de categorías que les interesan.

-   [Envío de notificaciones a los usuarios](/es-es/manage/services/notification-hubs/notify-users/)
    Sepa cómo enviar notificaciones de inserción desde un Servicio móvil a usuarios específicos en cualquier dispositivo.

-   [Envío de notificaciones entre plataformas a los usuarios](/es-es/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
    Sepa cómo utilizar las plantillas para enviar notificaciones de inserción desde un Servicio móvil sin tener que diseñar cargas específicas de plataforma en el back-end.

Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a los datos](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data)
    Obtenga más información sobre cómo almacenar y consultar datos con los servicios móviles en tiempo de ejecución .NET.

-   [Introducción a la autenticación](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users)
    Aprenda a autenticar a los usuarios de su aplicación con distintos tipos de cuenta con los servicios móviles en tiempo de ejecución .NET.

-   [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/?LinkId=262293)
    Obtenga más información acerca del registro y uso de scripts de servidor.

-   [Referencia conceptual de Servicios móviles con .NET](/es-es/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library)
    Obtenga más información sobre cómo utilizar Servicios móviles con .NET.



<!-- Images. -->

[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/enable-toast.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png