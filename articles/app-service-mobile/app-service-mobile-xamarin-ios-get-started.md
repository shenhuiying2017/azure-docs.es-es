<properties
	pageTitle="Introducción a Aplicaciones móviles del Servicio de aplicaciones de Azure para aplicaciones Xamarin.iOS | Microsoft Azure"
	description="Siga este tutorial para empezar a usar Aplicaciones móviles para el desarrollo de Xamarin.iOS."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/12/2015"
	ms.author="normesta"/>


#Creación de una aplicación Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Información general

En este tutorial se muestra cómo agregar un servicio de back-end basado en la nube a una aplicación móvil Xamarin.iOS con un back-end de Aplicaciones móviles de Azure. Creará tanto un back-end de aplicación móvil nuevo como una aplicación Xamarin.iOS simple de la _lista de tareas pendientes_ que almacene los datos de la aplicación en Azure.

Completar este tutorial es un requisito previo para todos los demás tutoriales de Xamarin.iOS sobre cómo usar la característica Aplicaciones móviles en el Servicio de aplicaciones de Azure.

##Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 aplicaciones móviles gratuitas que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013] o posterior. Si instala Visual Studio Community 2013, instale [Xamarin] por separado. Puede instalar las herramientas de Xamarin al instalar Visual Studio 2015.

* Un equipo Mac con [Xcode] v7.0 o versiones posteriores y [Xamarin Studio] instalados.

     >[AZURE.NOTE]Si piensa compilar la aplicación en un equipo basado en Windows mediante Visual Studio, necesitará tener acceso a un equipo Mac en red para hacerlo.

>[AZURE.NOTE]Si desea empezar a usar el Servicio de aplicaciones de Azure antes de suscribirse para obtener una cuenta de Azure, vaya a [Probar Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile). Allí puede crear de forma inmediata una aplicación móvil de corta duración para iniciarse en el Servicio de aplicaciones, no se requiere tarjeta de crédito y no se establece ningún compromiso.

## Creación de un nuevo back-end de Aplicaciones móviles de Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Descarga del proyecto de servidor

1. En el equipo, visite el [Portal de Azure]. Haga clic en **Examinar todo** > **Aplicaciones móviles** y, después, haga clic en el back-end de aplicaciones móviles que acaba de crear.

2. En la hoja de la aplicación móvil, haga clic en **Configuración** y, en **Aplicación móvil**, haga clic en **Inicio rápido** > **Xamarin.iOS**.

3. En **Descargar y ejecutar el proyecto de servidor**, haga clic en **Descargar**. Extraiga los archivos comprimidos del proyecto en su equipo y abra la solución en Visual Studio.

## Prueba del proyecto de back-end de forma local

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service](../../includes/app-service-mobile-dotnet-backend-test-local-service.md)]

## Publicación del proyecto de servidor en Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## Descarga y ejecución de la aplicación Xamarin.iOS

1. En el equipo Mac, abra el [Portal de Azure] en una ventana del explorador.

>[AZURE.NOTE]Es más fácil ejecutar la aplicación Xamarin.iOS en un equipo Mac. Puede ejecutar la aplicación Xamarin.iOS mediante Visual Studio en un equipo basado en Windows si lo desea, pero es un poco más complicado porque tiene que conectarse a un equipo Mac en red. Si está interesado en hacer eso, consulte [Instalación de Xamarin.iOS en Windows].

2. En **Descargar y ejecutar el proyecto de Xamarin.iOS**, haga clic en el botón **Descargar**.

  	De este modo, se descarga un proyecto que contiene la aplicación cliente que está conectada a la aplicación móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

3. Extraiga el proyecto descargado y, después, ábralo en Xamarin Studio (o Visual Studio).

	![][9]

	![][8]

4. Presione la tecla F5 para compilar el proyecto e iniciar la aplicación en el emulador de iPhone.

5. En la aplicación, escriba texto significativo, como _Aprender Xamarin_ y haga clic en el botón **+**.

	![][10]

	Esta acción envía una solicitud POST al nuevo back-end de aplicación móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El back-end de aplicación móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

>[AZURE.NOTE]Puede revisar el código que obtiene acceso al back-end de aplicación móvil para consultar e insertar datos en el archivo de C# QSTodoService.cs.

##Pasos siguientes

* [Incorporación de autenticación a la aplicación](app-service-mobile-xamarin-ios-get-started-users.md) <br/>Aprenda a autenticar a los usuarios de su aplicación mediante un proveedor de identidades.

* [Incorporación de notificaciones de inserción a la aplicación](app-service-mobile-xamarin-ios-get-started-push.md) <br/>Aprenda a enviar una notificación push muy básica a la aplicación.

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Portal de Azure]: https://portal.azure.com/


[Xamarin Studio]: http://xamarin.com/download
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
[Instalación de Xamarin.iOS en Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/

<!---HONumber=Nov15_HO1-->