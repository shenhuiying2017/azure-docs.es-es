<properties pageTitle="Get Started with Azure Mobile Services for Android apps" metaKeywords="Azure android application, mobile service android, getting started Azure android, azure droid, getting started droid windows" description="Follow this tutorial to get started using Azure Mobile Services for Android development." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Introducción a los Servicios móviles
====================================

[C\# para Tienda Windows](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started "C# para Tienda Windows") [JavaScript para Tienda Windows](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started "JavaScript para Tienda Windows") [Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started "Windows Phone") [iOS](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started "iOS") [Android](/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started "Android")

[Back-end de .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started/ "Back-end de .NET") | [Back-end de JavaScript](/en-us/documentation/articles/mobile-services-android-get-started/ "Back-end de JavaScript")

En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación Android mediante los Servicios móviles de Azure. Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de *Lista de pendientes* que almacena datos de la aplicación en el servicio móvil nuevo. El servicio móvil que cree utilizará los lenguajes .NET compatibles y recurrirá a Visual Studio para la lógica de negocios de servidor y para las tareas de administración. Si desea crear un servicio móvil que le permita escribir su lógica de negocios de servidor en JavaScript, consulte la [versión back-end de JavaScript](/en-us/documentation/articles/mobile-services-android-get-started/) de este tema.

La siguiente captura de pantalla muestra la aplicación final:

![](./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png)

Para completar este tutorial se requieren las [herramientas para desarrolladores de Android (en inglés)](https://go.microsoft.com/fwLink/p/?LinkID=280125) (ADT), que incluyen el entorno de desarrollo integrado (IDE) Eclipse, el complemento para las ADT y la última plataforma Android. Se requiere Android 4.2 o versiones posteriores.

El proyecto de inicio rápido descargado contiene el SDK de Servicios móviles para Android. Si bien este proyecto requiere Android 4.2 o versiones posteriores, el SDK mencionado tan solo requiere Android 2.2 o una versión posterior.

**Nota:**

para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28).

Creación de un servicio móvil
-----------------------------

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

Descarga del servicio móvil en el equipo local
----------------------------------------------

Una vez creado el servicio móvil, descargue el proyecto de servicio móvil personalizado, que podrá ejecutar en el equipo local o la máquina virtual local.

1.  Haga clic en el servicio móvil que acaba de crear, y, a continuación, en la pestaña de inicio rápido haga clic en **Android** debajo de **Choose platform** y expanda **Crear una nueva aplicación Android**.

2.  Si todavía no lo tiene, descargue e instale [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) o una versión posterior.

3.  Haga clic en **Download** debajo de **Descargar y publicar el servicio en la nube**.

    De este modo, se descarga el proyecto de Visual Studio que implementa el servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

4.  Además, descargue el perfil de publicación, guarde el archivo descargado en el equipo local y tome nota de dónde lo guarda.

Prueba del servicio móvil
-------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

Publicación del servicio móvil
------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

Creación de una aplicación Android
----------------------------------

En esta sección se creará una nueva aplicación Android que se conecta al servicio móvil.

1.  En el [Portal de administración](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  En la pestaña de inicio rápido, haga clic en **Android** bajo **Choose platform** y expanda **Create a new Android app**.

3.  Si aún no lo ha hecho, descargue las [herramientas para desarrolladores de Android (en inglés)](https://go.microsoft.com/fwLink/p/?LinkID=280125) e instálelas en el equipo local o la máquina virtual.

4.  Haga clic en **Create TodoItem table** para crear una tabla donde almacenar datos de la aplicación.

5.  En **Download and run your app**, haga clic en **Download**.

De este modo se descarga el proyecto para la aplicación de *lista de pendientes* de muestra que está conectada al servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

Ejecución de la aplicación Android
----------------------------------

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1.  Desplácese a la ubicación donde se guardaron los archivos del proyecto comprimidos y expanda dichos archivos en el equipo.

2.  En Eclipse, haga clic en **File**, **Import**, expanda **Android**, haga clic en **Existing Android Code into Workspace** y, por último, en **Next.**

	![](./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png)

1.  Haga clic en **Browse**, desplácese a la ubicación de los archivos de proyecto expandidos, haga clic en **OK**, asegúrese de que el proyecto TodoActivity esté marcado y, a continuación, haga clic en **Finish**.

	![](./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png)

    De esta forma se importan los archivos del proyecto en el área de trabajo.

	![](./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png)

1.  Haga clic en la opción **Run** del menú **Run** para iniciar el proyecto en el emulador de Android.

    **Nota:**

    Para poder ejecutar el proyecto en el emulador de Android, debe definir al menos un dispositivo virtual de Android (AVD). Use el administrador AVD para crear y administrar estos dispositivos.

2.  En la aplicación, escriba un texto significativo, como *Realice el tutorial* y, a continuación, haga clic en **Add**.

	![][10]

	Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

    **Nota:**

    Puede revisar el código de acceso al servicio móvil para consultar e insertar datos; este se encuentra en el archivo ToDoActivity.java.


<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Create a new mobile service]:#create-new-service
[Define the mobile service instance]:#define-mobile-service-instance
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
[1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs.png




[6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[Get started with authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Management Portal]: https://manage.windowsazure.com/
