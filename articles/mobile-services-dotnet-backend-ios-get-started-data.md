
<properties urlDisplayName="Get Started with Data" pageTitle="Introducción a los datos (iOS) | Centro de desarrollo móvil" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Incorporación de Servicios móviles a una aplicación existente

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

En este tema se muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de iOS. En este tutorial descargará una aplicación que almacena datos en memoria, creará un servicio móvil, integrará el servicio móvil en la aplicación y verá los cambios que se hicieron en los datos durante la ejecución de la aplicación.

El servicio móvil que cree en este tutorial admitirá el tiempo de ejecución .NET. Esto le permitirá utilizar lenguajes .NET y Visual Studio para la lógica de negocios de servidor en el servicio móvil. Si desea crear un servicio móvil que le permita escribir la lógica empresarial del lado del servidor en JavaScript, consulte la [Versión de back-end de JavaScript] de este tema.

>[WACOM.NOTE]Este tutorial está destinado a profundizar en el uso de Azure con los Servicios móviles para almacenar y recuperar datos en una aplicación de iOS. Para ello, en este tema se recorren muchos de los pasos que se completan automáticamente en el inicio rápido de Servicios móviles. Si es la primera vez que usa los Servicios móviles, considere la posibilidad de completar antes el tutorial [Introducción a Servicios móviles].
</div>

Este tutorial le guiará a través de estos pasos básicos:

1. [Descarga del proyecto de la aplicación de iOS]
2. [Crear el servicio móvil]
3. [Descarga del servicio móvil de manera local]
4. [Prueba del servicio móvil]
5. [Publicar el servicio móvil en Azure]
7. [Actualizar la aplicación para usar Servicios móviles]
8. [Probar la aplicación en Servicios móviles]

Este tutorial requiere lo siguiente:

+ [SDK de iOS para Servicios móviles] y [XCode 4.5][Instale Xcode] e iOS 5.0 o versiones posteriores.
+ Visual Studio 2013 (puede obtener [Visual Studio Express para Web](http://go.microsoft.com/p/?linkid=9832232) gratis).
+ Una cuenta de Microsoft Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F" target="_blank">Evaluación gratuita de Azure</a>.

##<a name="download-app"></a>Descarga del proyecto GetStartedWithData

Este tutorial se basa en la [aplicación GetStartedWithData][GitHub], una aplicación de iOS. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de iOS de Servicios móviles, con la excepción de que los elementos agregados se almacenan localmente en la memoria.

1. Descargue la [aplicación GetStartedWithData de ejemplo][GitHub].

2. En Xcode, abra el proyecto descargado y examine el archivo TodoService.m.

   	Observe que hay ocho comentarios **// TODO** que especifican los pasos que debe seguir para que la aplicación funcione con su servicio móvil.

3. Presione el botón **Run (Ejecutar)** (o la tecla Command+R [Comando+R]) para volver a compilar el proyecto e iniciar la aplicación.

4. En la aplicación, escriba algo de texto en el cuadro de texto y haga clic en el botón **+**.

   	![][0]  

   	Observe que el texto guardado se muestra en la lista a continuación.

##<a name="create-service"></a>Creación de un servicio móvil nuevo en el Portal de administración

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

##<a name="download-the-service-locally"></a>Descarga del servicio en el equipo local

[WACOM.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

##<a name="test-the-service"></a>Prueba del servicio móvil

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a name="publish-mobile-service"></a>Publicar el servicio móvil en Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

##<a name="update-app"></a>Actualización de la aplicación para usar el servicio móvil para el acceso a datos

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>Prueba de la aplicación con su servicio móvil nuevo

1. En Xcode, seleccione un emulador en el que implementar (iPhone o iPad), presione el botón **Run** (Ejecutar) (o la tecla Command+R [Comando+R]) para volver a compilar el proyecto e iniciar la aplicación.

   	Esta acción ejecuta su cliente de Servicios móviles de Azure, creado con el SDK de iOS, que consulta elementos desde su servicio móvil.

2. Igual que antes, escriba texto en el cuadro de texto y luego haga clic en el botón **+**.

   	Esto envía un elemento nuevo como inserción al servicio móvil. Cada nuevo elemento TodoItem se almacena y actualiza en la base de datos SQL que configuró previamente para el servicio móvil en el Portal de administración de Azure.

3. Detenga y reinicie la aplicación para ver que los cambios se guardaron en la base de datos de Azure.

	También puede examinar la base de datos utilizando el Portal de administración de Azure o el Explorador de objetos de SQL Server de Visual Studio. En los dos pasos siguientes se utilizará el [Portal de administración de Azure] para ver los cambios en la base de datos.

4. En el Portal de administración de Azure, haga clic en la opción Manage correspondiente a la base de datos asociada con el servicio móvil.

    ![][17]

5. Asimismo, en dicho portal, ejecute una consulta para ver los cambios realizados por la aplicación. La consulta será similar a la siguiente, pero con el nombre de la base de datos en lugar de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![][18]

Con esto concluye el tutorial **Introducción a los datos**.

##<a name="next-steps"></a>Pasos siguientes

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de iOS para trabajar con datos en Servicios móviles.

A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

* [Validación y modificación de datos con scripts]
  <br/>Obtenga más información sobre el uso de scripts de servidor en Servicios móviles para validar y cambiar los datos enviados desde su aplicación.

* [Limitación de consultas con paginación]
  <br/>Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos administrados en una sola solicitud.

Una vez que haya completado la serie de datos, pruebe con uno de estos otros tutoriales para iOS:

* [Introducción a la autenticación]
	<br/>Aprenda a autenticar a los usuarios de la aplicación.

* [Introducción a las notificaciones de inserción]
  <br/>Aprenda a enviar una notificación de inserción muy elemental a la aplicación con Servicios móviles.

<!-- Anchors. -->
[Descarga del proyecto de la aplicación de iOS]: #download-app
[Crear el servicio móvil]: #create-service
[Agregar una tabla de datos para almacenamiento]: #add-table
[Actualizar la aplicación para usar Servicios móviles]: #update-app
[Probar la aplicación en Servicios móviles]: #test-app
[Pasos siguientes]:#next-steps
[Descarga del servicio móvil de manera local]: #download-the-service-locally
[Prueba del servicio móvil]: #test-the-service
[Publicar el servicio móvil en Azure]: #publish-mobile-service


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-todoitem-data-browse.png
[17]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png


<!-- URLs. -->
[Validación y modificación de datos con scripts]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-ios
[Introducción a Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-ios
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-ios
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-ios
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-ios
[Versión de back-end de JavaScript]: /es-es/develop/mobile/tutorials/get-started-with-data-ios

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Portal de administración]: https://manage.windowsazure.com/
[Instalación de Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[SDK de iOS para Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkId=268622
[Repositorio de GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268784
