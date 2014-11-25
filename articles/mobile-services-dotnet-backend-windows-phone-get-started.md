<properties linkid="develop-mobile-tutorials-get-started-wp8" urlDisplayName="Get Started (WP8)" pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>Introducción a los Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación de Windows Phone 8 con los Servicios móviles de Azure. Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de *Lista de pendientes* que almacena datos de la aplicación en el servicio móvil nuevo. El servicio móvil que cree utilizará los lenguajes .NET compatibles y recurrirá a Visual Studio para la lógica de negocios de servidor y para las tareas de administración. Si desea crear un servicio móvil que le permita escribir la lógica empresarial del lado del servidor en JavaScript, consulte la [versión back-end de este tema para JavaScript][versión back-end de este tema para JavaScript].

La siguiente captura de pantalla muestra la aplicación final:

![][0]

> [WACOM.NOTE] Necesita una cuenta de Azure para completar este tutorial. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 servicios móviles gratuitos que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure][Evaluación gratuita de Azure].
> Este tutorial requiere [Visual Studio Professional 2013][Visual Studio Professional 2013]. Hay disponible una versión de prueba gratuita. Para crear una nueva aplicación de Windows Phone 8.1, debe disponer de la Actualización 2 de Visual Studio 2013 o una versión posterior.

## <a name="create-new-service"> </a>Creación de un servicio móvil

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Creación de una aplicación de Windows Phone

Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación o modificar una ya existente a fin de conectarla a dicho servicio.

En esta sección, creará una aplicación de Windows Phone 8 que se conecta al servicio móvil.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  En la pestaña de inicio rápido, haga clic en **Windows Phone 8** en **Choose platform** y, a continuación, expanda **Create a new Windows Phone 8 app**.

    ![][1]

    Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación de Windows Phone conectada al servicio móvil.

    ![][2]

3.  Si todavía no lo ha hecho, descargue e instale [Visual Studio Professional 2013][Visual Studio Professional 2013] en el equipo local o máquina virtual.

4.  En **Descargar y publicar el servicio en la nube**, haga clic en **Download**.

    De esta forma, se descarga una solución que contiene proyectos tanto para el servicio móvil como para la aplicación *Lista de pendientes* de ejemplo que se conecta al servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

5.  En **Publicar su servicio en la nube**, descargue el perfil de publicación, guarde el archivo descargado en el equipo local y tome nota de dónde lo ha guardado.

## Prueba del servicio móvil en el equipo local

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

> [WACOM.NOTE] Hay pasos de configuración adicionales que se necesitan para ejecutar una aplicación de Windows Phone que se conecte con un servicio local. En este tema no aparece cómo hacerlo, pero puede obtener más información en [How to connect to a local web service from the Windows Phone 8 emulator][How to connect to a local web service from the Windows Phone 8 emulator].

## Publicación del servicio móvil

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

1.  En el proyecto de la aplicación de Windows Phone, abra el archivo App.xaml.cs, ubique el código que crea una instancia [MobileServiceClient][MobileServiceClient], convierta en comentario el código que crea a este cliente con *localhost* y quite la marca de comentario del código que crea al cliente con la dirección URL del servicio móvil remoto, que debe presentar la siguiente apariencia:

        public static MobileServiceClient MobileService = new MobileServiceClient(
                    "https://todolist.azure-mobile.net/",
                    "XXXXXXX-APPLICATION-KEY-XXXXXXXX");

    El cliente ahora tendrá acceso al servicio móvil publicado en Azure.

2.  (Opcional) Si está creando una aplicación de Windows Phone 8.1, haga clic con el botón secundario en el Explorador de soluciones, haga clic en **Propiedades**, establezca **Versión de destino del SO Windows Phone** en **Windows Phone 8.1** y haga clic en **Sí** para actualizar el proyecto.

3.  Presione la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

4.  En la aplicación, escriba un texto significativo, como *Realice el tutorial* y, a continuación, haga clic en **Save**.

    Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

    <div class="dev-callout"> 
    <b>Nota:</b> 
    <p>Puede revisar el c&oacute;digo de acceso al servicio m&oacute;vil para consultar e insertar datos; este se encuentra en el archivo MainPage.xaml.cs.</p> 
    </div>

![][3]

Con esto se muestra cómo ejecutar la nueva aplicación cliente contra el servicio móvil que se ejecuta en Azure. Antes de que pueda probar la aplicación de Windows Phone con el servicio móvil que se ejecuta en un equipo local, debe configurar el servidor web y el firewall para permitir acceso a su emulador o dispositivo de Windows Phone. Para obtener más información, consulte [Configuración del servidor web local para permitir conexiones a un servicio móvil local][Configuración del servidor web local para permitir conexiones a un servicio móvil local].

## <a name="next-steps"> </a>Pasos siguientes

Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles:

-   [Introducción a los datos][Introducción a los datos]
    Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

-   [Introducción a la sincronización de datos sin conexión][Introducción a la sincronización de datos sin conexión]
    Aprenda a usar la sincronización de datos sin conexión para mejorar la capacidad de respuesta y reforzar la solidez de su aplicación.

-   [Introducción a la autenticación][Introducción a la autenticación]
    Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

-   [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción]
    Aprenda a enviar una notificación de inserción muy básica a la aplicación.

-   [Solución de problemas de un back-end de .NET de Servicios móviles][Solución de problemas de un back-end de .NET de Servicios móviles]
     Aprenda a diagnosticar y corregir los problemas que pueden surgir con un back-end de .NET de Servicios móviles.





  [versión back-end de este tema para JavaScript]: /es-es/documentation/articles/mobile-services-windows-phone-get-started
  [0]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-phone-get-started%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-portal-quickstart-wp8.png
  [2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-steps-wp8.png
  [How to connect to a local web service from the Windows Phone 8 emulator]: http://go.microsoft.com/fwlink/p/?LinkId=391930
  [MobileServiceClient]: http://msdn.microsoft.com/es-es/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started/mobile-quickstart-startup-wp8.png
  [Configuración del servidor web local para permitir conexiones a un servicio móvil local]: /es-es/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [Introducción a la sincronización de datos sin conexión]: /es-es/documentation/articles/mobile-services-windows-phone-get-started-offline-data
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
  [Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
  [Solución de problemas de un back-end de .NET de Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
