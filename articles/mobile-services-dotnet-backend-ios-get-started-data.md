<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Introducción a los datos en Servicios móviles

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="C# para Tienda Windows">C# para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title="iOS" class="current">iOS</a><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title="Android">Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title="Back-end de .NET" class="current">Back-end de .NET</a> | <a href="/es-es/develop/mobile/tutorials/get-started-with-data-ios/"  title="Back-end de JavaScript">Back-end de JavaScript</a></div>

Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de iOS. En este tutorial descargará una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil en la aplicación y verá los cambios que se hicieron en los datos durante la ejecución de la aplicación.

El servicio móvil que cree en este tutorial admitirá el tiempo de ejecución .NET. Esto le permitirá utilizar lenguajes .NET y Visual Studio para la lógica de negocios de servidor en el servicio móvil. Si desea crear un servicio móvil que le permita escribir su lógica de negocios de servidor en JavaScript, consulte la [versión back-end de JavaScript][versión back-end de JavaScript] de este tema.

<div class="dev-callout"><b>Nota:</b>
<p>Este tutorial requiere Visual Studio 2013.</p>
</div>

<div class="dev-callout"><b>Nota:</b>
<p>Este tutorial est&aacute; destinado a profundizar en el uso de Azure con los Servicios m&oacute;viles para almacenar y recuperar datos en una aplicaci&oacute;n de iOS. Para ello, en este tema se recorren muchos de los pasos que se completan autom&aacute;ticamente en el inicio r&aacute;pido de Servicios m&oacute;viles. Si esta es la primera vez que usa los Servicios m&oacute;viles, considere la posibilidad de completar antes el tutorial <a href="/es-es/develop/mobile/tutorials/get-started-ios">Introducci&oacute;n a los Servicios m&oacute;viles</a>.</p>
</div>

Este tutorial le guiará a través de estos pasos básicos:

1.  [Descarga del proyecto de la aplicación de iOS][Descarga del proyecto de la aplicación de iOS]
2.  [Creación del servicio móvil][Creación del servicio móvil]
3.  [Descarga del servicio móvil de manera local][Descarga del servicio móvil de manera local]
4.  [Prueba del servicio móvil][Prueba del servicio móvil]
5.  [Publicación del servicio móvil en Azure][Publicación del servicio móvil en Azure]
6.  [Actualización de la aplicación para usar Servicios móviles][Actualización de la aplicación para usar Servicios móviles]
7.  [Prueba de la aplicación en Servicios móviles][Prueba de la aplicación en Servicios móviles]

Este tutorial requiere lo siguiente:

-   Este tutorial requiere el [SDK de iOS para Servicios móviles][SDK de iOS para Servicios móviles], [XCode 4.5][XCode 4.5] y iOS 5.0 o versiones posteriores.
-   Visual Studio 2013 (puede obtener [Visual Studio Express para Web][Visual Studio Express para Web] gratis).
-   Una cuenta de Microsoft Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][Evaluación gratuita de Azure].

## <a name="download-app"></a>Descarga del proyecto GetStartedWithData

Este tutorial se basa en la [aplicación GetStartedWithData][aplicación GetStartedWithData], una aplicación de iOS. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de iOS de Servicios móviles, con la excepción de que los elementos agregados se almacenan localmente en la memoria.

1.  Descargue la [aplicación de muestra][aplicación GetStartedWithData] GetStartedWithData.

2.  En Xcode, abra el proyecto descargado y examine el archivo TodoService.m.

    Tenga en cuenta que hay ocho comentarios **// TODO** que especifican los pasos que debe seguir para que esta aplicación funcione con su servicio móvil.

3.  Presione el botón **Ejecutar** (o la tecla Command+R) para volver a compilar el proyecto e iniciar la aplicación.

4.  En la aplicación, escriba algo de texto en el cuadro de texto y, a continuación, haga clic en el botón **+**.

    ![][]

    Observe que el texto guardado se muestra en la lista a continuación.

## <a name="create-service"></a>Creación de un servicio móvil en el Portal de administración

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][mobile-services-dotnet-backend-create-new-service]]

## <a name="download-the-service-locally"></a>Descarga del servicio en el equipo local

[WACOM.INCLUDE [mobile-services-ios-download-service-locally][mobile-services-ios-download-service-locally]]

## <a name="test-the-service"></a>Prueba del servicio móvil

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][mobile-services-dotnet-backend-test-local-service]]

## <a name="publish-mobile-service"></a>Publicación del servicio móvil en Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

## <a name="update-app"></a>Actualización de la aplicación para que use el servicio móvil para el acceso a datos

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access][mobile-services-ios-enable-mobile-service-access]]

## <a name="test-app"></a>Prueba de la aplicación con el nuevo servicio móvil

1.  En Xcode, seleccione un emulador para implementar en (iPhone o iPad), presione el botón **Ejecutar** (o la tecla Command+R) para volver a crear el proyecto e iniciar la aplicación.

    Esta acción ejecuta su cliente de Servicios móviles de Azure, creado con el SDK de iOS, que consulta elementos desde su servicio móvil.

2.  Al igual que anteriormente, escriba texto en el cuadro de texto y, a continuación, haga clic en el botón **+**.

    Esto envía un elemento nuevo como inserción al servicio móvil. Cada nuevo elemento TodoItem se almacena y actualiza en la base de datos SQL que configuró previamente para el servicio móvil en el Portal de administración de Azure.

3.  Detenga y reinicie la aplicación para ver que los cambios se guardaron en la base de datos de Azure.

    También puede examinar la base de datos utilizando el Portal de administración de Azure o el Explorador de objetos de SQL Server de Visual Studio. En los dos pasos siguientes se utilizará el [Portal de administración de Azure][Portal de administración de Azure] para ver los cambios en la base de datos.

4.  En el Portal de administración de Azure, haga clic en la opción Manage correspondiente a la base de datos asociada con el servicio móvil.

    ![][1]

5.  Asimismo, en dicho portal, ejecute una consulta para ver los cambios realizados por la aplicación. La consulta será similar a la siguiente, pero con el nombre de la base de datos en lugar de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![][2]

Con esto concluye el tutorial **Introducción a los datos**.

## <a name="next-steps"></a>Pasos siguientes

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de iOS para trabajar con datos en Servicios móviles.

A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Validación y modificación de datos con scripts][Validación y modificación de datos con scripts]

    Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación][Limitación de consultas con paginación]

    Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Una vez que haya completado la serie de datos, pruebe con uno de estos otros tutoriales para iOS:

-   [Introducción a la autenticación][Introducción a la autenticación]

    Aprenda a autenticar a los usuarios de su aplicación.

-   [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción]

    Aprenda a enviar una notificación de inserción muy básica a la aplicación con Servicios móviles.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [C# para Tienda Windows]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/ "C# para Tienda Windows"
  [JavaScript para Tienda Windows]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/ "JavaScript para Tienda Windows"
  [Windows Phone]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/ "Windows Phone"
  [iOS]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ "iOS"
  [Android]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/ "Android"
  [Back-end de .NET]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ "Back-end de .NET"
  [Back-end de JavaScript]: /es-es/develop/mobile/tutorials/get-started-with-data-ios/ "Back-end de JavaScript"
  [versión back-end de JavaScript]: /es-es/develop/mobile/tutorials/get-started-with-data-ios
  [Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-ios
  [Descarga del proyecto de la aplicación de iOS]: #download-app
  [Creación del servicio móvil]: #create-service
  [Descarga del servicio móvil de manera local]: #download-the-service-locally
  [Prueba del servicio móvil]: #test-the-service
  [Publicación del servicio móvil en Azure]: #publish-mobile-service
  [Actualización de la aplicación para usar Servicios móviles]: #update-app
  [Prueba de la aplicación en Servicios móviles]: #test-app
  [SDK de iOS para Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Visual Studio Express para Web]: http://go.microsoft.com/p/?linkid=9832232
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F
  [aplicación GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622
  []: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [mobile-services-ios-download-service-locally]: ../includes/mobile-services-ios-download-service-locally.md
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-ios-enable-mobile-service-access]: ../includes/mobile-services-ios-enable-mobile-service-access.md
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
  [2]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png
  [Validación y modificación de datos con scripts]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-ios
  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-ios
  [Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-ios
