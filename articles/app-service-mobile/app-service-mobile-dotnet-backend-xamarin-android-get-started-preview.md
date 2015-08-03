<properties
	pageTitle="Introducción a Aplicaciones móviles de Azure para aplicaciones Xamarin Android: Aplicaciones móviles de Azure"
	description="Siga este tutorial para empezar a usar Aplicaciones móviles de Azure para el desarrollo de Xamarin Android."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="chrisanderson"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="05/01/2015"
	ms.author="chrande"/>

# <a name="getting-started"></a>Creación de una aplicación Xamarin Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]

En este tutorial se muestra cómo agregar un servicio de back-end basado en la nube a una aplicación Xamarin Android con Aplicaciones móviles de Azure. En este tutorial, creará tanto un nuevo servicio .NET como una aplicación simple _To do list_ que almacena datos en el back-end de .NET.

La siguiente captura de pantalla muestra la aplicación final:

![][0]

Completar este tutorial es un requisito previo para todos los tutoriales de Aplicaciones móviles de Azure para aplicaciones Xamarin Android.

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 aplicaciones móviles gratuitas que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de registrarse para abrir una cuenta de Azure, vaya a [Probar Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), donde podrá crear inmediatamente una aplicación móvil de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Creación de un nuevo back-end de aplicación móvil

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Creación de una aplicación Xamarin Android

Una vez que haya creado el back-end de aplicación móvil, podrá seguir una introducción rápida en el [Portal de Azure] para crear una nueva aplicación o modificar una ya existente para conectarla al back-end de aplicación móvil.

En este tutorial va a descargar una nueva aplicación Xamarin Android y un proyecto de servicio de back-end de .NET para la aplicación móvil.

1. En el Portal de Azure, haga clic en **Examinar**, **Aplicaciones móviles** y, a continuación, en la aplicación móvil que acaba de crear.

2. En la parte superior de la hoja, haga clic en **Agregar cliente** y expanda **Xamarin.Android**.

    ![][6]

    Con esto se muestran los tres sencillos pasos necesarios para crear una aplicación Xamarin Android conectada al back-end de aplicación móvil.


3. Si todavía no lo ha hecho, descargue e instale <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> en el equipo local o máquina virtual.

4. Si aún no lo ha hecho, descargue e instale [Xamarin Studio]. También puede usar Xamarin para Visual Studio.

5. En **Descargar y publicar el servicio en la nube**, haga clic en **Descargar**.

  	De esta forma, se descarga una solución que contiene proyectos para el código de back-end de aplicación móvil y para la aplicación cliente de ejemplo _To list_ que está conectada al back-end de aplicación móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

6. Descargue su perfil de publicación, guarde el archivo descargado en el equipo local y tome nota de dónde lo guarda.

## Prueba del back-end de aplicación móvil

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Publicación del back-end de aplicación móvil

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Ejecución de la aplicación Xamarin Android

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1. Navegue al proyecto de cliente en la solución de aplicación móvil, en Visual Studio o en Xamarin Studio.

	![][8]

	![][9]

2. Presione el botón **Ejecutar** para compilar el proyecto e iniciar la aplicación. Se le solicitará que seleccione un emulador o un dispositivo USB conectado.

	> [AZURE.NOTE]Para poder ejecutar el proyecto en el emulador de Android, debe definir al menos un dispositivo virtual de Android (AVD). Use el administrador AVD para crear y administrar estos dispositivos.

3. En la aplicación, escriba un texto significativo, como _Complete el tutorial_ y, a continuación, haga clic en el icono de signo de suma (**+**).

	![][10]

	Esta acción envía una solicitud POST al nuevo back-end de aplicación móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El back-end de aplicación móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

	> [AZURE.NOTE]Puede revisar el código de acceso al back-end de aplicación móvil para consultar e insertar datos; este se encuentra en el archivo de C# ToDoActivity.cs.



<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Portal de Azure]: https://azure.portal.com/
[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
 

<!---HONumber=July15_HO4-->