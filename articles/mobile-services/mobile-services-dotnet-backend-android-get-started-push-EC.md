<properties 
	pageTitle="Introducción a la inserción (Android) | Microsoft Azure" 
	description="Obtenga información acerca de cómo usar Servicios móviles de Azure para enviar notificaciones de inserción a su aplicación .Net Android." 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="08/08/2015" 
	ms.author="ricksal"/>

# Incorporación de notificaciones de inserción a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push-EC.md)]

##Información general

En este tema se muestra cómo usar los Servicios móviles de Azure para enviar notificaciones de inserción a la aplicación Android. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con el servicio de mensajería en la nube de Google (GCM). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de empezar este tutorial, primero debe completar [Introducción a Servicios móviles] o [Incorporación de Servicios móviles a una aplicación existente] para conectar su proyecto al servicio móvil. Por este motivo, este tutorial requiere también Visual Studio 2013.

##<a id="register"></a>Habilitación del servicio de mensajería en la nube de Google

[AZURE.INCLUDE [Habilitación de GCM](../../includes/mobile-services-enable-Google-cloud-messaging.md)]


##<a id="configure"></a>Configuración de Servicios móviles para enviar solicitudes de inserción

1. Inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en la aplicación.

   	![](./media/mobile-services-dotnet-backend-android-get-started-push-EC/mobile-services-selection.png)

2. Haga clic en la pestaña **Inserción** y escriba el valor de la **Clave de API** obtenido de GCM en el procedimiento anterior, luego haga clic en **Guardar**.

   	![](./media/mobile-services-dotnet-backend-android-get-started-push-EC/mobile-push-tab-android.png)

> [AZURE.IMPORTANT]Al configurar las credenciales de GCM para las notificaciones de inserción mejoradas en la pestaña Push del portal, se comparten con los Centros de notificaciones para configurar el centro de notificaciones con la aplicación.


Su servicio móvil ahora está configurado para trabajar con GCM y Centros de notificaciones.


##<a name="download-the-service"></a>Descarga del servicio de forma local

[AZURE.INCLUDE [mobile-services-download-service-locally](../../includes/mobile-services-download-service-locally.md)]

##<a name="test-the-service"></a>Prueba del servicio móvil

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a id="update-server"></a>Actualización del servidor para enviar notificaciones de inserción

1. En el Explorador de soluciones de Visual Studio, muestre la carpeta **Controladores** en el proyecto de servicio móvil. Abra TodoItemController.cs. Al principio del archivo, agregue las siguientes instrucciones `using`:


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


##<a name="publish-the-service"></a>Publicación del servicio móvil en Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="update-app"></a>Incorporación de notificaciones de inserción a la aplicación

###Comprobación de la versión del SDK de Android

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../../includes/mobile-services-verify-android-sdk-version-EC.md)]


El siguiente paso es instalar los servicios de Google Play. El servicio de mensajería en la nube de Google tiene algunos requisitos mínimos en el nivel de API para desarrollo y prueba, que debe cumplir la propiedad **minSdkVersion** del manifiesto.

Si va a realizar pruebas con un dispositivo antiguo, consulte [Configuración del SDK de Google Play Services] para determinar el valor mínimo que puede configurar, y cómo configurarlo de forma adecuada.

###Incorporación de Google Play Services al proyecto

[AZURE.INCLUDE [Incorporación de Play Services](../../includes/mobile-services-add-Google-play-services-EC.md)]

###Incorporación de código

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push-EC.md)]

##<a name="test-app"></a>Prueba de la aplicación con el servicio móvil publicado

Puede probar la aplicación conectando directamente un teléfono Android con un cable USB o utilizando un dispositivo virtual en el emulador.

###Si va a usar el emulador para las pruebas...

Asegúrese de que usa un dispositivo virtual de Android (AVD) que sea compatible con API de Google.

1. En **Ventana**, seleccione **Administrador de dispositivos virtuales de Android**, seleccione su dispositivo, haga clic en **Editar** (o en **Nuevo** si no tiene ningún dispositivo).

	![](./media/mobile-services-dotnet-backend-android-get-started-push-EC/mobile-services-android-virtual-device-manager.png)

2. Seleccione **API de Google** (o **API de Google x86**) en **Destino** y luego haga clic en Aceptar.

   	![](./media/mobile-services-dotnet-backend-android-get-started-push-EC/mobile-services-android-virtual-device-manager-edit.png)

	Con esto se dirige el dispositivo virtual Android para que use las API de Google. Si tiene instaladas varias versiones del SDK de Android, asegúrese de que el nivel de API coincida con el que ha establecido anteriormente en las propiedades del proyecto.

###<a id="local-testing"></a> Habilitación de notificaciones de inserción para pruebas locales

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]

###Ejecución de la prueba

1. En el menú **Run** en Eclipse, haga clic en **Run** para iniciar la aplicación.

2. En la aplicación, escriba texto significativo como _A new Mobile Services task_ y, a continuación, haga clic en el botón **Add** (Agregar).

  	![](./media/mobile-services-dotnet-backend-android-get-started-push-EC/mobile-quickstart-push1-android.png)

3. Deslice el dedo hacia abajo desde la parte superior de la pantalla para abrir el Centro de notificaciones del dispositivo y visualizar la notificación.


Ha completado correctamente este tutorial.


## <a name="next-steps"> </a>Pasos siguientes

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.


+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->
Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

* [Introducción a los datos] <br/>Obtenga más información sobre cómo almacenar y consultar datos con Servicios móviles.

* [Introducción a la autenticación] <br/>Aprenda a autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los servicios móviles.

* [¿Qué son los Centros de notificaciones?] <br/>Obtenga más información sobre el funcionamiento de los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas de cliente.

* [Depuración de aplicaciones de centros de notificaciones](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Obtenga instrucciones de resolución de problemas y depuración de soluciones de centros de notificaciones.

* [Uso de la biblioteca de cliente Android para Servicios móviles] <br/>Obtenga más información sobre cómo usar Servicios móviles con Android.
  
<!-- Anchors. -->

[Create a new mobile service]: #create-service
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Download the GetStartedWithData project]: #download-app
[Update the app to use the mobile service for data access]: #update-app
[Test the Android App against the service hosted locally]: #test-locally-hosted
[Publish the mobile service to Azure]: #publish-mobile-service
[Test the Android App against the service hosted in Azure]: #test-azure-hosted
[Test the app against the published mobile service]: #test-app
[Next Steps]: #next-steps

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
[Introducción a Servicios móviles]: mobile-services-dotnet-backend-android-get-started.md
[Incorporación de Servicios móviles a una aplicación existente]: mobile-services-dotnet-backend-android-get-started-data.md
[Introducción a la autenticación]: mobile-services-dotnet-backend-android-get-started-users.md
[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=328660

[Uso de la biblioteca de cliente Android para Servicios móviles]: mobile-services-android-how-to-use-client-library.md

[Send push notifications to authenticated users]: mobile-services-dotnet-backend-android-push-notifications-app-users.md

[¿Qué son los Centros de notificaciones?]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-windows-store-dotnet-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-windows-store-dotnet-send-localized-breaking-news.md
[Portal de administración de Azure]: https://manage.windowsazure.com/
 

<!---HONumber=August15_HO7-->