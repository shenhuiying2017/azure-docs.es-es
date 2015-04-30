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
	ms.date="04/09/2015" 
	ms.author="ricksal"/>

# Introducción a Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">


<p>En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación Android mediante los Servicios móviles de Azure. Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de <em>Lista de pendientes</em> que almacena datos de la aplicación en el servicio móvil nuevo.</p>
<p>La siguiente captura de pantalla muestra la aplicación final:</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">ver el tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir vídeo</span></a><span class="time">7:26</span></div>
</div>

![](./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png)

## Requisitos previos

Para completar este tutorial se requieren la [Herramientas para desarrolladores de Android][Android Studio], que incluyen el entorno de desarrollo integrado de Android Studio y la última plataforma de Android. Se requiere Android 4.2 o versiones posteriores. 

El proyecto de inicio rápido descargado contiene el SDK de Azure Mobile Services para Android.

> [AZURE.IMPORTANT] Para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28).


## Creación de un servicio móvil

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Creación de una aplicación Android

Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación o modificar una ya existente a fin de conectarla a dicho servicio. 

En esta sección se creará una nueva aplicación Android que se conecta al servicio móvil.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2. En la pestaña de inicio rápido, haga clic en **Android** en **Seleccionar plataforma** y expanda **Crear una nueva aplicación Android**.

  	![](./media/mobile-services-android-get-started/mobile-portal-quickstart-android1.png)

   	Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación Android conectada al servicio móvil.

  	![](./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png)

3. Si aún no lo ha hecho, descargue e instale las [Herramientas para desarrolladores de Android][SDK de Android] en su equipo local o máquina virtual.

4. Haga clic en **Crear tabla TodoItem** para crear una tabla para almacenar los datos de la aplicación.


5. Descargue ahora la aplicación:
	- La versión más reciente de la aplicación utiliza el SDK de Android para Servicios móviles 2.0. Puede descargar esta versión <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures">aquí</a>. Haga clic en **Descargar ZIP**, descomprima el archivo, y el proyecto se encuentra en GettingStarted, bajo de la carpeta Android.
	 
	- Una versión anterior usa la versión previa del SDK. Para usarla, en **Descargar y ejecutar la aplicación**, haga clic en **Descargar**. De este modo se descarga el proyecto para la aplicación de _To do list_ de muestra que está conectada al servicio móvil. Los archivos del proyecto están comprimidos, así que, vaya a su ubicación y expanda los archivos en el equipo.


## Ejecución de la aplicación Android

[AZURE.INCLUDE [mobile-services-run-your-app](../includes/mobile-services-android-get-started.md)]

### Examinar el código (opcional)

Si desea ver el código fuente de la aplicación final, vaya [aquí](https://github.com/RickSaling/mobile-services-samples/tree/androidStudio/GettingStarted/AndroidStudio).


Si desea ver la versión de Eclipse de este tutorial, vaya a: [Introducción (Eclipse)](mobile-services-android-get-started-EC.md).

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
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png
[8]: ./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png
[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png
[14]: ./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Introducción (Eclipse)]: mobile-services-android-get-started-EC.md
[Introducción a los datos]: mobile-services-android-get-started-data.md
[Introducción a la autenticación]: mobile-services-android-get-started-users.md
[Introducción a las notificaciones de inserción]: mobile-services-javascript-backend-android-get-started-push.md
[SDK de Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[SDK de Android para Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Portal de administración]: https://manage.windowsazure.com/

<!--HONumber=52-->