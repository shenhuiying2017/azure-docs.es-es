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
	ms.topic="hero-article" 
	ms.date="02/13/2015" 
	ms.author="ricksal;glenga"/>

# <a name="getting-started"> </a>Introducción a Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started-EC.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación Android mediante los Servicios móviles de Azure. Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de <em>Lista de pendientes</em> que almacena datos de la aplicación en el servicio móvil nuevo.</p>
<p>La siguiente captura de pantalla muestra la aplicación final:</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">ver el tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir vídeo</span></a><span class="time">7:26</span></div>
</div>

![][0]

Para completar este tutorial se requieren las [Herramientas para desarrolladores de Android][SDK de Android], que incluyen el entorno de desarrollo integrado (IDE) de Eclipse, el complemento Herramientas para desarrolladores de Android (ADT) y la plataforma Android más reciente. Se requiere Android 4.2 o versiones posteriores. 

El proyecto de inicio rápido descargado contiene el SDK de Servicios móviles para Android. Si bien este proyecto requiere Android 4.2 o versiones posteriores, el SDK mencionado tan solo requiere Android 2.2 o una versión posterior.

> [AZURE.IMPORTANT] Para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28"%20target="_blank).

<!-- -->

>[AZURE.NOTE] Si desea ver el código fuente de la aplicación final, vaya <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStarted/Android" target="_blank">aquí</a>.

## <a name="create-new-service"> </a>Creación de un servicio móvil

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2>Creación de una aplicación Android</h2>

Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación o modificar una ya existente a fin de conectarla a dicho servicio. 

En esta sección se creará una nueva aplicación Android que se conecta al servicio móvil.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2. En la pestaña de inicio rápido, haga clic en **Android** en **Seleccionar plataforma** y expanda **Crear una nueva aplicación Android**.

   ![][6]

   	Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación Android conectada al servicio móvil.

  	![][7]

3. Si aún no lo ha hecho, descargue e instale las [Herramientas para desarrolladores de Android][SDK de Android] en su equipo local o máquina virtual.

4. Haga clic en **Crear tabla TodoItem** para crear una tabla para almacenar los datos de la aplicación.


5. Descargue ahora la aplicación:
	- La versión más reciente de la aplicación utiliza el SDK de Android para Servicios móviles 2.0. Puede descargar esta versión <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures">aquí</a>. Haga clic en **Descargar ZIP**, descomprima el archivo, y el proyecto se encuentra en GettingStarted, bajo de la carpeta Android.
	 
	- Una versión anterior usa la versión previa del SDK. Para usarla, en **Descargar y ejecutar la aplicación**, haga clic en **Descargar**. De este modo se descarga el proyecto para la aplicación de _To do list_ de muestra que está conectada al servicio móvil. Los archivos del proyecto están comprimidos, así que, vaya a su ubicación y expanda los archivos en el equipo.


## Ejecución de la aplicación Android

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

2. En Eclipse, haga clic en **File** (Archivo), **Import** (Importar), expanda **Android**, haga clic en **Existing Android Code into Workspace** (Código de Android existente en el espacio de trabajo) y luego haga clic en **Next (Siguiente).** 

 	![][14]

3. Haga clic en **Browse**, desplácese a la ubicación de los archivos de proyecto expandidos, haga clic en **OK**, asegúrese de que el proyecto TodoActivity esté marcado y, a continuación, haga clic en **Finish**. 

 	![][15]

	De esta forma se importan los archivos del proyecto en el área de trabajo actual.

   ![][8]

4. Si ha descargado la versión 2.0 del SDK, deberá actualizar el código con la dirección URL y la clave del servicio móvil:
	- 	Busque el método **OnCreate** en **TodoActivity.java** y localice el código que crea una instancia del cliente de servicios móviles. El código se puede ver en la imagen anterior.
	- 	Reemplace "MobileServiceUrl" con la dirección URL real del servicio móvil.
	- 	Reemplace "AppKey" con la clave del servicio móvil.
	- 	Para obtener más información, consulte el tutorial <a href="http://azure.microsoft.com/documentation/articles/mobile-services-android-get-started-data/">Incorporación de Servicios móviles a una aplicación existente</a>. 



4. En el menú **Run** (Ejecutar), haga clic en **Run** (Ejecutar) para iniciar el proyecto en el emulador de Android.

	> [AZURE.IMPORTANT] Para poder ejecutar el proyecto en el emulador de Android, debe definir al menos un dispositivo virtual de Android (AVD). Use el administrador AVD para crear y administrar estos dispositivos.

5. En la aplicación, escriba un texto significativo, como "Realice el tutorial" y, a continuación, haga clic en **Agregar**.

   ![][10]

   	Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

	> [AZURE.NOTE] Puede revisar el código de acceso al servicio móvil para consultar e insertar datos; este se encuentra en el archivo ToDoActivity.java.

6. De nuevo en el Portal de administración, haga clic en la pestaña **Datos** y luego haga clic en la tabla **TodoItems**.

   ![][11]

   	Esto le permite examinar los datos que la aplicación inserta en la tabla.

   ![][12]

## <a name="next-steps"> </a>Pasos siguientes
Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles: 

* [Introducción a los datos]
  <br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

* [Introducción a las notificaciones de inserción]
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

<!-- Anchors. -->
[Introducción a los Servicios móviles]:#getting-started
[Creación de un servicio móvil]:#create-new-service
[Definición de la instancia del servicio móvil]:#define-mobile-service-instance
[Pasos siguientes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png





[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android-EC.png
[8]: ./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Introducción a los datos]: mobile-services-android-get-started-data.md
[Introducción a la autenticación]: mobile-services-android-get-started-users.md
[Introducción a las notificaciones de inserción]: mobile-services-javascript-backend-android-get-started-push.md
[SDK de Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[SDK de Android para Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Portal de administración]: https://manage.windowsazure.com/

<!--HONumber=52-->