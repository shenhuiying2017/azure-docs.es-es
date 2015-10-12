<properties
	pageTitle="Agregar Servicios móviles a una aplicación existente en iOS"
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
	ms.date="10/01/2015"
	ms.author="krisragh"/>

# Agregar Servicios móviles a una aplicación existente

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

2. Compruebe que los cambios se aplicaron en la base de datos de Azure. Examine la base de datos utilizando el Portal de administración de Azure o el Explorador de objetos de SQL Server de Visual Studio.

3. Para examinar la base de datos mediante el portal, en la página del panel del servicio móvil, haga clic en el nombre de la base de datos, haga clic en "Administrar" para administrar la base de datos e inicie sesión. Ejecute la siguiente consulta, pero use el nombre de su servicio móvil en lugar de `todolist`.

```
        SELECT * FROM [todolist].[todoitems]
```

<!-- Anchors. -->
[Download the iOS app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Publish the mobile service to Azure]: #publish-mobile-service


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-todoitem-data-browse.png
[17]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png


<!-- URLs. -->

[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[JavaScript backend version]: /develop/mobile/tutorials/get-started-with-data-ios


[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268622
[GitHub repo]: http://go.microsoft.com/fwlink/p/?LinkId=268784

[Inicio rápido]: mobile-services-dotnet-backend-ios-get-started.md
[aplicación GetStartedWithData de iOS]: http://go.microsoft.com/fwlink/p/?LinkId=268622
[aplicación de iOS GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622

<!---HONumber=Oct15_HO1-->