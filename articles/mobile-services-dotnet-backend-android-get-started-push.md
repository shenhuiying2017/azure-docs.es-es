<properties urlDisplayName="Get Started with Push" pageTitle="Introducción a la inserción (Android) | Centro de desarrollo móvil" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android .Net app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/26/2014" ms.author="ricksal" />

# Incorporación de notificaciones de inserción a la aplicación de Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

En este tema se muestra cómo usar Servicios móviles de Azure para enviar notificaciones de inserción a la aplicación Android. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con el servicio de mensajería en la nube de Google (GCM). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro. 

Este tutorial le guiará a través de estos pasos:

1. [Habilitación del servicio de mensajería en la nube de Google](#register)
2. [Configuración del servicio móvil para enviar solicitudes de inserción](#configure)
5. [Actualización del servidor para enviar notificaciones de inserción](#update-server)
7. [Incorporación de notificaciones de inserción a la aplicación](#update-app)
8. [Habilitar notificaciones de inserción para pruebas locales](#local-testing)
9. [Prueba de la aplicación con el servicio móvil publicado]


Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de empezar este tutorial, primero debe completar [Introducción a Servicios móviles] o [Introducción a los datos] para conectar su proyecto al servicio móvil. Por este motivo, este tutorial requiere también Visual Studio 2013. 

>[WACOM.NOTE]Para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Evaluación gratuita de Azure</a>. 


##<a id="register"></a>Habilitación del servicio de mensajería en la nube de Google

[WACOM.INCLUDE [Habilitación de GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]


##<a id="configure"></a>Configuración de Servicios móviles para enviar solicitudes de inserción

1. Inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y luego en su aplicación.

   	![](./media/mobile-services-android-get-started-push/mobile-services-selection.png)

2. Haga clic en la pestaña **Inserción**, escriba el valor de la **Clave de API** obtenido de GCM en el procedimiento anterior y haga clic en **Guardar**.

   	![](./media/mobile-services-android-get-started-push/mobile-push-tab-android.png)

    <div class="dev-callout"><b>Importante</b>
	<p>Al configurar las credenciales de GCM para las notificaciones de inserción mejoradas en la pestaña Push del portal, se comparten con los Centros de notificaciones para configurar el centro de notificaciones con la aplicación.</p>
    </div>


Su servicio móvil ahora está configurado para trabajar con GCM y Centros de notificaciones.


<h2><a name="download-the-service"></a>Descarga del servicio en el equipo local</h2>

[WACOM.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

<h2><a name="test-the-service"></a>Prueba del servicio móvil</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a id="update-server"></a>Actualización del servidor para enviar notificaciones de inserción

1. En el Explorador de soluciones de Visual Studio, expanda la carpeta **Controllers** en el proyecto de servicio móvil. Abra TodoItemController.cs. En la parte superior del archivo, agregue las siguientes instrucciones `using`:


		using System;
		using System.Collections.Generic;

2. Actualice la definición del método `PostTodoItem` con el siguiente código:  

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


<h2><a name="publish-the-service"></a>Publicar el servicio móvil en Azure</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="update-app"></a>Incorporación de notificaciones de inserción a la aplicación

###Comprobación de la versión del SDK de Android

[WACOM.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version.md)]


El siguiente paso es instalar los servicios de Google Play. El servicio de mensajería en la nube de Google tiene algunos requisitos mínimos en el nivel de API para desarrollo y prueba, a los que debe ajustarse la propiedad **minSdkVersion** del manifiesto. 

Si va a realizar pruebas con un dispositivo antiguo, consulte [Configuración del SDK de Google Play Services] para determinar el valor mínimo que puede configurar, y cómo configurarlo de forma adecuada.

###Incorporación de Google Play Services al proyecto

[WACOM.INCLUDE [Incorporación de servicios de reproducción](../includes/mobile-services-add-Google-play-services.md)]

###Incorporación de código

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push.md)]

<h2><a name="test-app"></a>Prueba de la aplicación con el servicio móvil publicado</h2>

Puede probar la aplicación conectando directamente un teléfono Android con un cable USB o utilizando un dispositivo virtual en el emulador.

###Si va a usar el emulador para las pruebas...

Asegúrese de que usa un dispositivo virtual de Android (AVD) que sea compatible con API de Google.

1. En **Ventana**, seleccione **Administrador de dispositivos virtuales Android**, seleccione el dispositivo, haga clic en **Editar** (o en **Nuevo** si no tiene ningún dispositivo).

	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2. Seleccione **API de Google** (o **API de Google x86**) en **Destino** y luego haga clic en Aceptar.

   	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	Con esto se dirige el dispositivo virtual Android para que use las API de Google. Si tiene instaladas varias versiones del SDK de Android, asegúrese de que el nivel de API coincida con el que ha establecido anteriormente en las propiedades del proyecto.

###<a id="local-testing"></a> Habilitar notificaciones de inserción para pruebas locales

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

###Ejecución de la prueba

1. En el menú **Run (Ejecutar)** de Eclipse, haga clic en **Run (Ejecutar)** para iniciar la aplicación.

2. En la aplicación, escriba un texto significativo, como _Nueva tarea de Servicios móviles_, y haga clic en el botón **Add** (Agregar).

  	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

3. Deslice el dedo hacia abajo desde la parte superior de la pantalla para abrir el Centro de notificaciones del dispositivo y visualizar la notificación.


Ha completado correctamente este tutorial.


## <a name="next-steps"> </a>Pasos siguientes

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.


+ [Send push notifications to authenticated users]
	<br/>Aprenda a usar etiquetas para enviar notificaciones de inserción desde un servicio móvil a solo un usuario autenticado.

+ [Envío de notificaciones de difusión a suscriptores]
	<br/>Obtenga información sobre cómo los usuarios pueden registrar y recibir notificaciones de inserción para las categorías que les interesan.

+ [Envío de notificaciones basadas en plantillas a suscriptores]
	<br/>Aprenda a usar plantillas para enviar notificaciones de inserción desde un servicio móvil sin tener que diseñar cargas específicas de plataforma en el backend.
-->
Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

* [Introducción a los datos]
  <br/>Obtenga más información sobre cómo almacenar y consultar datos con Servicios móviles.

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de la aplicación con diferentes tipos de cuenta mediante Servicios móviles.

* [¿Qué son los Centros de notificaciones?]
  <br/>Obtenga más información sobre el funcionamiento de los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas cliente.

* [Depuración de aplicaciones de los Centros de notificaciones](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obtenga orientación para solucionar y depurar soluciones de Centros de notificaciones. 

* [Uso de la biblioteca de cliente Android para Servicios móviles]
  <br/>Obtenga más información sobre el uso de Servicios móviles con Android.  
  
<!-- Anchors. -->

[Creación de un servicio móvil]: #create-service
[Descarga del servicio móvil de manera local]: #download-the-service-locally
[Prueba del servicio móvil]: #test-the-service
[Descarga del proyecto GetStartedWithData]: #download-app
[Actualización de la aplicación para usar el servicio móvil para el acceso a datos]: #update-app
[Prueba de la aplicación Android con el servicio hospedado de manera local]: #test-locally-hosted
[Publicar el servicio móvil en Azure]: #publish-mobile-service
[Prueba de la aplicación Android con el servicio hospedado en Azure]: #test-azure-hosted
[Prueba de la aplicación con el servicio móvil publicado]: #test-app
[Pasos siguientes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-data-sample-download-dotnet-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-run-solution.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/new-local-todoitem.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-show-local-table-data.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/local-item-checked.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png

[20]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-service-and-packages-folder.png


<!-- URLs. -->
[Validación y modificación de datos con scripts]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-dotnet
[Introducción a Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[JavaScript y HTML]: /es-es/develop/mobile/tutorials/get-started-with-data-js
[Versión de back-end de JavaScript]: /es-es/develop/mobile/tutorials/get-started-with-data-android
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Portal de administración]: https://manage.windowsazure.com/
[SDK de Servicios móviles]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Sitio de ejemplos de código para desarrolladores]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Referencia conceptual de Servicios móviles con .NET]: /es-es/develop/mobile/how-to-guides/work-with-net-client-library
[Clase MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030

[Uso de la biblioteca de cliente Android para Servicios móviles]: /es-es/documentation/articles/mobile-services-android-how-to-use-client-library

[Envío de notificaciones de inserción a usuarios autenticados]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users/

[¿Qué son los Centros de notificaciones?]: /es-es/documentation/articles/notification-hubs-overview/
[Envío de notificaciones de difusión a suscriptores]: /es-es/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Envío de notificaciones basadas en plantillas a suscriptores]: /es-es/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/
[Portal de administración de Azure]: https://manage.windowsazure.com/
