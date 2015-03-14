<properties 
	pageTitle="Introducción a Servicios móviles para Xamarin.Android 
	writer="craigd" 
	description="Obtenga información acerca de cómo utilizar Servicios móviles de Azure con la aplicación Xamarin.Android." 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="11/11/2014" 
	ms.author="donnam"/>

# <a name="getting-started"></a>Introducción a los Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>En este tutorial se indica cómo agregar un servicio back-end basado en la nube a una aplicación Xamarin.Android mediante Servicios móviles de Azure. En este tutorial, creará tanto un nuevo servicio móvil como una aplicación sencilla <em>To do list</em> que almacena datos en el nuevo servicio móvil.</p>
<p>La siguiente captura de pantalla muestra la aplicación final:</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="label">ver el tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-xamarin-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir vídeo</span></a> <span class="time">10:05</span></div>
</div>

![][0]

Para completar este tutorial es necesario [Xamarin.Android], que instala Xamarin Studio y un complemento de Visual Studio (en Windows) y la plataforma de Android más reciente. Se requiere el SDK de Android 4.2 o versiones posteriores. 

El proyecto de inicio rápido descargado contiene el componente de servicios móviles de Azure para Xamarin.Android. Aunque este proyecto tiene como destino Android 4.2 o una versión posterior, el SDK mencionado tan solo requiere Android 2.2 o una versión posterior.

> [AZURE.IMPORTANT] Para completar este tutorial, deberá tener una cuenta de Azure. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 servicios móviles gratuitos que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5"%20target="_blank).

## <a name="create-new-service"></a>Creación de un servicio móvil

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2>Creación de una nueva aplicación Xamarin.Android</h2>

Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación o modificar una ya existente a fin de conectarla a dicho servicio. 

En esta sección se creará una nueva aplicación Xamarin.Android que se conecta al servicio móvil.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2. En la pestaña de inicio rápido, haga clic en **Xamarin.Android** en **Elegir una plataforma** y expanda **Crear una nueva aplicación Android**.

	![][6]

	Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación Xamarin.Android conectada al servicio móvil.

	![][7]

3. Haga clic en **Crear tabla TodoItem** para crear una tabla donde almacenar los datos de la aplicación.

4. En **Descargar y ejecutar aplicación**, haga clic en **Descargar**. 

	De este modo se descarga el proyecto para la aplicación de _Lista de pendientes_ de muestra que está conectada al servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

## Ejecución de la aplicación Android

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1. Desplácese a la ubicación donde se guardaron los archivos del proyecto comprimidos y expanda dichos archivos en el equipo.

2. En Xamarin Studio o Visual Studio, haga clic en **File** y, a continuación, en **Open**. Posteriormente, desplácese a los archivos de ejemplo sin comprimir y seleccione **XamarinTodoQuickStart.Android.sln** para abrirlo.

 	![][8]

	![][9]

3. Presione el botón **Run** (Ejecutar) para compilar el proyecto e iniciar la aplicación. Se le solicitará que seleccione un emulador o un dispositivo USB conectado. 

	> [AZURE.NOTE] Para poder ejecutar el proyecto en el emulador de Android, debe definir al menos un dispositivo virtual de Android (AVD). Use el administrador AVD para crear y administrar estos dispositivos.

4. En la aplicación, escriba un texto significativo, como "Realice el tutorial" y, a continuación, haga clic en **Agregar**.

	![][10]

	Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

	> [AZURE.NOTE] 
   	> Puede revisar el código que accede a su servicio móvil para consultar e insertar datos; puede encontrarlo en el archivo de C# ToDoActivity.cs.

6. Nuevamente en el Portal de administración, haga clic en la pestaña **Datos** y, a continuación, en la tabla **TodoItems**.

	![][11]

	Esto le permite examinar los datos que la aplicación inserta en la tabla.

	![][12]

## <a name="next-steps">Pasos siguientes</a>
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
[0]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-completed-android.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page1.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-create-page2.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started/obile-services-selection.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-portal-quickstart-xamarin-android.png
[7]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-xs.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-xamarin-project-android-vs.png
[10]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started/mobile-services-diagram.png


<!-- URLs. -->
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Introducción a la sincronización de datos sin conexión]: /es-es/documentation/articles/mobile-services-xamarin-android-get-started-offline-data
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-xamarin-android
[Xamarin.Android]: http://xamarin.com/download
[SDK de Android para Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[WindowsAzure.com]: http://azure.microsoft.com/
[Portal de administración]: https://manage.windowsazure.com/






<!--HONumber=42-->
