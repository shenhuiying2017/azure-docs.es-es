<properties
	pageTitle="Introducción a Aplicaciones móviles de Azure para aplicaciones Xamarin.Android"
	description="Siga este tutorial para empezar a usar Aplicaciones móviles de Azure para el desarrollo de Xamarin Android."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor="" />

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="10/20/2015"
	ms.author="normesta" />

#Creación de una aplicación Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]
 
##Información general

En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación Xamarin.Android con un back-end de la aplicación móvil de Azure. Creará tanto un back-end de aplicación móvil nuevo como una aplicación Xamarin.Andorid simple de la _lista de tareas pendientes_ que almacene los datos de la aplicación en Azure.

La siguiente captura de pantalla muestra la aplicación final:

![][0]

Completar este tutorial es un requisito previo para todos los tutoriales de aplicaciones móviles para aplicaciones Xamarin.Android.
 
##Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 aplicaciones móviles gratuitas que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).
 
* [Visual Studio Community 2013] o posterior. Si instala Visual Studio Community 2013, instale [Xamarin] por separado. Puede instalar las herramientas de Xamarin al instalar Visual Studio 2015.
 
>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Probar Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), donde podrá crear inmediatamente una aplicación móvil de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.


## Creación de un nuevo back-end de Aplicaciones móviles de Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Descarga del proyecto de servidor

1. En el equipo, visite el [Portal de Azure]. Haga clic en **Examinar todo** > **Aplicaciones móviles** y, después, haga clic en el back-end de aplicaciones móviles que acaba de crear.

2. En la hoja de la aplicación móvil, haga clic en **Configuración** y, en **Aplicación móvil**, haga clic en **Inicio rápido** > **Xamarin.Android**.
 
3. En **Descargar y ejecutar el proyecto de servidor**, haga clic en **Descargar**. Extraiga los archivos comprimidos del proyecto en su equipo y abra la solución en Visual Studio.
 
## Prueba del proyecto de back-end de forma local

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service](../../includes/app-service-mobile-dotnet-backend-test-local-service.md)]

## Publicación del proyecto de servidor en Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## Descarga y ejecución de la aplicación Xamarin.Android

1. En **Descargar y ejecutar el proyecto de Xamarin.Android**, haga clic en el botón **Descargar**.

  	De este modo, se descarga un proyecto que contiene la aplicación cliente que está conectada a la aplicación móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

	![][8]

	![][9]

2. Presione la tecla **F5** para compilar el proyecto e iniciar la aplicación.

3. En la aplicación, escriba un texto significativo, como _Realice el tutorial_. Luego, haga clic en el botón **Agregar**.

	![][10]

	Esta acción envía una solicitud POST al nuevo back-end de aplicación móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El back-end de la aplicación móvil devuelve los elementos almacenados en la tabla y los datos aparecen en la lista.

	> [AZURE.NOTE]Puede revisar el código de acceso al back-end de aplicación móvil para consultar e insertar datos; este se encuentra en el archivo de C# ToDoActivity.cs.

##Pasos siguientes

* [Incorporación de autenticación a la aplicación ](app-service-mobile-xamarin-android-get-started-users.md) <br/>Obtenga información acerca de cómo autenticar a los usuarios de su aplicación con un proveedor de identidades.


<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Portal de Azure]: https://azure.portal.com/
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
 
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=Nov15_HO1-->