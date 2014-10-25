<properties linkid="mobile-services-dotnet-backend-xamarin-android-get-started" urlDisplayName="Get Started with Mobile Services for Xamarin Android apps" pageTitle="Get Started with Mobile Services for Xamarin Android apps - Azure Mobile Services" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Xamarin Android development" metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with Mobile Services for Xamarin Android apps" authors="donnam" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# <a name="getting-started"> </a>Introducción a los Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started][mobile-services-selector-get-started]]

En este tutorial se muestra cómo agregar un servicio de backend basado en la nube a una aplicación Xamarin Android usando Servicios móviles de Azure. Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de *Lista de pendientes* que almacena datos de la aplicación en el servicio móvil nuevo. El servicio móvil que cree utilizará los lenguajes .NET compatibles y recurrirá a Visual Studio para la lógica de negocios de servidor y para las tareas de administración. Si desea crear un servicio móvil que le permita escribir su lógica de negocios de servidor en JavaScript, consulte la [versión back-end de JavaScript][versión back-end de JavaScript] de este tema.

> [WACOM.NOTE]En este tema se muestra cómo crear un proyecto de servicio móvil nuevo desde el Portal de administración de Azure. Si usa Visual Studio 2013 con actualización 2, también puede agregar un proyecto de servicio móvil nuevo a una solución existente de Visual Studio. Para obtener más información, consulte [Quickstart: Add a mobile service (.NET backend)][Quickstart: Add a mobile service (.NET backend)]

La siguiente captura de pantalla muestra la aplicación final:

![][]

Completar este tutorial es un requisito previo para todos los tutoriales de Servicios móviles para aplicaciones Xamarin Android.

> [WACOM.NOTE] Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener detalles, consulte [Evaluación gratuita de Azure][Evaluación gratuita de Azure].
> Este tutorial requiere [Visual Studio Professional 2013][Visual Studio Professional 2013]. Hay disponible una versión de prueba gratuita.

## Creación de un servicio móvil

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][mobile-services-dotnet-backend-create-new-service]]

## Creación de una aplicación Xamarin Android

Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación o modificar una ya existente a fin de conectarla a dicho servicio.

En esta sección va a descargar una nueva aplicación Xamarin Android y un proyecto de servicio para su servicio móvil.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  En la ficha de inicio rápido, haga clic en **Xamarin**, en **Seleccionar plataforma**, y expanda **Crear una nueva aplicación Android**.

    ![][1]

    Con esto se muestran los tres sencillos pasos necesarios para crear una aplicación Xamarin Android conectada al servicio móvil.

    ![][2]

3.  Si todavía no lo ha hecho, descargue e instale [Visual Studio Professional 2013][Visual Studio Professional 2013] en el equipo local o máquina virtual.

4.  Si aún no lo ha hecho, descargue e instale [Xamarin Studio][Xamarin Studio] o Xamarin para Visual Studio.

5.  En **Descargar y publicar el servicio en la nube**, seleccione **Android** y haga clic en **Descargar**.

    De esta forma, se descarga una solución que contiene proyectos tanto para el servicio móvil como para la aplicación *Lista de pendientes* de ejemplo que se conecta al servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

6.  Descargue su perfil de publicación, guarde el archivo descargado en el equipo local y tome nota de dónde lo guarda.

## Prueba del servicio móvil

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][mobile-services-dotnet-backend-test-local-service]]

## Publicación del servicio móvil

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

## Ejecución de la aplicación Xamarin Android

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1.  Navegue al proyecto de cliente en la solución del servicio móvil, en Visual Studio o en Xamarin Studio.

    ![][3]

    ![][4]

2.  Presione el botón **Run** para compilar el proyecto e iniciar la aplicación. Se le solicitará que seleccione un emulador o un dispositivo USB conectado.

    <div class="dev-callout">
<strong>Nota:</strong>
<p>Para poder ejecutar el proyecto en el emulador de Android, debe definir al menos un dispositivo virtual de Android (AVD). Use el administrador AVD para crear y administrar estos dispositivos.</p></div>

3.  En la aplicación, escriba un texto significativo, como *Realice el tutorial*. A continuación, haga clic en el icono de suma (**+**).

    ![][5]

    Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

    > [WACOM.NOTE]
    >  Puede revisar el código que tiene acceso a su servicio móvil para consultar e insertar datos, el que se encuentra en el archivo ToDoActivity.cs C\#.

## Pasos siguientes

Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles:

-   [Introducción a la autenticación][Introducción a la autenticación]

    Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

-   [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción]

    Aprenda a enviar una notificación de inserción muy básica a la aplicación.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [versión back-end de JavaScript]: /es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started
  [Quickstart: Add a mobile service (.NET backend)]: http://msdn.microsoft.com/en-us/library/windows/apps/dn629482.aspx
  []: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-completed-android.png
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-android-get-started
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [1]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
  [2]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-steps-xamarin-android.png
  [Xamarin Studio]: http://xamarin.com/download
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [3]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
  [4]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
  [5]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-startup-android.png
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users
  [Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push
