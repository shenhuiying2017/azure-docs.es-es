<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-push-android" urlDisplayName="Get Started with Push" pageTitle="Get started with push (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android .Net app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Introducción a las notificaciones de inserción en Servicios móviles

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="C# para Tienda Windows">C# para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Windows Phone">Windows Phone</a><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="iOS">iOS</a><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Android" class="current">Android</a>
<!---<a href="/es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Back-end de .NET" class="current">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-javascript-backend-android-get-started-push/"  title="Back-end de JavaScript">Back-end de JavaScript</a></div>

Este tema muestra cómo puede utilizar Servicios móviles de Azure para enviar notificaciones de inserción a su aplicación Android. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con el servicio de mensajería en la nube de Google (GCM). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

Este tutorial le guiará a través de estos pasos:

1.  [Habilitación del servicio de mensajería en la nube de Google][]
2.  [Configuración del servicio móvil para enviar solicitudes de inserción][]
3.  [Actualización del servidor para enviar notificaciones de inserción][]
4.  [Incorporación de notificaciones de inserción a la aplicación][]
5.  [Habilitación de notificaciones de inserción para pruebas locales][]
6.  [Prueba de la aplicación con el servicio móvil publicado][]

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de empezar este tutorial, primero debe completar [Introducción a los Servicios móviles][] o [Introducción a los datos][] para conectar su proyecto al servicio móvil. Por este motivo, este tutorial requiere también Visual Studio 2013.

> [WACOM.NOTE] Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][].

## <span id="register"></span></a>Habilitación del servicio de mensajería en la nube de Google

[WACOM.INCLUDE [Habilitación de GCM][]]

## <span id="configure"></span></a>Configuración de Servicios móviles para enviar solicitudes de inserción

1.  Inicie sesión en el [Portal de administración de Azure][], haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación.

    ![][]

2.  Haga clic en la pestaña **Inserción**, escriba el valor de la **Clave de API** obtenido de GCM en el procedimiento anterior y, a continuación, haga clic en **Guardar**.

    ![][1]

    <div class="dev-callout"><b>Importante</b>
<p>Al configurar las credenciales de GCM para las notificaciones de inserci&oacute;n mejoradas en la pesta&ntilde;a Push del portal, se comparten con los Centros de notificaciones para configurar el centro de notificaciones con la aplicaci&oacute;n.</p>
</div>

Su servicio móvil ahora está configurado para trabajar con GCM y Centros de notificaciones.

## <a name="download-the-service"></a><span class="short-header">Descarga del servicio</span>Descarga del servicio en el equipo local

[WACOM.INCLUDE [mobile-services-download-service-locally][]]

## <a name="test-the-service"></a><span class="short-header">Prueba del servicio</span>Prueba del servicio móvil

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][]]

## <span id="update-server"></span></a> Actualización del servidor para enviar notificaciones de inserción

1.  En el Explorador de soluciones de Visual Studio, muestre la carpeta **Controladores** en el proyecto de servicio móvil. Abra TodoItemController.cs. En la parte superior del archivo, agregue las siguientes instrucciones `using`:

        using System;
        using System.Collections.Generic;

2.  Actualice la definición de métoco `PostTodoItem` con el siguiente código:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };
            GooglePushMessage message = new GooglePushMessage(data, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Este código enviará una notificación de inserción (con el texto del elemento insertado) tras insertar un elemento todo. En caso de error, el código agregará una entrada al registro de errores que aparecerá en la pestaña **Registros** del servicio móvil en el Portal de administración.

## <a name="publish-the-service"></a><span class="short-header">Publicación del servicio</span>Publicación del servicio móvil en Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][]]

## <a name="update-app"></a>Incorporación de notificaciones de inserción a la aplicación

### Comprobación de la versión del SDK de Android

[WACOM.INCLUDE [mobile-services-verify-android-sdk-version][]]

El siguiente paso es instalar los servicios de Google Play. El servicio de mensajería en la nube de Google tiene algunos requisitos mínimos en el nivel de API para desarrollo y prueba, los que debe cumplir la propiedad **minSdkVersion** del manifiesto.

Si va a realizar pruebas con un dispositivo antiguo, consulte [Configuración del SDK de Google Play Services] para determinar el valor mínimo que puede configurar, y cómo configurarlo de forma adecuada.

### Incorporación de Google Play Services al proyecto

[WACOM.INCLUDE [Incorporación de Play Services][]]

### Incorporación de código

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push][]]

## <a name="test-app"></a><span class="short-header">Prueba de la aplicación</span>Prueba de la aplicación con el servicio móvil publicado

Puede probar la aplicación conectando directamente un teléfono Android con un cable USB o utilizando un dispositivo virtual en el emulador.

### Si va a usar el emulador para las pruebas...

Asegúrese de que usa un dispositivo virtual de Android (AVD) que sea compatible con API de Google.

1.  En **Ventana**, seleccione **Administrador de dispositivos virtuales**, seleccione su dispositivo, haga clic en **Editar** (o en **Nuevo** si no tiene ningún dispositivo).

    ![][2]

2.  Seleccione **API de Google** (o **API de Google x86**) en **Destino** y luego haga clic en Aceptar.

    ![][3]

    Con esto, el dispositivo virtual Android se dirige para usar las API de Google. Si tiene instaladas varias versiones del SDK de Android, asegúrese de que el nivel de API coincida con el que ha establecido anteriormente en las propiedades del proyecto.

### <span id="local-testing"></span></a>Habilitación de notificaciones de inserción para pruebas locales

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push][]]

### Ejecución de la prueba

1.  En el menú **Run** en Eclipse, haga clic en **Run** para iniciar la aplicación.

2.  En la aplicación, escriba un texto significativo, como *Una nueva tarea de Servicios móviles* y, a continuación, haga clic en el botón **Agregar**.

    ![][4]

3.  Deslice el dedo hacia abajo desde la parte superior de la pantalla para abrir el Centro de notificaciones del dispositivo y visualizar la notificación.

Ha completado correctamente este tutorial.

## <a name="next-steps"> </a>Pasos siguientes

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.   + [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

-   [Introducción a los datos][]
    <br/>Obtenga más información sobre cómo almacenar y consultar datos con los servicios móviles.

-   [Introducción a la autenticación][]
    <br/>Aprenda a autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los servicios móviles.

-   [¿Qué son los Centros de notificaciones?][]
    <br/>Aprenda cómo funcionan los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas cliente.

-   [Uso de la biblioteca de cliente Android para Servicios móviles][]
    <br/>Obtenga más información sobre cómo usar Servicios móviles con Android.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [C# para Tienda Windows]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/ "C# para Tienda Windows"
  [JavaScript para Tienda Windows]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/ "JavaScript para Tienda Windows"
  [Windows Phone]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ "Windows Phone"
  [iOS]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ "iOS"
  [Android]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ "Android"
  [Back-end de .NET]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ "Back-end de .NET"
  [Back-end de JavaScript]: /es-es/documentation/articles/mobile-services-javascript-backend-android-get-started-push/ "Back-end de JavaScript"
  [Habilitación del servicio de mensajería en la nube de Google]: #register
  [Configuración del servicio móvil para enviar solicitudes de inserción]: #configure
  [Actualización del servidor para enviar notificaciones de inserción]: #update-server
  [Incorporación de notificaciones de inserción a la aplicación]: #update
  [Habilitación de notificaciones de inserción para pruebas locales]: #local-testing
  [Prueba de la aplicación con el servicio móvil publicado]: #test-app
  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F
  [Habilitación de GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
  [1]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
  [mobile-services-download-service-locally]: ../includes/mobile-services-download-service-locally.md
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-verify-android-sdk-version]: ../includes/mobile-services-verify-android-sdk-version.md
  [Incorporación de Play Services]: ../includes/mobile-services-add-Google-play-services.md
  [mobile-services-android-getting-started-with-push]: ../includes/mobile-services-android-getting-started-with-push.md
  [2]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
  [3]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [4]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
  [¿Qué son los Centros de notificaciones?]: /es-es/documentation/articles/notification-hubs-overview/
  [Uso de la biblioteca de cliente Android para Servicios móviles]: /es-es/documentation/articles/mobile-services-android-how-to-use-client-library
