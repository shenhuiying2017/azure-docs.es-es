<properties pageTitle="Get started with push notifications (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Phone app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor="" />


Introducción a las notificaciones de inserción en Servicios móviles
===================================================================

[C\# para Tienda Windows](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "C# para Tienda Windows")[JavaScript para Tienda Windows](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-push "JavaScript para Tienda Windows")[Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-ios-get-started-push "iOS")[Android](/en-us/documentation/articles/mobile-services-android-get-started-push "Android")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started-push "Xamarin.Android")

[Back-end de .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ "Back-end de .NET") | [Back-end de JavaScript](/en-us/documentation/articles/mobile-services-windows-phone-get-started-push/ "Back-end de JavaScript")

Este tema muestra cómo puede utilizar Servicios móviles de Azure para enviar notificaciones de inserción a una aplicación de Windows Phone 8. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con el servicio de notificaciones de inserción de Microsoft (MPNS). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

> [WACOM.NOTE] Servicios móviles ahora se integra con Centros de notificaciones de Azure para ofrecer compatibilidad con la funcionalidad de notificación de inserción adicional, como plantillas, varias plataformas y escala. Esta funcionalidad integrada actualmente está en su versión preliminar. Para obtener más información, consulte esta versión de [Introducción a las notificaciones de inserción](/en-us/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/).

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Creación de la tabla Registros](#create-table)
2.  [Incorporación de notificaciones de inserción a la aplicación](#add-push)
3.  [Actualización de scripts para enviar notificaciones de inserción](#update-scripts)
4.  [Inserción de datos para recibir notificaciones](#test)

Este tutorial requiere [Visual Studio 2012 Express para Windows Phone](https://go.microsoft.com/fwLink/p/?LinkID=268374) o una versión posterior.

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started-wp8).

> [WACOM.NOTE]Cuando envíe más de 500 mensajes por usuario al día, debe utilizar Centros de notificaciones. Para obtener más información, consulte [Introducción a los Centros de notificaciones](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/).

Creación de una tabla
---------------------

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

Incorporación de notificaciones de inserciónIncorporación de notificaciones de inserción a la aplicación
--------------------------------------------------------------------------------------------------------

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

         private void AcquirePushChannel()
         {
             CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

             if (CurrentChannel == null)
             {
                 CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                 CurrentChannel.Open();
                 CurrentChannel.BindToShellTile();
             }
                      
            IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
            var registration = new Registrations { Handle = CurrentChannel.Uri };
            await registrationsTable.InsertAsync(registration);
         }

        Este código adquiere y almacena un canal para una suscripción a notificaciones e inserción y lo enlaza al icono predeterminado de la aplicación.

    **Nota:**

    En este tutorial, el servicio móvil envía una notificación de icono dinámico al dispositivo. Cuando envía una notificación del sistema, debe llamar al método **BindToShellToast** en el canal. Para ser compatible con notificaciones del sistema y notificaciones de icono, llame a ambos métodos, **BindToShellTile** y **BindToShellToast**.

4.  En la parte superior del controlador de eventos **Application\_Launching** en App.xaml.cs, agregue la siguiente llamada al nuevo método **AcquirePushChannel**:

         AcquirePushChannel();

        Esto garantiza que la propiedad **CurrentChannel** se inicialice cada vez que se inicia la aplicación.

5.  En el Explorador de soluciones, expanda **Propiedades**, abra el archivo WMAppManifest.xml, haga clic en la pestaña **Funcionalidades** y asegúrese de que la funcionalidad **ID\_\_*CAP*\_\_PUSH\_NOTIFICATION** esté marcada.

	![](./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png)

Esto asegura que la aplicación puede recibir notificaciones de inserción.

Actualización del script de inserciónActualización de scripts de inserción registrados en el Portal de administración
---------------------------------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

1.  Haga clic en **TodoItem**, en **Script** y seleccione **Insert**.

	![][10]

2.  Sustituya la función de inserción por el siguiente código y, a continuación, haga clic en **Save**:

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
                            push.mpns.sendFlipTile(registration.uri, {
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

Prueba de la aplicaciónPruebas de notificaciones de inserción en su aplicación
------------------------------------------------------------------------------

1.  En Visual Studio, seleccione **Implementar solución** en el menú **Compilar**.

2.  En el emulador, deslícese rápidamente a la izquierda para que aparezca la lista de aplicaciones instaladas y busque la nueva aplicación **TodoList**.

3.  Mantenga pulsado el icono de la aplicación y, a continuación, seleccione **anclar a inicio** en el menú contextual.

	![](./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png)

	Esta acción ancla un icono llamado **TodoList** al menú de inicio.

1.  Toque el icono llamado **TodoList** para iniciar la aplicación.

	![](./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png)

1.  En la aplicación, escriba el texto "hello push" en el cuadro de texto y, a continuación, haga clic en **Guardar**.

	![][4]

	Esto envía una solicitud de inserción al servicio móvil para almacenar el elemento agregado.

1.  Pulse el botón **Inicio** para volver al menú de inicio.

	![](./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png)

	Observe que la aplicación recibió la notificación de inserción y que el título del icono ahora es **hello push**.

Pasos siguientes
----------------

Este tutorial demuestra la funcionalidad de notificación de inserción básica que brinda Servicios móviles. Si su aplicación requiere funcionalidades más avanzadas, como enviar notificaciones entre plataformas, enrutamiento basado en suscripción o volúmenes de tamaño muy grande, considere utilizar Centros de notificaciones de Azure con su servicio móvil. Para obtener más información, consulte uno de los siguientes temas de Centros de notificaciones:

-   [Introducción a los Centros de notificaciones](/en-us/manage/services/notification-hubs/get-started-notification-hubs-wp8/)
    Aprenda a aprovechar Centros de notificaciones en su aplicación de la Tienda Windows.

-   [¿Qué son los Centros de notificaciones?](/en-us/develop/net/how-to-guides/service-bus-notification-hubs/)
    Aprenda a crear e insertar notificaciones para usuarios en varias plataformas.

-   [Envío de notificaciones a los suscriptores](/en-us/manage/services/notification-hubs/breaking-news-wp8/)
    Sepa cómo los usuarios se pueden registrar y recibir notificaciones de inserción de categorías que les interesan.

Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-wp8)
    Obtenga más información sobre almacenar y consultar datos con los Servicios móviles.

-   [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-wp8)
    Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

-   [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/?LinkId=262293)
    Obtenga más información acerca del registro y uso de scripts de servidor.

-   [Referencia conceptual de Servicios móviles con .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library/)
    Obtenga más información sobre cómo utilizar Servicios móviles con .NET.



<!-- Anchors. -->
[Create the Registrations table]: #create-table
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test
[Next Steps]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[10]: ./media/mobile-services-windows-phone-get-started-push/mobile-insert-script-push2.png



<!-- URLs. -->
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=268375
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-wp8
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-wp8
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-wp8
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-wp8
[Push notifications to app users]: /en-us/develop/mobile/tutorials/push-notifications-to-users-wp8
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Azure Management Portal]: https://manage.windowsazure.com/
[mpns object]: http://go.microsoft.com/fwlink/p/?LinkId=271130
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services .NET How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library/
[Get started with Notification Hubs]: /en-us/manage/services/notification-hubs/get-started-notification-hubs-wp8/
[What are Notification Hubs?]: /en-us/develop/net/how-to-guides/service-bus-notification-hubs/
[Send notifications to subscribers]: /en-us/manage/services/notification-hubs/breaking-news-wp8/
[Send notifications to users]: /en-us/manage/services/notification-hubs/notify-users/
[Send cross-platform notifications to users]: /en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/
