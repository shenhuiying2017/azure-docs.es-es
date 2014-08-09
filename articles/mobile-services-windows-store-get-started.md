<properties pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />


Introducción a los Servicios móviles
====================================

[Tienda Windows](/es-es/documentation/articles/mobile-services-windows-store-get-started "Windows Store") [Windows Phone](/es-es/documentation/articles/mobile-services-windows-phone-get-started "Windows Phone") [iOS](/es-es/documentation/articles/mobile-services-ios-get-started "iOS") [Android](/es-es/documentation/articles/mobile-services-android-get-started "Android") [HTML](/es-es/documentation/articles/mobile-services-html-get-started "HTML") [Xamarin.iOS](/es-es/documentation/articles/partner-xamarin-mobile-services-ios-get-started "Xamarin.iOS") [Xamarin.Android](/es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started "Xamarin.Android") [Sencha](/es-es/documentation/articles/partner-sencha-mobile-services-get-started/ "Sencha") [PhoneGap](/es-es/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/ "PhoneGap")

[Back-end de .NET](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/ ".NET backend") | [Back-end de JavaScript](/es-es/documentation/articles/mobile-services-windows-store-get-started/ "JavaScript backend")

En este tutorial se muestra cómo agregar un servicio de back-end basado en la nube para una aplicación de la Tienda Windows con Servicios móviles de Azure.

Si prefiere ver un vídeo, el clip que aparece a la derecha muestra los mismos pasos que este tutorial. En el vídeo, Scott Guthrie ofrece una introducción a los Servicios móviles y describe la creación de su primer servicio móvil y cómo conectar con él desde una aplicación de la Tienda Windows.

[Ver el tutorial (en inglés)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services) [Reproducir vídeo (en inglés)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services) 10:08:00

Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de *lista de pendientes* que almacena datos de aplicación en el servicio móvil nuevo. El servicio móvil que se creará utiliza JavaScript para la lógica de negocios de servidor. Si desea crear un servicio móvil que le permita escribir su lógica de negocios de servidor en los lenguajes compatibles de .NET con Visual Studio, consulte la versión de back-end de .NET de este tema.

La siguiente captura de pantalla muestra la aplicación final:

![](./media/mobile-services-windows-store-get-started/mobile-quickstart-completed.png)

Completar este tutorial es un requisito previo para todos los tutoriales de Servicios móviles para aplicaciones de la Tienda Windows.

> [WACOM.NOTE] Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F).

> Este tutorial requiere Visual Studio 2013. Para conectar una aplicación de la Tienda Windows mediante Visual Studio 2012, siga los pasos en el tema [Introducción a los datos en Servicios móviles con Visual Studio 2012](/es-es/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012/).

Creación de un servicio móvil
-----------------------------

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

Creación de una aplicación de la Tienda Windows
-----------------------------------------------

Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación o modificar una ya existente a fin de conectarla a dicho servicio.

En esta sección, creará una aplicación de Tienda Windows que se conecta al servicio móvil.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  En la pestaña de inicio rápido, haga clic en **Windows** en **Choose platform** y, a continuación, expanda **Create a new Windows Store app**.

	![][6]

	Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación de la Tienda Windows conectada al servicio móvil.

	![](./media/mobile-services-windows-store-get-started/mobile-quickstart-steps.png)

1.  Si todavía no lo ha hecho, descargue e instale [Visual Studio 2013 Express para Windows](http://go.microsoft.com/fwlink/?LinkId=257546) en el equipo local o máquina virtual.

2.  Haga clic en **Create TodoItem table** para crear una tabla donde almacenar datos de la aplicación.

3.  En **Download and run your app**, seleccione un idioma para la aplicación y, a continuación, haga clic en **Download**.

De este modo se descarga el proyecto para la aplicación de *lista de pendientes* de muestra que está conectada al servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

Ejecución de la aplicación de Windows
-------------------------------------

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1.  Vaya a la ubicación donde guardó los archivos comprimidos del proyecto, expándalos en su equipo y abra el archivo de solución en Visual Studio 2013 Express para Windows.

2.  Pulse la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

3.  En la aplicación, escriba un texto significativo, como *Realice el tutorial*, en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

	![][10]

	Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y se muestran los datos en la segunda columna de la aplicación.

    > [WACOM.NOTE]Puede revisar el código que obtiene acceso al servicio móvil para consultar e insertar datos. Puede encontrarse en el archivo MainPage.xaml.cs (proyecto C\#/XAML) o en el archivo default.js (proyecto JavaScript/HTML).

4.  Nuevamente en el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItems**.

	![][11]

	Esto le permite examinar los datos que la aplicación inserta en la tabla.

	![][12]

Pasos siguientes
----------------

Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles:

-   **Introducción a los datos** ( [C\#](/es-es/develop/mobile/tutorials/get-started-with-data-dotnet) / [JavaScript](/es-es/develop/mobile/tutorials/get-started-with-data-js) )
    Obtenga más información sobre almacenar y consultar datos con los Servicios móviles.

-   **Introducción a la autenticación** ( [C\#](/es-es/develop/mobile/tutorials/get-started-with-users-dotnet) / [JavaScript](/es-es/develop/mobile/tutorials/get-started-with-users-js) )
    Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

-   **Introducción a las notificaciones de inserción** ( [C\#](/es-es/develop/mobile/tutorials/get-started-with-push-dotnet) / [JavaScript](/es-es/develop/mobile/tutorials/get-started-with-push-js) )
    Aprenda a enviar una notificación de inserción muy básica a la aplicación.


<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Create a new mobile service]:#create-new-service
[Define the mobile service instance]:#define-mobile-service-instance
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-windows-store-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-windows-store-get-started/mobile-vs2013-project.png

[10]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-startup.png
[11]: ./media/mobile-services-windows-store-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-windows-store-get-started/mobile-data-browse.png


<!-- URLs. -->
[Get started with data]: /es-es/develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /es-es/develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /es-es/develop/mobile/tutorials/get-started-with-push-dotnet
[Get started with data JS]: /es-es/develop/mobile/tutorials/get-started-with-data-js
[Get started with authentication JS]: /es-es/develop/mobile/tutorials/get-started-with-users-js
[Get started with push notifications JS]: /es-es/develop/mobile/tutorials/get-started-with-push-js
[Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[Management Portal]: https://manage.windowsazure.com/
[.NET backend version]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-get-started
[Get started with data in Mobile Services using Visual Studio 2012]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012