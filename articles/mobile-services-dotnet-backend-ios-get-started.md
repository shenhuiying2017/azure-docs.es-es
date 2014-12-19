<properties pageTitle="Introducción a Servicios móviles de Azure para aplicaciones iOS" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="Follow this tutorial to get started using Azure Mobile Services for iOS development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# <a name="getting-started"> </a>Introducción a Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

En este tutorial se muestra cómo agregar un servicio backend basado en la nube a una aplicación de iOS con los Servicios móviles de Azure. Con este tutorial creará tanto un servicio móvil como una aplicación _Lista de tareas pendientes_ sencilla que almacena datos de la aplicación en el nuevo servicio móvil. El servicio móvil que cree utilizará los lenguajes .NET compatibles y recurrirá a Visual Studio para la lógica de negocios de servidor y para las tareas de administración. Si desea crear un servicio móvil que le permita escribir la lógica empresarial del lado del servidor en JavaScript, consulte la [Versión de back-end de JavaScript] de este tema.

La siguiente captura de pantalla muestra la aplicación final:

![][0]

Para completar este tutorial necesita XCode 4.5 y iOS 5.0 o versiones posteriores.

<div class="dev-callout"><strong>Nota:</strong> <p>para completar este tutorial, deberá tener una cuenta de Azure. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 servicios móviles gratuitos que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Evaluación gratuita de Azure</a>.</p></div>

## <a name="create-new-service"> </a>Creación de un servicio móvil

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Descarga del servicio móvil en el equipo local

Una vez creado el servicio móvil, descargue el proyecto de servicio móvil personalizado, que podrá ejecutar en el equipo local o la máquina virtual local.

1. Haga clic en el servicio móvil que acaba de crear y, en la pestaña de inicio rápido, haga clic en **iOS** en **Seleccionar plataforma** y expanda **Crear una nueva aplicación iOS**.

	![][1]

2. Si todavía no lo ha hecho, descargue e instale Visual Studio Professional 2013 o una versión posterior.

3. Haga clic en **Descargar** en **Descargar y publicar el servicio en la nube**.

	De este modo, se descarga el proyecto de Visual Studio que implementa el servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y tome nota de dónde lo guarda.

4. Además, descargue el perfil de publicación, guarde el archivo descargado en el equipo local y tome nota de dónde lo guarda.

## Prueba del servicio móvil

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publicación del servicio móvil

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Creación de una nueva aplicación iOS

En esta sección, creará una aplicación de iOS que se conecta al servicio móvil.

1. En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2. En la pestaña de inicio rápido, haga clic en **iOS** en **Seleccionar plataforma** y expanda **Crear una nueva aplicación iOS**.

3. Si todavía no lo ha hecho, descargue e instale [Xcode] v4.4 o versión posterior.

4. En **Descargar y ejecutar la aplicación**, haga clic en **Descargar**.

  	De este modo se descarga el proyecto para la aplicación de _Lista de tareas pendientes_ de muestra que está conectada al servicio móvil, además del SDK de iOS para Servicios móviles. Guarde el archivo comprimido del proyecto en el equipo local y tome nota de dónde lo guarda.

## Ejecución de la nueva aplicación de iOS

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

Con esto se muestra cómo ejecutar la nueva aplicación cliente contra el servicio móvil que se ejecuta en Azure. Antes de que pueda probar la aplicación iOS con el servicio móvil en ejecución en un equipo local, debe configurar el firewall y el servidor web para permitir el acceso desde su equipo de desarrollo iOS. Para obtener más información, consulte [Configuración del servidor web local para permitir conexiones a un servicio móvil local](/es-es/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).

## <a name="next-steps"> </a>Pasos siguientes
Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles:

* [Introducción a los datos]
  <br/>Obtenga más información sobre cómo almacenar y consultar datos con Servicios móviles.

* [Introducción a la sincronización de datos sin conexión]
  <br/>Obtenga información acerca de cómo utilizar la sincronización de datos sin conexión para que la aplicación responde y robusto.

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

* [Introducción a las notificaciones de inserción]
  <br/>Aprenda a enviar una notificación de inserción muy elemental a la aplicación.

* [Solución de problemas en el backend .NET de Servicios móviles]
  <br/> Aprenda a diagnosticar y corregir problemas que pueden surgir con un backend .NET de Servicios móviles.

<!-- Anchors. -->
[Introducción a Servicios móviles]:#getting-started
[Creación de un servicio móvil]:#create-new-service
[Definición de la instancia del servicio móvil]:#define-mobile-service-instance
[Pasos siguientes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[Introducción a la sincronización de datos sin conexión]: /es-es/documentation/articles/mobile-services-ios-get-started-offline-data
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push
[Solución de problemas en el backend .NET de Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/

[SDK de iOS para Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Portal de administración]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Versión de back-end de JavaScript]: /es-es/documentation/articles/mobile-services-ios-get-started
