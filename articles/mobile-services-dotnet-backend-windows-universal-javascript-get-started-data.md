<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-javascript-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Introducción a los datos en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Este tema muestra cómo usar Servicios móviles de Azure como origen de datos back-end para una aplicación de la Tienda Windows. En este tutorial descargará un proyecto de Visual Studio 2013 para una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil a la aplicación y verá los cambios que se hicieron en los datos durante la ejecución de la aplicación.

El servicio móvil que se crea en este tutorial es un servicio móvil back-end .NET. El back-end .NET permite usar lenguajes .NET y Visual Studio para la lógica de negocios del lado servidor en el servicio móvil. Este servicio puede ejecutarse y depurarse en el equipo local. Para crear un servicio móvil que permita escribir la lógica de negocios del lado servidor en JavaScript, consulte la versión back-end JavaScript de este tema.

> [WACOM.NOTE]Este tema muestra cómo usar las herramientas de Visual Studio Professional 2013 Update 3 para conectar un nuevo servicio móvil a una aplicación universal para Windows. Si no puede actualizar a Visual Studio Professional 2013 Update 3 o prefiere agregar de forma manual el proyecto de servicio móvil a la solución de aplicación de la Tienda Windows, consulte [esta versión][esta versión] del tema.

Este tutorial le guiará a través de estos pasos básicos:

1.  [Descargar el proyecto de la aplicación de la Tienda Windows][Descargar el proyecto de la aplicación de la Tienda Windows]
2.  [Crear un servicio móvil nuevo desde Visual Studio][Crear un servicio móvil nuevo desde Visual Studio]
3.  [Probar el proyecto de servicio móvil de forma local][Probar el proyecto de servicio móvil de forma local]
4.  [Actualizar la aplicación para usar el servicio móvil][Actualizar la aplicación para usar el servicio móvil]
5.  [Publicar el servicio móvil en Azure][Publicar el servicio móvil en Azure]
6.  [Probar la aplicación con el servicio hospedado en Azure][Probar la aplicación con el servicio hospedado en Azure]
7.  [Ver los datos almacenados en la base de datos SQL][Ver los datos almacenados en la base de datos SQL]

Para completar este tutorial, necesitará lo siguiente:

-   Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][Evaluación gratuita de Azure].
-   [Visual Studio Professional 2013][Visual Studio Professional 2013]. Hay disponible una versión de prueba gratuita.

## <a name="download-app"></a>Descargar el proyecto GetStartedWithData

[WACOM.INCLUDE [mobile-services-windows-universal-javascript-download-project](../includes/mobile-services-windows-universal-javascript-download-project.md)]

## <a name="create-service"></a>Crear un servicio móvil nuevo desde Visual Studio

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

1.  En el Explorador de soluciones, vaya a la subcarpeta **services\\mobileService\\scripts**, abra el archivo de script service.js y observe la nueva variable global, similar al ejemplo siguiente:

        var todolistClient = new WindowsAzure.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");

    Este código proporciona acceso a su nuevo servicio móvil en su aplicación mediante una variable global. El cliente se crea al suministrar el URI y la clave de aplicación del nuevo servicio móvil. Como se agregó una referencia a este script en el archivo default.html, esta variable está disponible para todos los archivos de script a los que también se hace referencia desde esta página.

2.  Abra el archivo de proyecto default.html, busque la referencia al nuevo archivo de script service.js y asegúrese de que la ruta de acceso a la que se hace referencia es similar a la siguiente:

        <script src="/services/mobileServices/scripts/todolist.js">

    Actualmente, se produce un error en Visual Studio que genera un nombre de carpeta incorrecto en la ruta de acceso.

3.  Haga clic con el botón secundario en el proyecto de aplicación de Windows Phone y con el botón primario en **Agregar** y **Servicio conectado...**. Seleccione el servicio móvil que acaba de crear y haga clic en **Aceptar**.

    El mismo archivo de código nuevo se agrega al proyecto de aplicación de la Tienda de Windows Phone. Asegúrese de corregir también en este caso la ruta de acceso de referencia que se agrega al archivo default.html.

En este punto, se conectan al nuevo servicio móvil las aplicaciones de la Tienda Windows y de la Tienda de Windows Phone. El paso siguiente consiste en probar el nuevo proyecto de servicio móvil.

## <a name="test-the-service-locally"></a>Probar el proyecto de servicio móvil de forma local

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]

## <a name="update-app"></a>Actualizar la aplicación para usar el servicio móvil

En esta sección, se actualiza la aplicación universal para Windows para usar el servicio móvil como servicio back-end para la aplicación. Solo debe realizar cambios en el archivo de proyecto default.js de la carpeta de proyecto GetStartedWithData.Shared.

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

## <a name="publish-mobile-service"></a>Publicar el servicio móvil en Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="test-azure-hosted"></a>Probar el servicio móvil hospedado en Azure

Ya puede probar las dos versiones de la aplicación universal para Windows con el servicio móvil hospedado en Azure.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

## <a name="view-stored-data"></a>Ver los datos almacenados en la base de datos SQL

[WACOM.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../includes/mobile-services-dotnet-backend-view-sql-data.md)]

Con esto concluye el tutorial **Introducción a los datos**.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial muestra los aspectos básicos de la habilitación de un proyecto de aplicación universal para Windows para trabajar con datos en Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Validación y modificación de datos con scripts][Validación y modificación de datos con scripts]
    Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación][Limitación de consultas con paginación]
    Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Una vez que haya completado la serie de datos, intente con uno de estos otros tutoriales:

-   [Introducción a la autenticación][Introducción a la autenticación]
    Aprenda a autenticar a los usuarios de su aplicación.

-   [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción]
    Aprenda a enviar una notificación de inserción muy básica a la aplicación.

-   [Referencia conceptual de Servicios móviles con .NET][Referencia conceptual de Servicios móviles con .NET]
    Obtenga más información sobre cómo utilizar Servicios móviles con HTML y JavaScript.



  [esta versión]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
  [Descargar el proyecto de la aplicación de la Tienda Windows]: #download-app
  [Crear un servicio móvil nuevo desde Visual Studio]: #create-service
  [Probar el proyecto de servicio móvil de forma local]: #test-the-service-locally
  [Actualizar la aplicación para usar el servicio móvil]: #update-app
  [Publicar el servicio móvil en Azure]: #publish-mobile-service
  [Probar la aplicación con el servicio hospedado en Azure]: #test-azure-hosted
  [Ver los datos almacenados en la base de datos SQL]: #view-stored-data
  [Evaluación gratuita de Azure]: http://azure.microsoft.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-javascript-get-started-data%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [Validación y modificación de datos con scripts]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-js
  [Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-js
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
  [Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
  [Referencia conceptual de Servicios móviles con .NET]: /es-es/documentation/articles/mobile-services-html-how-to-use-client-library/
