<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Introducción a las notificaciones de inserción en Servicios móviles
===================================================================

[C\# para Tienda Windows](/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push "C# para Tienda Windows")[JavaScript para Tienda Windows](/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push "JavaScript para Tienda Windows")[Windows Phone](/en-us/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-ios-get-started-push "iOS")[Android](/en-us/documentation/articles/mobile-services-javascript-backend-android-get-started-push "Android")

[Back-end de .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ "Back-end de .NET") | [Back-end de JavaScript](/en-us/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/ "Back-end de JavaScript")

Este tema muestra cómo puede usar Servicios móviles de Azure para enviar notificaciones de inserción a una aplicación de la Tienda Windows. Este tutorial le permite habilitar notificaciones de inserción con los Centros de notificaciones de Azure para el proyecto de inicio rápido. Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción con Centros de notificaciones cada vez que se inserte un registro. El centro de notificaciones que cree es gratuito con el servicio móvil, puede administrarse independientemente del servicio móvil y pueden utilizarlo otras aplicaciones y servicios.

> [WACOM.NOTE]Este tutorial demuestra cómo funciona la integración de los servicios móviles con los Centros de notificaciones, que actualmente se encuentra en vista previa. De manera predeterminada, no está habilitado el envío de notificaciones utilizando los Centros de notificaciones desde un back-end de JavaScript. Una vez que se ha creado un nuevo centro de notificaciones, el proceso de integración no puede revertirse. Actualmente las notificaciones de inserción para iOS y Android solo están disponibles mediante la compatibilidad de inserción predeterminada descrita en [esta versión del tema](/en-us/documentation/articles/mobile-services-windows-phone-get-started-push/).

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Actualización de la aplicación para registrarse a fin de recibir notificaciones](#update-app)
2.  [Actualización de scripts del servidor para enviar notificaciones de inserción](#update-scripts)
3.  [Inserción de datos para recibir notificaciones de inserción](#test)

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de empezar este tutorial, primero debe completar [Introducción a los Servicios móviles](/en-us/documentation/articles/mobile-services-windows-phone-get-started) o [Introducción a los datos](/en-us/documentation/articles/mobile-services-windows-phone-get-started-data) para conectar su proyecto al servicio móvil. Si no se conecta un servicio móvil, el Asistente para agregar notificaciones de inserción se encarga de crear esta conexión.

Actualización de la aplicación para registrarse a fin de recibir notificaciones
-------------------------------------------------------------------------------

Para que la aplicación pueda recibir notificaciones de inserción, debe registrar un canal de notificaciones.

1.  En Visual Studio, abra el archivo App.xaml.cs y agregue la siguiente instrucción `using`:

         using Microsoft.Phone.Notification;

2.  Agregue lo siguiente a App.xaml.cs:

         public static HttpNotificationChannel CurrentChannel { get; private set; }

         private void AcquirePushChannel()
         {
             CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

             if (CurrentChannel == null)
             {
                 CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                 CurrentChannel.Open();
                 CurrentChannel.BindToShellToast();
             }

             CurrentChannel.ChannelUriUpdated +=
                 new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                 {

                     // Registrarse para notificaciones con el nuevo canal.
                     await MobileService.GetPush()
                         .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                 });
         }

    Este código recupera el valor de ChannelURI de la aplicación desde WNS y, a continuación, lo registra para notificaciones de inserción.

    > [WACOM.NOTE]En este tutorial, el servicio móvil envía una notificación del sistema al dispositivo. Cuando envía una notificación de icono, debe llamar al método **BindToShellTile** en el canal.

3.  En la parte superior del controlador de eventos **Application\_Launching** en App.xaml.cs, agregue la siguiente llamada al nuevo método **AcquirePushChannel**:

         AcquirePushChannel();

    Esto garantiza que se solicitará registro cada vez que se cargue la página. En la aplicación, es posible que solo desee realizar este registro de manera periódica para asegurarse de que el registro esté actualizado.

4.  Presione la tecla **F5** para ejecutar la aplicación. Se muestra un cuadro de diálogo emergente con la clave de registro.

5.  En el Explorador de soluciones, expanda **Propiedades**, abra el archivo WMAppManifest.xml, haga clic en la pestaña **Funcionalidades** y asegúrese de que la funcionalidad **ID\_\_*CAP*\_\_PUSH\_NOTIFICATION** esté marcada.

  ![](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png) 
 Esto asegura que la aplicación puede generar notificaciones del sistema.

Actualización de scripts del servidor para enviar notificaciones de inserción
-----------------------------------------------------------------------------

Finalmente, debe actualizar el script registrado para insertar la operación en la tabla TodoItem a fin de enviar notificaciones.

1.  Haga clic en **TodoItem**, en **Script** y seleccione **Insert**.

      ![][10]

2.  Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Save**:

         function insert(item, user, request) {
         // Defina una carga para la notificación del sistema de Windows Phone.
         var payload = '<
         xml version="1.0" encoding="utf-8"
         >' +
             '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
             '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text + 
             '</wp:Text2></wp:Toast></wp:Notification>';
            
         request.execute({
             success: function() {
                 // Si la inserción se realiza correctamente, enviar una notificación.
                push.mpns.send(null, payload, 'toast', 22, {
                     success: function(pushResponse) {
                         console.log("Sent push:", pushResponse);
                         request.respond();
                         },              
                         error: function (pushResponse) {
                             console.log("Error Sending push:", pushResponse);
                             request.respond(500, { error: pushResponse });
                             }
                         });
                     }
                 });      
         }

    Este script de inserción envía una notificación de inserción (con el texto del elemento insertado) a todos los registros de aplicaciones de Windows Phone una vez que se realiza la inserción.

3.  Haga clic en la pestaña **Insertar**, marque **Habilite las notificaciones de inserción no autenticadas** y, a continuación, haga clic en **Guardar**.

    ![](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-push-tab.png)

    Con esto se permite que el servicio móvil se conecte a MPNS en modo sin autenticar para enviar notificaciones de inserción.

    > [WACOM.NOTE] Este tutorial usa MPNS en modo sin autenticar. En este modo, MPNS limita el número de notificaciones que se pueden enviar a un canal de dispositivo. Para quitar esta restricción, debe generar y cargar un certificado con un clic en **Upload** y seleccionando el certificado. Para obtener más información sobre la generación del certificado, consulte [Setting up an authenticated web service to send push notifications for Windows Phone](http://msdn.microsoft.com/en-us/library/windowsphone/develop/ff941099(v=vs.105).aspx).

Pruebas de notificaciones de inserción en su aplicación
-------------------------------------------------------

1.  En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

2.  En la aplicación, escriba el texto "hello push" en el cuadro de texto y, a continuación, haga clic en **Guardar**.

      ![][4]

Esto envía una solicitud de inserción al servicio móvil para almacenar el elemento agregado. Observe que la aplicación recibe una notificación del sistema que dice **hello push**.

Pasos siguientes
----------------

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de la Tienda Windows para trabajar con datos en Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Introducción a los Centros de notificaciones](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
    Aprenda a aprovechar Centros de notificaciones en su aplicación de la Tienda Windows.

-   [Envío de notificaciones a los suscriptores](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
    Sepa cómo los usuarios se pueden registrar y recibir notificaciones de inserción de categorías que les interesan.

-   [Envío de notificaciones a los usuarios](/en-us/manage/services/notification-hubs/notify-users/)
    Sepa cómo enviar notificaciones de inserción desde un Servicio móvil a usuarios específicos en cualquier dispositivo.

-   [Envío de notificaciones entre plataformas a los usuarios](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
    Sepa cómo utilizar las plantillas para enviar notificaciones de inserción desde un Servicio móvil sin tener que diseñar cargas específicas de plataforma en el back-end.

Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a los datos](/en-us/documentation/articles/mobile-services-windows-phone-get-started-data)
    Obtenga más información sobre almacenar y consultar datos con los Servicios móviles.

-   [Introducción a la autenticación](/en-us/documentation/articles/mobile-services-windows-phone-get-started-users)
    Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

-   [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/?LinkId=262293)
    Obtenga más información acerca del registro y uso de scripts de servidor.

-   [Referencia conceptual de Servicios móviles con .NET](/en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library)
    Obtenga más información sobre cómo utilizar Servicios móviles con .NET.



<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[10]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-insert-script-push2.png
[11]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-push-tab.png