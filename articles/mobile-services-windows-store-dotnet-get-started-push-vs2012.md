<properties linkid="develop-mobile-tutorials-get-started-with-push-dotnet" urlDisplayName="Get Started with Push Notifications" pageTitle="Get started with push notifications - Mobile Services" metaKeywords="push notifications c#" description="Learn how to use push notifications with Azure Mobile Services." metaCanonical="http://www.windowsazure.com/es-es/develop/mobile/tutorials/get-started-with-push-dotnet/" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services using Visual Studio 2012" authors="" />

Introducción a las notificaciones de inserción en Servicios móviles con Visual Studio 2012
==========================================================================================

[C\# para Tienda Windows](/es-es/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012 "C# para Tienda Windows") [JavaScript para Tienda Windows](/es-es/develop/mobile/tutorials/get-started-with-push-js-vs2012 "JavaScript para Tienda Windows") [Windows Phone](/es-es/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone") [iOS](/es-es/develop/mobile/tutorials/get-started-with-push-ios "iOS") [Android](/es-es/develop/mobile/tutorials/get-started-with-push-android "Android") [Xamarin.iOS](/es-es/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS") [Xamarin.Android](/es-es/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

Este tema muestra cómo puede usar Servicios móviles de Azure para enviar notificaciones de inserción a una aplicación de la Tienda Windows. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con el servicio de notificaciones de inserción de Windows (WNS). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

**Nota:**

Este tutorial agrega notificaciones de inserción a una aplicación de la Tienda Windows creadas en Visual Studio 2012. Visual Studio 2013 incluye nuevas características que facilitan la configuración de notificaciones de inserción en su aplicación de la Tienda Windows con Servicios móviles. Para obtener la versión de Visual Studio 2013, consulte [Introducción a las notificaciones de inserción](/es-es/develop/mobile/tutorials/get-started-with-push-dotnet).

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Registro de la aplicación para notificaciones de inserción y configuración de Servicios móviles](#register)
2.  [Creación de la tabla Registros](#create-table)
3.  [Incorporación de notificaciones de inserción a la aplicación](#add-push)
4.  [Actualización de scripts para enviar notificaciones de inserción](#update-scripts)
5.  [Inserción de datos para recibir notificaciones](#test)

Este tutorial requiere lo siguiente:

-   Microsoft Visual Studio 2012 Express para Windows 8
-   Activación de la cuenta de la Tienda Windows

Este tutorial se basa en el tutorial [Introducción a los datos](/es-es/develop/mobile/tutorials/get-started-with-data-dotnet). Antes de empezar este tutorial, primero debe completar [este tutorial](/es-es/develop/mobile/tutorials/get-started-with-data-dotnet).

Registro de la aplicaciónRegistro de la aplicación para la Tienda Windows
-------------------------------------------------------------------------

Para poder enviar notificaciones de inserción a las aplicaciones de la Tienda Windows desde Servicios móviles, debe enviar su aplicación a la Tienda Windows. A continuación, debe configurar el servicio móvil para que se integre con WNS.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

El servicio móvil y la aplicación están ahora configurados para funcionar con WNS. A continuación, creará una tabla para almacenar los registros.

Creación de una tabla
---------------------

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

Incorporación de notificaciones de inserciónIncorporación de notificaciones de inserción a la aplicación
--------------------------------------------------------------------------------------------------------

1.  Abra el archivo de proyecto MainPage.xaml.cs y agregue el código siguiente que crea una nueva clase **Registrations**:

         public class Registrations
         {
             public string Id { get; set; }

             [JsonProperty(PropertyName = "handle")]
             public string Handle { get; set; }
         }

    La propiedad Handle se utiliza para almacenar el URI de canal.

2.  Abra el archivo App.xaml.cs y agregue la siguiente instrucción using:

         using Windows.Networking.PushNotifications;

3.  Agregue lo siguiente a App.xaml.cs:

         private async void AcquirePushChannel()
         {
            CurrentChannel = 
                await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
            var registration = new Registrations { Handle = CurrentChannel.Uri };
            await registrationsTable.InsertAsync(registration);
         }

    Este código inserta el canal actual en la tabla Registrations.

4.  En la parte superior del controlador de eventos **OnLaunched** en App.xaml.cs, agregue la siguiente llamada al nuevo método **AcquirePushChannel**:

         AcquirePushChannel();

    Esto garantiza que la propiedad **CurrentChannel** se inicialice cada vez que se inicia la aplicación.

5.  Abra el archivo Package.appxmanifest y asegúrese de que en la pestaña **IU de la aplicación**, la opción **Capacidad de aviso** esté definida en **Sí**.

  ![][15]

  Esto asegura que la aplicación puede generar notificaciones del sistema. 

Actualización del script de inserciónActualización de scripts de inserción registrados en el Portal de administración
---------------------------------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

1.  Haga clic en **TodoItem**, en **Script** y seleccione **Insert**.

  ![][5]

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

Prueba de la aplicaciónPruebas de notificaciones de inserción en su aplicación
------------------------------------------------------------------------------

1.  En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

2.  En la aplicación, escriba un texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

  ![][13]

  Tenga en cuenta que una vez finalizada la inserción, la aplicación recibe una notificación de inserción de WNS.

  ![][14]

Pasos siguientes
----------------

Este tutorial demuestra la funcionalidad de notificación de inserción básica que brinda Servicios móviles. Si su aplicación requiere funcionalidades más avanzadas, como enviar notificaciones entre plataformas, enrutamiento basado en suscripción o volúmenes de tamaño muy grande, considere utilizar Centros de notificaciones de Azure con su servicio móvil. Para obtener más información, consulte uno de los siguientes temas de Centros de notificaciones:

-   [Introducción a los Centros de notificaciones](/es-es/manage/services/notification-hubs/getting-started-windows-dotnet/)
	<br/>Aprenda a aprovechar Centros de notificaciones en su aplicación de la Tienda Windows.

-   [¿Qué son los Centros de notificaciones?](/es-es/develop/net/how-to-guides/service-bus-notification-hubs/)
	<br/>Aprenda a crear e insertar notificaciones para usuarios en varias plataformas.

-   [Envío de notificaciones a los suscriptores](/es-es/manage/services/notification-hubs/breaking-news-dotnet/)
	<br/>Sepa cómo los usuarios se pueden registrar y recibir notificaciones de inserción de categorías que les interesan.

-   [Envío de notificaciones a los usuarios](/es-es/manage/services/notification-hubs/notify-users/)
	<br/>Sepa cómo enviar notificaciones de inserción desde un Servicio móvil a usuarios específicos en cualquier dispositivo.

-   [Envío de notificaciones entre plataformas a los usuarios](/es-es/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
	<br/>Sepa cómo utilizar las plantillas para enviar notificaciones de inserción desde un Servicio móvil sin tener que diseñar cargas específicas de plataforma en el back-end.

Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a los datos](/es-es/develop/mobile/tutorials/get-started-with-data-dotnet)
	<br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

-   [Introducción a la autenticación](/es-es/develop/mobile/tutorials/get-started-with-users-dotnet)
	<br/>Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

-   [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/?LinkId=262293)
	<br/>Obtenga más información acerca del registro y uso de scripts de servidor.

-   [Referencia conceptual de Servicios móviles con .NET](/es-es/develop/mobile/how-to-guides/work-with-net-client-library/)
	<br/>Obtenga más información sobre cómo utilizar Servicios móviles con .NET.


<!-- Images. -->





[5]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-insert-script-push2.png







[13]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-quickstart-push2.png
[15]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-app-enable-toast-win8.png