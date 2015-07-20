<properties 
	pageTitle="Introducción a Servicios móviles de Azure para aplicaciones de Android" 
	description="Siga este tutorial para empezar a usar Servicios móviles de Azure para el desarrollo de Android." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="05/13/2015" 
	ms.author="ricksal"/>

# <a name="getting-started"> </a>Introducción a Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started-EC.md)]

En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación Android mediante los Servicios móviles de Azure. Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de _Lista de pendientes_ que almacena datos de la aplicación en el servicio móvil nuevo. El servicio móvil que cree utilizará los lenguajes .NET compatibles y recurrirá a Visual Studio para la lógica de negocios de servidor y para las tareas de administración. Si desea crear un servicio móvil que le permita escribir su lógica de negocios de servidor en JavaScript, consulte la [versión back-end de JavaScript](mobile-services-android-get-started-EC.md) de este tema.

La siguiente captura de pantalla muestra la aplicación final:

![][0]

Para completar este tutorial se requieren las [herramientas para desarrolladores de Android][Android SDK] (ADT), que incluyen el entorno de desarrollo integrado (IDE) Eclipse, el complemento para las ADT y la última plataforma Android. Se requiere Android 4.2 o versiones posteriores.

El proyecto de inicio rápido descargado contiene el SDK de Servicios móviles para Android. Si bien este proyecto requiere Android 4.2 o versiones posteriores, el SDK mencionado tan solo requiere Android 2.2 o una versión posterior.

> [AZURE.IMPORTANT]Para completar este tutorial, deberá tener una cuenta de Azure. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 servicios móviles gratuitos que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28"%20target="_blank).

## <a name="create-new-service"> </a>Creación de un servicio móvil

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Descarga del servicio móvil en el equipo local

Una vez creado el servicio móvil, descargue el proyecto de servicio móvil personalizado, que podrá ejecutar en el equipo local o la máquina virtual local.

1. Haga clic en el servicio móvil que acaba de crear, y, a continuación, en la pestaña de inicio rápido haga clic en **Android** en **Seleccionar plataforma** y expanda **Crear una nueva aplicación Android**.

	![][1]

2. Si todavía no lo ha hecho, descargue e instale [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) o una versión posterior.

3. Haga clic en **Descargar** en **Descargar y publicar el servicio en la nube.**

	De este modo, se descarga el proyecto de Visual Studio que implementa el servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

4. Además, descargue el perfil de publicación, guarde el archivo descargado en el equipo local y tome nota de dónde lo guarda.

## Prueba del servicio móvil

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publicación del servicio móvil

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Creación de una aplicación Android

En esta sección se creará una nueva aplicación Android que se conecta al servicio móvil.

1. En el [Portal de administración], haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2. En la pestaña de inicio rápido, haga clic en **Android** en **Seleccionar plataforma** y expanda **Crear una nueva aplicación Android**.
 
	![][2]

3. Si aún no lo ha hecho, descargue las [herramientas para desarrolladores de Android][Android SDK] (en inglés) e instálelas en el equipo local o la máquina virtual.

4. En **Descargar y ejecutar la aplicación**, haga clic en **Descargar**.

  	De este modo se descarga el proyecto para la aplicación de _lista de pendientes_ de muestra que está conectada al servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

## Ejecución de la aplicación Android

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1. Desplácese a la ubicación donde se guardaron los archivos del proyecto comprimidos y expanda dichos archivos en el equipo.

2. En Eclipse, haga clic en **File** (Archivo), **Import** (Importar), expanda **Android**, haga clic en **Existing Android Code into Workspace** (Código de Android existente en el espacio de trabajo) y luego haga clic en **Next** (Siguiente).

 	![][14]

3. Haga clic en **Examinar**, desplácese a la ubicación de los archivos de proyecto expandidos, haga clic en **Aceptar**, asegúrese de que el proyecto TodoActivity esté marcado y, a continuación, haga clic en **Finalizar**.

 	![][15]

	De esta forma se importan los archivos del proyecto en el área de trabajo actual.

   	![][8]

4. En el menú **Run** (Ejecutar), haga clic en **Run** (Ejecutar) para iniciar el proyecto en el emulador de Android.

	> [AZURE.IMPORTANT]Para poder ejecutar el proyecto en el emulador de Android, debe definir al menos un dispositivo virtual de Android (AVD). Use el administrador AVD para crear y administrar estos dispositivos.

5. En la aplicación, escriba un texto significativo, como _Realizar el tutorial_ y haga clic en **Add** (Agregar).

   	![][10]

   	Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

	> [AZURE.NOTE]Puede revisar el código de acceso al servicio móvil para consultar e insertar datos; este se encuentra en el archivo ToDoActivity.java.

<!--This shows how to run your new client app against the mobile service running in Azure. Before you can test the Android app with the mobile service running on a local computer, you must configure the Web server and firewall to allow access from your Android development computer. For more information, see [Configure the local web server to allow connections to a local mobile service](mobile-services-dotnet-backend-how-to-configure-iis-express.md).-->

## <a name="next-steps"> </a>Pasos siguientes
Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles:

* [Introducción a la autenticación] <br/>Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

* [Introducción a las notificaciones de inserción] <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

* [Solución de problemas de un back-end de .NET de Servicios móviles] <br/> Aprenda a diagnosticar y corregir los problemas que pueden surgir con un back-end de .NET de Servicios móviles.

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-android-get-started-EC/mobile-quickstart-completed-android.png
[1]: ./media/mobile-services-dotnet-backend-android-get-started-EC/mobile-quickstart-steps-vs-EC.png
[2]: ./media/mobile-services-dotnet-backend-android-get-started-EC/mobile-quickstart-steps-android-EC.png


[6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-dotnet-backend-android-get-started-EC/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-dotnet-backend-android-get-started-EC/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-dotnet-backend-android-get-started-EC/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-dotnet-backend-android-get-started-EC/mobile-services-import-android-project.png

<!-- URLs. -->
[Get started with data]: mobile-services-dotnet-backend-android-get-started-data.md
[Introducción a la autenticación]: mobile-services-dotnet-backend-android-get-started-users.md
[Introducción a las notificaciones de inserción]: mobile-services-dotnet-backend-android-get-started-push.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Solución de problemas de un back-end de .NET de Servicios móviles]: mobile-services-dotnet-backend-how-to-troubleshoot.md

[Portal de administración]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO2-->