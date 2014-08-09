<properties linkid="develop-mobile-tutorials-get-started-wp8" urlDisplayName="Get Started (WP8)" pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Introducción a los Servicios móviles
====================================

[C\# para Tienda Windows](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started "C# para Tienda Windows")[JavaScript para Tienda Windows](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started "JavaScript para Tienda Windows")[Windows Phone](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started "Windows Phone")[iOS](/es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started "iOS") [Android](/es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started "Android")

[Back-end de .NET](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/ "Back-end de .NET") | [Back-end de JavaScript](/es-es/documentation/articles/mobile-services-windows-phone-get-started/ "Back-end de JavaScript")

En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación de Windows Phone 8 con los Servicios móviles de Azure. Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de *Lista de pendientes* que almacena datos de la aplicación en el servicio móvil nuevo. El servicio móvil que va a crear utiliza los lenguajes compatibles de .NET con Visual Studio para la lógica de negocios de servidor y para administrar el servicio móvil. Si desea crear un servicio móvil que le permita escribir su lógica de negocios de servidor en JavaScript, consulte la [versión back-end de JavaScript](/es-es/documentation/articles/mobile-services-windows-phone-get-started) de este tema.

La siguiente captura de pantalla muestra la aplicación final:

![](./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png)

> [WACOM.NOTE] Para completar este tutorial, necesita una cuenta de Azure que tenga habilitada la característica de Servicios móviles de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-phone-get-started%2F).
> Este tutorial requiere [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=257546). Hay disponible una versión de prueba gratuita.

Creación de un servicio móvil
-----------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

Creación de una aplicación de Windows Phone
-------------------------------------------

Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación o modificar una ya existente a fin de conectarla a dicho servicio.

En esta sección, creará una aplicación de Windows Phone 8 que se conecta al servicio móvil.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  En la pestaña de inicio rápido, haga clic en **Windows Phone 8** en **Choose platform** y, a continuación, expanda **Create a new Windows Phone 8 app**.

      ![][6]

      Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación de Windows Phone conectada al servicio móvil.

     ![](./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-steps-wp8.png)

1.  Si todavía no lo ha hecho, descargue e instale [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=257546) en el equipo local o máquina virtual.

2.  En **Descargar y publicar el servicio en la nube**, haga clic en **Download**.

    De esta forma, se descarga una solución que contiene proyectos tanto para el servicio móvil como para la aplicación *Lista de pendientes* de ejemplo que se conecta al servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

1.  En **Publicar su servicio en la nube**, descargue el perfil de publicación, guarde el archivo descargado en el equipo local y tome nota de dónde lo ha guardado.

Prueba del servicio móvil en el equipo local
--------------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

> [WACOM.NOTE] Hay pasos de configuración adicionales que se necesitan para ejecutar una aplicación de Windows Phone que se conecte con un servicio local. En este tema no aparece cómo hacerlo, pero puede obtener más información en [How to connect to a local web service from the Windows Phone 8 emulator](http://go.microsoft.com/fwlink/p/?LinkId=391930).

Publicación del servicio móvil
------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

1.  En el proyecto de la aplicación de Windows Phone, abra el archivo App.xaml.cs, ubique el código que crea una instancia [MobileServiceClient](http://msdn.microsoft.com/es-es/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx), convierta en comentario el código que crea a este cliente con *localhost* y quite la marca de comentario del código que crea al cliente con la dirección URL del servicio móvil remoto, que debe presentar la siguiente apariencia:

    ``` {}
    public static MobileServiceClient MobileService = new MobileServiceClient(
                "https://todolist.azure-mobile.net/",
                "XXXXXXX-APPLICATION-KEY-XXXXXXXX");
    ```

    El cliente ahora tendrá acceso al servicio móvil publicado en Azure.

2.  Presione la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

3.  En la aplicación, escriba un texto significativo, como *Realice el tutorial* y, a continuación, haga clic en **Save**.

    Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

    **Nota:**

    Puede revisar el código de acceso al servicio móvil para consultar e insertar datos; este se encuentra en el archivo MainPage.xaml.cs.

![](./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-startup-wp8.png)

Con esto se muestra cómo ejecutar la nueva aplicación cliente contra el servicio móvil que se ejecuta en Azure. Antes de que pueda probar la aplicación de Windows Phone con el servicio móvil que se ejecuta en un equipo local, debe configurar el servidor web y el firewall para permitir acceso a su emulador o dispositivo de Windows Phone. Para obtener más información, consulte [Configuración del servidor web local para permitir conexiones a un servicio móvil local](/es-es/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).

Pasos siguientes
----------------

Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles:

-   [Introducción a los datos](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data)
    Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

-   [Introducción a la autenticación](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users)
    Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

-   [Introducción a las notificaciones de inserción](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push)
    Aprenda a enviar una notificación de inserción muy básica a la aplicación.


<!-- Images. -->
[0]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png

[6]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-portal-quickstart-wp8.png
[7]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-steps-wp8.png
[8]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-service-startup.pnG

[10]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-startup-wp8.png
[11]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-data-browse.png
