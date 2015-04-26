<properties 
	pageTitle="Introducción a Servicios móviles de Azure para aplicaciones de Xamarin iOS" 
	description="Siga este tutorial para empezar a usar Servicios móviles de Azure para el desarrollo de Xamarin iOS." 
	services="mobile-services" 
	documentationCenter="xamarin" 
	authors="conceptdev" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="11/22/2014" 
	ms.author="craig.dunn@xamarin.com"/>

# <a name="getting-started">Introducción a los Servicios móviles</a>

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación Xamarin.iOS mediante Servicios móviles de Azure. En este tutorial, creará tanto un nuevo servicio móvil como una aplicación sencilla <em>To do list</em> que almacena datos en el nuevo servicio móvil.

Si prefiere ver un vídeo, el clip que aparece a continuación muestra los mismos pasos que este tutorial.

Vídeo: "Getting Started with Xamarin and Azure Mobile Services" con Craig Dunn, desarrollador evangelista de Xamarin (duración: 10:05 min)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]



La siguiente captura de pantalla muestra la aplicación final:

![][0]

Para completar este tutorial es necesario XCode y [Xamarin Studio] para OS X o el complemento Xamarin Visual Studio para Visual Studio en Windows. El ejemplo se ejecutará en iOS 5.0 y más reciente.

> [AZURE.IMPORTANT] Para completar este tutorial, deberá tener una cuenta de Azure. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 servicios móviles gratuitos que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-xamarin-ios%2F"%20target="_blank).

## <a name="create-new-service"></a>Creación de un servicio móvil

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2>Creación de una aplicación Xamarin.iOS</h2>

Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación o modificar una ya existente a fin de conectarla a dicho servicio. 

En esta sección se creará una nueva aplicación Xamarin.iOS que se conecta al servicio móvil.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2. En la pestaña de inicio rápido, haga clic en **Xamarin.iOS** bajo **Elegir plataforma** y expanda **Creación de una aplicación Xamarin.iOS**.

	![][6]

	Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación Xamarin.iOS conectada al servicio móvil.

  	![][7]

3. Si todavía no lo ha hecho, descargue e instale Xcode (se recomienda la última versión, Xcode 6.0, o una más reciente) y [Xamarin Studio].

4. Haga clic en **Crear tabla TodoItems** para crear una tabla donde almacenar datos de la aplicación.

5. En **Descargar y ejecutar aplicación**, haga clic en **Descargar**. 

	De este modo se descarga el proyecto para la aplicación de _lista de pendientes_ de muestra que está conectada al servicio móvil y que hace referencia al componente de Servicios móviles de Azure para Xamarin.iOS. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

<h2>Ejecución de la nueva aplicación Xamarin.iOS</h2>

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1. Vaya a la ubicación donde guardó los archivos comprimidos del proyecto, expándalos en su equipo y abra el archivo de la solución **XamarinTodoQuickStart.iOS.sln** con Xamarin Studio o Visual Studio.

	![][8]

	![][9]

2. Presione el botón **Ejecutar** para crear el proyecto e iniciar la aplicación en el emulador del iPhone, que es la solución predeterminada para este proyecto.

3. En la aplicación, escriba un texto significativo, como _Realice el tutorial_ y, a continuación, haga clic en el icono del signo más (**+**).

	![][10]

	Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

	> [AZURE.NOTE] Puede revisar el código de acceso al servicio móvil para consultar e insertar datos; este se encuentra en el archivo de C# TodoService.cs.

4. Nuevamente en el Portal de administración, haga clic en la pestaña **Datos** y, a continuación, en la tabla **TodoItems**.

	![][11]

	Esto le permite examinar los datos que la aplicación inserta en la tabla.

	![][12]

## <a name="next-steps">Pasos siguientes</a>
Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles: 

## Pasos siguientes
Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles: 

* [Introducción a la sincronización de datos sin conexión]
  <br/>Descubra cómo el tutorial rápido usa sincronización de datos sin conexión para que la aplicación sea sólida y tenga capacidad de respuesta.

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

* [Introducción a las notificaciones de inserción]
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

<!-- Anchors. -->
[Introducción a Servicios móviles]:#getting-started
[Creación de un servicio móvil]:#create-new-service
[Definición de la instancia del servicio móvil]:#define-mobile-service-instance
[Pasos siguientes]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introducción a la sincronización de datos sin conexión]: /es-es/develop/mobile/tutorials/mobile-services-xamarin-ios-get-started-offline-data
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Xamarin Studio]: http://xamarin.com/download
[SDK de iOS para Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Portal de administración]: https://manage.windowsazure.com/







<!--HONumber=42-->
