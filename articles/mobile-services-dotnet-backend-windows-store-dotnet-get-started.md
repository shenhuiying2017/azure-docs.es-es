<properties pageTitle="Introducción a Servicios móviles para aplicaciones de la Tienda Windows | Centro de desarrollo móvil" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C#, VB, or JavaScript. " metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/18/2014" ms.author="glenga" />


# <a name="getting-started"> </a>Introducción a Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

En este tutorial se muestra cómo agregar un servicio de back-end basado en la nube para una aplicación universal de Windows con Servicios móviles de Azure. Las soluciones de aplicaciones universales de Windows incluyen aplicaciones y un proyecto compartido común tanto para la Tienda Windows 8.1 como para la Tienda de Windows Phone 8.1. Para obtener más información, consulte [Generación de aplicaciones universales dirigidas a Windows y a Windows Phone](http://msdn.microsoft.com/es-es/library/windows/apps/xaml/dn609832.aspx).

Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de *Lista de pendientes* que almacena datos de la aplicación en el servicio móvil nuevo. El servicio móvil que cree utilizará los lenguajes .NET compatibles y recurrirá a Visual Studio para la lógica de negocios de servidor y para las tareas de administración. Para crear un servicio móvil que permita escribir la lógica de negocios del lado servidor en JavaScript, consulte la versión back-end JavaScript de este tema.

>[WACOM.NOTE]En este tema se muestra cómo crear un proyecto de servicio móvil nuevo y la aplicación universal de Windows mediante el Portal de administración de Azure. Si usa Visual Studio 2013 con actualización 3, también puede agregar un proyecto de servicio móvil nuevo a una solución existente de Visual Studio. Para obtener más información, consulte [Quickstart: Agregar un servicio móvil (back-end .NET)](http://msdn.microsoft.com/es-es/library/windows/apps/dn629482.aspx).

>Para agregar un servicio móvil a un proyecto de aplicación de Windows Phone 8.0 o Windows Phone Silverlight 8.1, consulte [Introducción a los datos para Windows Phone](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data).

[WACOM.INCLUDE [mobile-services-windows-universal-get-started](../includes/mobile-services-windows-universal-get-started.md)]

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 servicios móviles gratuitos que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Hay disponible una versión de prueba gratuita.

## Creación de un servicio móvil

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Creación de una nueva aplicación universal de Windows

Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación o modificar una ya existente a fin de conectarla a dicho servicio. 

En esta sección, creará una nueva aplicación universal de Windows que se conecta al servicio móvil.

1. En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.
   
2. En la pestaña de inicio rápido, haga clic en **Windows** en **Seleccionar plataforma** y, a continuación, expanda **Crear una nueva aplicación de la Tienda Windows**.

   	![][6]

   	Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación de la Tienda Windows conectada al servicio móvil.

  	![][7]

3. Si todavía no lo ha hecho, descargue e instale <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> en el equipo local o máquina virtual.

4. En **Descargar y ejecutar la aplicación y el servicio localmente**, seleccione un idioma para la aplicación de la Tienda Windows y, a continuación, haga clic en **Descargar**. 

  	De esta forma, se descarga una solución que contiene proyectos tanto para el servicio móvil como para la aplicación _Lista de pendientes_ de ejemplo conectada a su servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

## Prueba de la aplicación con el servicio móvil local

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

>[WACOM.NOTE]Puede revisar el código de acceso al servicio móvil para consultar e insertar datos; este se encuentra en el archivo MainPage.xaml.cs.


## Publicación del servicio móvil

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


<ol start="4">
<li><p>En el proyecto de código compartido, abra el archivo App.xaml.cs, busque el código que crea una instancia <a href="http://msdn.microsoft.com/es-es/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx" target="_blank">MobileServiceClient</a>, convierta en comentario el código que crea este cliente con <em>localhost</em> y quite la marca de comentario del código que crea el cliente con la dirección URL del servicio móvil remoto, que debe tener el siguiente aspecto:</p>

        <pre><code>public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXX-APPLICATION-KEY-XXXXX");</code></pre>

	<p>El cliente ahora tendrá acceso al servicio móvil publicado en Azure.</p></li>
</ol>

## Prueba de la aplicación con el servicio móvil hospedado en Azure

Ahora que el servicio móvil se ha publicado y el cliente está conectado al servicio móvil remoto hospedado en Azure, podemos ejecutar la aplicación mediante Azure para el almacenamiento de elementos.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]


## Pasos siguientes
Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles: 

* [Introducción a los datos]
  <br/>Más información sobre cómo almacenar y consultar datos con Servicios móviles.

* [Introducción a la sincronización de datos sin conexión]
  <br/>Obtenga información acerca de cómo utilizar la sincronización de datos sin conexión para que la aplicación responde y robusto.

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

* [Introducción a las notificaciones de inserción] 
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

* [Solución de problemas en un back-end .NET de Servicios móviles]
  <br/> Aprenda a diagnosticar y corregir problemas que pueden surgir con un back-end .NET de Servicios móviles. 

Para obtener más información acerca de las aplicaciones universales de Windows, consulte [Compatibilidad de plataformas de varios dispositivos desde un único servicio móvil](/es-es/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs).

<!-- Anchors. -->
[Introducción a Servicios móviles]:#getting-started
[Creación de un servicio móvil]:#create-new-service
[Definición de la instancia del servicio móvil]:#define-mobile-service-instance
[Pasos siguientes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-service-startup.png

[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-startup.png



<!-- URLs. -->
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[Introducción a la sincronización de datos sin conexión]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[SDK de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript y HTML]: mobile-services-win8-javascript/
[Portal de administración]: https://manage.windowsazure.com/
[Versión de back-end de JavaScript]: /es-es/documentation/articles/mobile-services-windows-store-get-started
[Introducción a los datos en Servicios móviles con Visual Studio 2012]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012
[Solución de problemas en un back-end .NET de Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
