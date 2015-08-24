<properties
	pageTitle="Introducción a los Servicios móviles para aplicaciones de la Tienda Windows | Centro de desarrollo móvil"
	description="Siga este tutorial para aprender a usar Servicios móviles de Azure para el desarrollo de la Tienda Windows en C#, VB o JavaScript."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="javascript"
	ms.topic="article" 
	ms.date="08/08/2015"
	ms.author="glenga"/>


# <a name="getting-started"> </a>Introducción a Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

##Información general
En este tutorial se muestra cómo agregar un servicio de back-end basado en la nube para una aplicación universal de Windows con Servicios móviles de Azure. Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple *To do list* de pendientes en HTML y JavaScript que almacena datos de la aplicación en el servicio móvil nuevo. El servicio móvil que cree usa los lenguajes .NET compatibles y recurrirá a Visual Studio para la lógica de negocios de servidor y para las tareas de administración. Si desea crear un servicio móvil que le permita escribir su lógica de negocios de servidor en JavaScript, consulte la versión de JavaScript de este tema.

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../../includes/mobile-services-windows-universal-get-started.md)]

##Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Hay disponible una versión de prueba gratuita.


## Creación de un servicio móvil

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Creación de una nueva aplicación universal de Windows

Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación o modificar una ya existente a fin de conectarla a dicho servicio.

En esta sección, creará una nueva aplicación universal de Windows que se conecta al servicio móvil.

1. En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2. En la pestaña de inicio rápido, haga clic en **Windows** en **Seleccionar plataforma** y, a continuación, expanda **Crear una nueva aplicación de la Tienda Windows**.

   	![][6]

   	Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación de la Tienda Windows conectada al servicio móvil.

  	![][7]

3. Si todavía no lo ha hecho, descargue e instale <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> en el equipo local o la máquina virtual.

4. En **Descargar y ejecutar la aplicación y el servicio localmente**, seleccione un idioma para la aplicación de la Tienda Windows y, a continuación, haga clic en **Descargar**.

  	De esta forma, se descarga una solución que contiene proyectos tanto para el servicio móvil como para la aplicación _To do list_ de ejemplo conectada a su servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.


## Prueba de la aplicación con el servicio móvil local

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

>[AZURE.NOTE]Puede revisar el código de acceso al servicio móvil para consultar e insertar datos; este se encuentra en el archivo default.js.

## Publicación del servicio móvil

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

&nbsp;&nbsp;4. En el proyecto de código compartido, abra el archivo default.js, ubique el código que crea una instancia de [WindowsAzure.MobileServiceClient](http://msdn.microsoft.com/library/azure/jj554219.aspx), convierta en comentario el código que crea a este cliente con *localhost* y quite la marca de comentario del código que crea al cliente con la dirección URL del servicio móvil remoto, que debe presentar el siguiente aspecto:

	var client = new WindowsAzure.MobileServiceClient(
	    "https://todolist.azure-mobile.net/",
	    "XXXXXX-APPLICATION-KEY-XXXXXX"
	);

&nbsp;&nbsp;El cliente ahora tendrá acceso al servicio móvil publicado en Azure.

&nbsp;&nbsp;5. Presione la tecla **F5** para recopilar el proyecto e iniciar la aplicación.

&nbsp;&nbsp;6. En la aplicación, escriba texto significativo, como *Realice el tutorial*, en **Insertar TodoItem** y, a continuación, haga clic en **Guardar**.

&nbsp;&nbsp;Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure.

&nbsp;&nbsp;7. (Opcional) En una solución universal de Windows, cambie el proyecto de inicio predeterminado a la otra aplicación y presione **F5** nuevamente y observe que los datos guardados en el paso anterior se carguen desde el servicio móvil después de que se inicie la aplicación.

Para obtener más información acerca de las aplicaciones universales de Windows, consulte [Compatibilidad de plataformas de varios dispositivos desde un único servicio móvil](mobile-services-how-to-use-multiple-clients-single-service.md#shared-vs).

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-service-startup.png

[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-startup.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-publish.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-data-browse.png


<!-- URLs. -->
[Get started with data]: ../mobile-services-dotnet-backend-windows-universal-javascript-get-started-data.md
[Get started with authentication]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md
[Get started with push notifications]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[Management Portal]: https://manage.windowsazure.com/
[JavaScript version]: ../mobile-services-windows-store-get-started.md
[Get started with data in Mobile Services using Visual Studio 2012]: ../mobile-services-windows-store-dotnet-get-started-data-vs2012.md
 

<!---HONumber=August15_HO7-->