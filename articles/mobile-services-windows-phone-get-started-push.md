<properties pageTitle="Get started with push notifications (legacy push) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Phone app (legacy push)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Introducción a las notificaciones de inserción en Servicios móviles (inserción heredada)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="C# para Tienda Windows" >C# para Tienda Windows</a>
    <a href="/es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a>
    <a href="/es-es/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a>
    <a href="/es-es/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/es-es/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/es-es/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>    -->
    <a href="/es-es/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Back-end de .NET">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-windows-phone-get-started-push/"  title="Back-end de JavaScript" class="current">Back-end de JavaScript</a>
</div>

Este tema muestra cómo puede utilizar Servicios móviles de Azure para enviar notificaciones de inserción a una aplicación de Windows Phone 8.
En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con el servicio de notificaciones de inserción de Microsoft (MPNS). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

> [WACOM.NOTE] Este tema admite *servicios móviles* existentes que *todavía no se hayan actualizado para usar* la integración con Centros de notificaciones. Al crear un *servicio móvil* nuevo, esta funcionalidad integrada se habilita automáticamente. Para servicios móviles nuevos, consulte [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción].
>
> La solución Servicios móviles se integra con Centros de notificaciones de Azure para ofrecer compatibilidad con la funcionalidad de notificación de inserción adicional, como plantillas, varias plataformas y escala. *Debería actualizar los servicios móviles existentes para que usen Centros de notificaciones siempre que sea posible*. Una vez que haya realizado la actualización, consulte esta versión de [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción].

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Creación de la tabla Registros][Creación de la tabla Registros]
2.  [Incorporación de notificaciones de inserción a la aplicación][Incorporación de notificaciones de inserción a la aplicación]
3.  [Actualización de scripts para enviar notificaciones de inserción][Actualización de scripts para enviar notificaciones de inserción]
4.  [Inserción de datos para recibir notificaciones][Inserción de datos para recibir notificaciones]

Este tutorial requiere [Visual Studio 2012 Express para Windows Phone][Visual Studio 2012 Express para Windows Phone] o una versión posterior.

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles][Introducción a los Servicios móviles].

> [WACOM.NOTE]Cuando envíe más de 500 mensajes por usuario al día, debe utilizar Centros de notificaciones. Para obtener más información, consulte [Introducción a los Centros de notificaciones][Introducción a los Centros de notificaciones].

## <a name="create-table"></a>Creación de una tabla

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

## <a name="add-push"></a><span class="short-header">Incorporación de notificaciones de inserción</span>Incorporación de notificaciones de inserción a la aplicación

1.  En Visual Studio, abra el archivo de proyecto MainPage.xaml.cs y agregue el siguiente código que crea una nueva clase **Registrations**:

        public class Registrations
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "handle")]
            public string Handle { get; set; }
        }

    La propiedad Handle se utiliza para almacenar el URI de canal.

2.  Abra el archivo App.xaml.cs y agregue la siguiente instrucción using:

        using Microsoft.Phone.Notification;

3.  Agregue lo siguiente a App.xaml.cs:

        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private async void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }

           IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
           var registration = new Registrations { Handle = CurrentChannel.ChannelUri.AbsoluteUri };
           await registrationsTable.InsertAsync(registration);
        }

    Este código adquiere y almacena un canal para una suscripción a notificaciones e inserción y lo enlaza al icono predeterminado de la aplicación.

    <div class="dev-callout"><b>Nota:</b>
    <p>En este tutorial, el servicio m&oacute;vil env&iacute;a una notificaci&oacute;n de icono din&aacute;mico al dispositivo. Cuando env&iacute;a una notificaci&oacute;n del sistema, debe llamar al m&eacute;todo <strong>BindToShellToast</strong> en el canal. Para ser compatible con notificaciones del sistema y notificaciones de icono, llame a ambos m&eacute;todos, <strong>BindToShellTile</strong> y <strong>BindToShellToast</strong>. </p>
</div>

4.  En la parte superior del controlador de eventos **Application\_Launching** en App.xaml.cs, agregue la siguiente llamada al nuevo método **AcquirePushChannel**:

        AcquirePushChannel();

    Esto garantiza que la propiedad **CurrentChannel** se inicialice cada vez que se inicia la aplicación.

5.  En el Explorador de soluciones, expanda **Propiedades**, abra el archivo WMAppManifest.xml, haga clic en la pestaña **Funcionalidades** y asegúrese de que la funcionalidad \*\*ID\_**CAP**\_PUSH\_NOTIFICATION\*\* esté marcada.

    ![][0]

    Esto asegura que la aplicación puede recibir notificaciones de inserción.

## <a name="update-scripts"></a><span class="short-header">Actualización del script de inserción</span>Actualización de scripts de inserción registrados en el Portal de administración

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
                            push.mpns.sendFlipTile(registration.handle, {
                                title: item.text
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

1.  En Visual Studio, seleccione **Implementar solución** en el menú **Compilar**.

2.  En el emulador, deslícese rápidamente a la izquierda para que aparezca la lista de aplicaciones instaladas y busque la nueva aplicación **TodoList**.

3.  Mantenga pulsado el icono de la aplicación y, a continuación, seleccione **anclar a inicio** en el menú contextual.

    ![][2]

    Esta acción ancla un icono llamado **TodoList** al menú de inicio.

4.  Toque el icono llamado **TodoList** para iniciar la aplicación.

    ![][3]

5.  En la aplicación, escriba el texto "hello push" en el cuadro de texto y, a continuación, haga clic en **Guardar**.

    ![][4]

    Esto envía una solicitud de inserción al servicio móvil para almacenar el elemento agregado.

6.  Pulse el botón **Inicio** para volver al menú de inicio.

    ![][5]

    Observe que la aplicación recibió la notificación de inserción y que el título del icono ahora es **hello push**.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial demuestra la funcionalidad de notificación de inserción básica que brinda Servicios móviles. Si su aplicación requiere funcionalidades más avanzadas, como enviar notificaciones entre plataformas, enrutamiento basado en suscripción o volúmenes de tamaño muy grande, considere utilizar Centros de notificaciones de Azure con su servicio móvil. Para obtener más información, consulte uno de los siguientes temas de Centros de notificaciones:

-   [Introducción a los Centros de notificaciones][6]
    
	Aprenda a aprovechar Centros de notificaciones en su aplicación de la Tienda Windows.

-   [¿Qué son los Centros de notificaciones?][¿Qué son los Centros de notificaciones?]
    
	Aprenda a crear e insertar notificaciones para usuarios en varias plataformas.

-   [Envío de notificaciones a los suscriptores][Envío de notificaciones a los suscriptores]
    
	Sepa cómo los usuarios se pueden registrar y recibir notificaciones de inserción de categorías que les interesan.

<!--+ [Send notifications to users]     <br/>Learn how to send push notifications from a Mobile Service to specific users on any device.  + [Send cross-platform notifications to users]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a los datos][Introducción a los datos]
    
	Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

-   [Introducción a la autenticación][Introducción a la autenticación]
    
	Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

-   [Referencia del script del servidor de Servicios móviles][Referencia del script del servidor de Servicios móviles]
    Obtenga más información acerca del registro y uso de scripts de servidor.

-   [Referencia conceptual de Servicios móviles con .NET][Referencia conceptual de Servicios móviles con .NET]
    
	Obtenga más información sobre cómo utilizar Servicios móviles con .NET.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/
  [Creación de la tabla Registros]: #create-table
  [Incorporación de notificaciones de inserción a la aplicación]: #add-push
  [Actualización de scripts para enviar notificaciones de inserción]: #update-scripts
  [Inserción de datos para recibir notificaciones]: #test
  [Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-wp8
  [Introducción a los Centros de notificaciones]: /es-es/manage/services/notification-hubs/getting-started-windows-dotnet/
  [mobile-services-create-new-push-table]: ../includes/mobile-services-create-new-push-table.md
  [0]: ./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
  [mobile-services-update-registrations-script]: ../includes/mobile-services-update-registrations-script.md
  [1]: ./media/mobile-services-windows-phone-get-started-push/mobile-insert-script-push2.png
  [2]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
  [3]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
  [4]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
  [5]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
  [6]: /es-es/manage/services/notification-hubs/get-started-notification-hubs-wp8/
  [Envío de notificaciones a los suscriptores]: /es-es/manage/services/notification-hubs/breaking-news-wp8/
  [Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-wp8
  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-wp8
  [Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Referencia conceptual de Servicios móviles con .NET]: /es-es/develop/mobile/how-to-guides/work-with-net-client-library/
