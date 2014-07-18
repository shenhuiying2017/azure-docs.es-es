<properties pageTitle="Get Started with Azure Mobile Services for iOS apps" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="Follow this tutorial to get started using Azure Mobile Services for iOS development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />


Introducción a los Servicios móviles
====================================

[Tienda Windows](/en-us/documentation/articles/mobile-services-windows-store-get-started "Tienda Windows") [Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-get-started "Windows Phone") [iOS](/en-us/documentation/articles/mobile-services-ios-get-started "iOS") [Android](/en-us/documentation/articles/mobile-services-android-get-started "Android") [HTML](/en-us/documentation/articles/mobile-services-html-get-started "HTML") [Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started "Xamarin.iOS") [Xamarin.Android](/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started "Xamarin.Android") [Sencha](/en-us/documentation/articles/partner-sencha-mobile-services-get-started/ "Sencha") [PhoneGap](/en-us/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/ "PhoneGap")

[Back-end de .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started/ "Back-end de .NET") | [Back-end de JavaScript](/en-us/documentation/articles/mobile-services-ios-get-started/ "Back-end de JavaScript")

En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación de iOS con los Servicios móviles de Azure.

Si prefiere ver un vídeo, el clip que aparece a la derecha muestra los mismos pasos que este tutorial.

[Ver el tutorial (en inglés)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart) [Reproducir vídeo (en inglés)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart) 9:38:00

Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de *lista de pendientes* que almacena datos de aplicación en el servicio móvil nuevo. El servicio móvil que se creará utiliza JavaScript para la lógica de negocios de servidor. Si desea crear un servicio móvil que le permita escribir su lógica de negocios de servidor en los lenguajes compatibles de .NET con Visual Studio, consulte la [versión de back-end de .NET] de este tema.

La siguiente captura de pantalla muestra la aplicación final:

![][0]

Para completar este tutorial necesita XCode 4.5 y iOS 5.0 o versiones posteriores.

**Nota:**

Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F).

Creación de un servicio móvil
-----------------------------

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

Creación de una aplicaciónCreación de una aplicación de iOS
-----------------------------------------------------------

Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación o modificar una ya existente a fin de conectarla a dicho servicio.

En esta sección, creará una aplicación de iOS que se conecta al servicio móvil.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  En la pestaña de inicio rápido, haga clic en **iOS** en **Choose platform** y, a continuación, expanda **Create a new iOS app**.

	![][6]

	Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación de iOS conectada al servicio móvil.

	![][7]

1.  Si todavía no lo ha hecho, descargue e instale [Xcode] v4.4 o una versión posterior.

2.  Haga clic en **Create TodoItems table** para crear una tabla donde almacenar datos de la aplicación.

3.  En **Download and run your app**, haga clic en **Download**.

De este modo se descarga el proyecto para la aplicación de *lista de pendientes* de muestra que está conectada al servicio móvil, además del SDK de iOS para Servicios móviles. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

Ejecución de la nueva aplicación de iOS
---------------------------------------

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

1.  Nuevamente en el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItems**.

    ![](./media/mobile-services-ios-get-started/mobile-data-tab.png)

    Esto le permite examinar los datos que la aplicación inserta en la tabla.

    ![](./media/mobile-services-ios-get-started/mobile-data-browse.png)

    ## Pasos siguientes Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles: \* [Introducción a los datos]
    Obtenga más información sobre almacenar y consultar datos con los Servicios móviles. \* [Introducción a la autenticación]
    Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades. \* [Introducción a las notificaciones de inserción]
    Aprenda a enviar una notificación de inserción muy básica a la aplicación. [Introducción a los Servicios móviles]:\#getting-started [Creación de un servicio móvil]:\#create-new-service [Definición de la instancia de servicio móvil]:\#define-mobile-service-instance [Pasos siguientes]:\#next-steps [0]: ./media/mobile-services-ios-get-started/mobile-quickstart-completed-ios.png [6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png [7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png [8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png [10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png [11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png [12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png [Introducción a los datos]: /en-us/develop/mobile/tutorials/get-started-with-data-ios [Introducción a la autenticación]: /en-us/develop/mobile/tutorials/get-started-with-users-ios [Introducción a las notificaciones de inserción]: /en-us/develop/mobile/tutorials/get-started-with-push-ios [SDK de iOS para Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533 [Portal de administración]: https://manage.windowsazure.com/ [XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532 [Versión de back-end de .NET]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started

<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Create a new mobile service]:#create-new-service
[Define the mobile service instance]:#define-mobile-service-instance
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started/mobile-quickstart-completed-ios.png

[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-ios

[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Management Portal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[.NET backend version]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started
