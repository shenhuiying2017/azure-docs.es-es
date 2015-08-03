<properties
	pageTitle="Incorporación de Servicios móviles a una aplicación existente (iOS) | Centro de desarrollo móvil"
	description="Obtenga información acerca de cómo empezar a usar Servicios móviles para aprovechar datos en su aplicación iOS."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="07/01/2015"
	ms.author="krisragh"/>

# Incorporación de Servicios móviles a una aplicación existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

En este tutorial, descargue una aplicación existente que almacena datos en memoria y cámbielo para que funcione con un Servicio móvil de Azure.

Completar el [Inicio rápido] es un requisito previo necesario antes de iniciar este tutorial. Volver a usar el servicio móvil que cree en el inicio rápido.

##<a name="download-app"></a>Descargue el proyecto GetStartedWithData

Este tutorial se basa en la [aplicación de iOS GetStartedWithData]. La aplicación es idéntica a [Inicio rápido], salvo que los elementos agregados se almacenan en la memoria.

Descargue la [aplicación GetStartedWithData de iOS]. En Xcode, abra el proyecto y examine **TodoService.m**. Existen ocho comentarios **// TODO** que especifican los pasos necesarios para que la aplicación funcione.

##<a name="update-app"></a>Actualice la aplicación para usar el servicio móvil para el acceso a datos

[AZURE.INCLUDE [mobile-services-ios-enable-mobile-service-access](../../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>Aplicación de prueba

1. En Xcode, haga clic en **Ejecutar** para iniciar la aplicación. Agregue elementos a la lista de tareas escribiendo texto y haga clic en **+**.

2. Inicie sesión en el Portal de administración de Azure, haga clic en **Servicios móviles** y, a continuación, en su servicio móvil. Haga clic en la pestaña **Datos** y, a continuación, en **Examinar**. Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.

<!-- Anchors. -->
[Download the iOS app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png







[8]: ./media/mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268622
[GitHub repo]: http://go.microsoft.com/fwlink/p/?LinkId=268784


[Inicio rápido]: ../mobile-services-javascript-backend-ios-get-started.md
[aplicación GetStartedWithData de iOS]: http://go.microsoft.com/fwlink/p/?LinkId=268622
[aplicación de iOS GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622

<!---HONumber=July15_HO4-->