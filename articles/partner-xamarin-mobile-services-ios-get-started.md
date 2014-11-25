<properties pageTitle="Get Started with Mobile Services for Xamarin iOS apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Xamarin iOS development." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with Mobile Services" authors="craigd" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="craigd" />

# <a name="getting-started"> </a>Introducción a los Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación Xamarin.iOS mediante Servicios móviles de Azure. Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de *Lista de pendientes* que almacena datos de la aplicación en el servicio móvil nuevo.

Si prefiere ver un vídeo, el clip que aparece a continuación muestra los mismos pasos que este tutorial.

Vídeo: "Getting Started with Xamarin and Azure Mobile Services" con Craig Dunn, desarrollador evangelista de Xamarin (duración: 10:05 min)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]

La siguiente captura de pantalla muestra la aplicación final:

![][0]

Para completar este tutorial es necesario XCode 4.5 y iOS 5.0 o versiones posteriores, y [Xamarin Studio][Xamarin Studio] para OS X o el complemento Xamarin Visual Studio para Visual Studio en Windows.

<div class="dev-callout"><strong>Nota:</strong> <p>para completar este tutorial, deber&aacute; tener una cuenta de Azure. Si no dispone de ninguna cuenta, puede registrarse para obtener una versi&oacute;n de evaluaci&oacute;n de Azure y conseguir hasta 10 servicios m&oacute;viles gratuitos que podr&aacute; seguir usando incluso despu&eacute;s de que finalice la evaluaci&oacute;n. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-xamarin-ios%2F" target="_blank">Evaluaci&oacute;n gratuita de Azure</a>.</p></div>

## <a name="create-new-service"> </a>Creación de un servicio móvil

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <span class="short-header">Creación de una nueva aplicación</span>Creación de una nueva aplicación Xamarin.iOS

Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación o modificar una ya existente a fin de conectarla a dicho servicio.

En esta sección se creará una nueva aplicación Xamarin.iOS que se conecta al servicio móvil.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  En la pestaña de inicio rápido, haga clic en **Xamarin.iOS** bajo **Choose platform** y expanda **Create a new Xamarin.iOS app**.

    ![][1]

    Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación Xamarin.iOS conectada al servicio móvil.

    ![][2]

3.  Si todavía no lo ha hecho, descargue e instale [Xcode] v4.4 o una versión posterior y [Xamarin Studio][Xamarin Studio].

4.  Haga clic en **Create TodoItems table** para crear una tabla donde almacenar datos de la aplicación.

5.  En **Download and run app**, haga clic en **Download**.

    De esta forma, se descarga el proyecto para el ejemplo de la aplicación *Lista de tareas pendientes* que se conecta al servicio móvil y hace referencia al componente de Servicios móviles de Azure para Xamarin.iOS. Guarde el archivo de proyecto comprimido en el equipo local y anote el lugar donde lo guardó.

## <span class="short-header">Ejecución de la aplicación</span>Ejecución de una nueva aplicación Xamarin.iOS

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1.  Vaya a la ubicación donde guardó los archivos comprimidos del proyecto, expándalos en su equipo y abra el archivo de la solución **XamarinTodoQuickStart.iOS.sln** con Xamarin Studio o Visual Studio.

    ![][3]

    ![][4]

2.  Presione el botón **Run** para crear el proyecto e iniciar la aplicación en el emulador de iPhone, que es la solución predeterminada para este proyecto.

3.  En la aplicación, escriba un texto significativo, como *Realice el tutorial*. A continuación, haga clic en el icono de suma (**+**).

    ![][5]

    Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

    <div class="dev-callout"> 
<b>Nota:</b> 
<p>Puede revisar el c&oacute;digo de acceso al servicio m&oacute;vil para consultar e insertar datos; este se encuentra en el archivo de C# TodoService.cs.</p> 
</div>

4.  Nuevamente en el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItems**.

    ![][6]

    Esto le permite examinar los datos que la aplicación inserta en la tabla.

    ![][7]

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



  [0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
  [Xamarin Studio]: http://xamarin.com/download
  [1]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
  [2]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
  [3]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
  [4]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
  [5]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
  [6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
  [7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png
  [Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [Introducción a la sincronización de datos sin conexión]: /es-es/develop/mobile/tutorials/mobile-services-xamarin-ios-get-started-offline-data
  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-xamarin-ios
  [Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-xamarin-ios
