<properties pageTitle="Introducción a Servicios móviles para aplicaciones de la Tienda Windows | Centro de desarrollo móvil" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="hero-article" ms.date="08/18/2014" ms.author="glenga" />

# <a name="getting-started"> </a>Introducción a Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

En este tutorial se muestra cómo agregar un servicio de back-end basado en la nube para una aplicación universal de Windows con Servicios móviles de Azure. Las soluciones de aplicaciones universales de Windows incluyen aplicaciones y un proyecto compartido común tanto para la Tienda Windows 8.1 como para la Tienda de Windows Phone 8.1. Para obtener más información, consulte [Creación de aplicaciones de Windows universales dirigidas a Windows y Windows Phone](http://msdn.microsoft.com/es-es/library/windows/apps/xaml/dn609832.aspx).

En este tutorial, creará tanto un nuevo servicio móvil como una aplicación sencilla *To do list* que almacena datos en el nuevo servicio móvil. El servicio móvil que se creará utiliza JavaScript para la lógica de negocios de servidor. Si desea crear un servicio móvil que le permita escribir su lógica de negocios de servidor en los lenguajes compatibles de .NET con Visual Studio, consulte la versión de back-end de .NET de este tema.

>[WACOM.NOTE]En este tema se muestra cómo crear un proyecto de servicio móvil nuevo y la aplicación universal de Windows mediante el Portal de administración de Azure. Si usa Visual Studio 2013, también puede agregar un proyecto de servicio móvil nuevo a una solución existente de Visual Studio. Para obtener más información, consulte [Quickstart: Adición de un servicio móvil (back-end de JavaScript)](http://msdn.microsoft.com/es-es/library/windows/apps/xaml/dn263180.aspx).

>Para agregar un servicio móvil a un proyecto de aplicación de Windows Phone 8.0 o la Tienda de Windows Phone 8.1, consulte [Introducción a los datos para Windows Phone](/es-es/documentation/articles/mobile-services-windows-phone-get-started-data).

[WACOM.INCLUDE [mobile-services-windows-universal-get-started](../includes/mobile-services-windows-universal-get-started.md)]

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 servicios móviles gratuitos que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* [Visual Studio 2013 Express para Windows] 

## Creación de un servicio móvil

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Creación de una nueva aplicación universal de Windows

Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación universal de Windows o modificar un proyecto de aplicación existente en la Tienda Windows o Windows Phone para conectarla a su servicio móvil. 

En esta sección, creará una nueva aplicación universal de Windows que se conecta al servicio móvil.

1.  En el Portal de administración, haga clic en **Servicios móviles** y después en el servicio móvil que acaba de crear.

   
2. En la pestaña de inicio rápido, haga clic en **Windows** en **Elegir plataforma** y expanda **Crear una nueva aplicación de la Tienda Windows**.

   	![](./media/mobile-services-javascript-backend-windows-store-dotnet-get-started/mobile-portal-quickstart.png)

   	Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación de la Tienda Windows conectada al servicio móvil.

  	![](./media/mobile-services-javascript-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png)

3. Si todavía no lo ha hecho, descargue e instale [Visual Studio 2013 Express para Windows] en su equipo local o máquina virtual.

4. Haga clic en **Crear una tabla TodoItem** para crear una tabla donde almacenar datos de la aplicación.

5. En **Descargar y ejecutar su aplicación**, seleccione un idioma para la aplicación y, a continuación, haga clic en **Descargar**. 

  	De este modo se descarga el proyecto para la aplicación de ejemplo *To do list* que está conectado a su servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

## Ejecución de la aplicación de Windows

[WACOM.INCLUDE [mobile-services-javascript-backend-run-app](../includes/mobile-services-javascript-backend-run-app.md)]

>[WACOM.NOTE]Puede revisar el código de acceso al servicio móvil para consultar e insertar datos; este se encuentra en el archivo MainPage.xaml.cs.

## Pasos siguientes
Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles: 

* [Introducción a los datos]
  <br/>Más información sobre cómo almacenar y consultar datos con Servicios móviles.

* [Introducción a la sincronización de datos sin conexión]
  <br/>Obtenga información acerca de cómo utilizar la sincronización de datos sin conexión para que la aplicación responde y robusto.

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

* [Introducción a las notificaciones de inserción] 
  <br/>Vea cómo enviar una notificación de inserción muy básica a su aplicación.

Para obtener más información acerca de las aplicaciones de Windows universales, consulte [Compatibilidad de plataformas de varios dispositivos desde un único servicio móvil](/es-es/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs).

<!-- Anchors. -->
[Introducción a Servicios móviles]:#getting-started
[Creación de un servicio móvil]:#create-new-service
[Definición de la instancia del servicio móvil]:#define-mobile-service-instance
[Pasos siguientes]:#next-steps

<!-- Images. -->



<!-- URLs. -->
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
[Introducción a la sincronización de datos sin conexión]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-users
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push
[Visual Studio 2013 Express para Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[SDK de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portal de administración]: https://manage.windowsazure.com/
[Introducción a los datos en Servicios móviles con Visual Studio 2012]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012
