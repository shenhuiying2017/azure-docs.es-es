<properties pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />


Introducción a los Servicios móviles
====================================

[Tienda Windows](/en-us/documentation/articles/mobile-services-windows-store-get-started "Tienda Windows")[Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-get-started "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-ios-get-started "iOS")[Android](/en-us/documentation/articles/mobile-services-android-get-started "Android")[HTML](/en-us/documentation/articles/mobile-services-html-get-started "HTML")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started "Xamarin.Android")[Sencha](/en-us/documentation/articles/partner-sencha-mobile-services-get-started/ "Sencha")[PhoneGap](/en-us/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/ "PhoneGap")

[Back-end de .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/ "Back-end de .NET") | [Back-end de JavaScript](/en-us/documentation/articles/mobile-services-windows-phone-get-started/ "Back-end de JavaScript")

En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación de Windows Phone 8 con los Servicios móviles de Azure. Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de *lista de pendientes* que almacena datos de aplicación en el servicio móvil nuevo.

Si prefiere ver un vídeo, el clip que aparece a la derecha muestra los mismos pasos que este tutorial. En el vídeo, Nick Harris ofrece una introducción a los Servicios móviles y describe la creación de su primer servicio móvil y cómo conectar con él desde una aplicación de la Tienda Windows.

[Ver el tutorial (en inglés)](http://go.microsoft.com/fwlink/?LinkId=290816) [Reproducir vídeo (en inglés)](http://go.microsoft.com/fwlink/?LinkId=290816) 13:18:00

Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de *lista de pendientes* que almacena datos de aplicación en el servicio móvil nuevo. El servicio móvil que se creará utiliza JavaScript para la lógica de negocios de servidor. Si desea crear un servicio móvil que le permita escribir su lógica de negocios de servidor en los lenguajes compatibles de .NET con Visual Studio, consulte la [versión de back-end de .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started) de este tema.

La siguiente captura de pantalla muestra la aplicación final:

![](./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png)

**Nota:**

Para completar este tutorial, necesita una cuenta de Azure que tenga habilitada la característica de Servicios móviles de Azure.

-   En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2FFen-us%2Fdocumentation%2Farticles%2Fmobile-services-windows-phone-get-started%2F).

Creación de un servicio móvil
-----------------------------

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Creación de una aplicaciónCreación de una aplicación de Windows Phone
------------------------------------------------------------------------------------------------------

Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación o modificar una ya existente a fin de conectarla a dicho servicio.

En esta sección, creará una aplicación de Windows Phone 8 que se conecta al servicio móvil.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  En la pestaña de inicio rápido, haga clic en **Windows Phone 8** en **Choose platform** y, a continuación, expanda **Create a new Windows Phone 8 app**.

	![][6]

	Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación de Windows Phone conectada al servicio móvil.

	![](./media/mobile-services-windows-phone-get-started/mobile-quickstart-steps-wp8.png)

1.  Si todavía no lo ha hecho, descargue e instale [Visual Studio 2012 Express para Windows Phone](https://go.microsoft.com/fwLink/p/?LinkID=268374) en el equipo local.

2.  Haga clic en **Create TodoItem table** para crear una tabla donde almacenar datos de la aplicación.

3.  En **Download and run app**, haga clic en **Download**.

De este modo se descarga el proyecto para la aplicación de *lista de pendientes* de muestra que está conectada al servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

Ejecución de la aplicación de Windows Phone
-------------------------------------------

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1.  Vaya a la ubicación donde guardó los archivos comprimidos del proyecto, expándalos en su equipo y abra el archivo de solución en Visual Studio.

2.  Pulse la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

3.  En la aplicación, escriba un texto significativo, como *Realice el tutorial* y, a continuación, haga clic en **Save**.

	![][10]

	Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

    > [WACOM.NOTE]Puede revisar el código que tiene acceso a su servicio móvil para consultar e insertar datos, el que se encuentra en el archivo MainPage.xaml.cs.

4.  Nuevamente en el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItems**.

	![][11]

	Esto le permite examinar los datos que la aplicación inserta en la tabla.

	![][12]

Pasos siguientes
----------------

Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles:

-   [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-wp8)
    Obtenga más información sobre almacenar y consultar datos con los Servicios móviles.

-   [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-wp8)
    Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

-   [Introducción a las notificaciones de inserción](/en-us/develop/mobile/tutorials/get-started-with-push-wp8)
    Aprenda a enviar una notificación de inserción muy básica a la aplicación.


<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Create a new mobile service]:#create-new-service
[Define the mobile service instance]:#define-mobile-service-instance
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png





[6]: ./media/mobile-services-windows-phone-get-started/mobile-portal-quickstart-wp8.png
[7]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-steps-wp8.png
[8]: ./media/mobile-services-windows-phone-get-started/mobile-vs-project-wp8.png

[10]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-startup-wp8.png
[11]: ./media/mobile-services-windows-phone-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-windows-phone-get-started/mobile-data-browse.png


<!-- URLs. -->
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-wp8
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-wp8
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-wp8
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[Management Portal]: https://manage.windowsazure.com/
[.NET backend version]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started