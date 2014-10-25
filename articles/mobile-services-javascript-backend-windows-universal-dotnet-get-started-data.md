<properties linkid="mobile-services-javascript-backend-windows-universal-dotnet-get-started-data" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Universal) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your universal Windows app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Introducción a los datos en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

En este tema se muestra cómo usar Servicios móviles de Azure para aprovechar los datos en una aplicación universal de Windows. Las soluciones de aplicaciones universales de Windows incluyen aplicaciones y un proyecto compartido común tanto para la Tienda Windows 8.1 como para la Tienda de Windows Phone 8.1. Para obtener más información, consulte [Generación de aplicaciones universales dirigidas a Windows y a Windows Phone][Generación de aplicaciones universales dirigidas a Windows y a Windows Phone].

En este tutorial descargará un proyecto de Visual Studio 2013 para una aplicación universal de Windows que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil a la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

> [WACOM.NOTE]Este tema muestra cómo usar las herramientas de Visual Studio Professional 2013 Update 3 para conectar un nuevo servicio móvil a una aplicación universal para Windows. Se pueden seguir estos mismos pasos para conectar un servicio móvil a una aplicación de la Tienda Windows o de la Tienda de Windows Phone 8.1. Para conectar un servicio móvil a una aplicación de Windows Phone 8.0 o Windows Phone Silverlight 8.1, consulte [Introducción a los datos en Servicios móviles][Introducción a los datos en Servicios móviles].

> Si no puede actualizar a Visual Studio Professional 2013 Update 3 o prefiere agregar de forma manual el proyecto de servicio móvil a la solución de aplicación de la Tienda Windows, consulte [esta versión][esta versión] del tema.

Este tutorial le guiará a través de estos pasos básicos:

1.  [Descargar el proyecto de la aplicación de la Tienda Windows][Descargar el proyecto de la aplicación de la Tienda Windows]
2.  [Crear el servicio móvil desde Visual Studio][Crear el servicio móvil desde Visual Studio]
3.  [Agregar una tabla de datos para almacenamiento][Agregar una tabla de datos para almacenamiento]
4.  [Actualizar la aplicación para usar el servicio móvil][Actualizar la aplicación para usar el servicio móvil]
5.  [Probar la aplicación en Servicios móviles][Probar la aplicación en Servicios móviles]
6.  [Ver los datos cargados en el Portal de administración de Azure][Ver los datos cargados en el Portal de administración de Azure]

Para completar este tutorial, necesitará lo siguiente:

-   Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][Evaluación gratuita de Azure].
-   [Visual Studio Express 2013 para Windows][Visual Studio Express 2013 para Windows] (Update 2 o una versión posterior).

## <a name="download-app"></a>Descargar el proyecto GetStartedWithData

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]

## <a name="create-service"></a>Crear un servicio móvil nuevo desde Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

1.  En el Explorador de soluciones, abra el archivo de código App.xaml.cs en la carpeta de proyecto GetStartedWithData.Shared y observe el nuevo campo estático que se agregó a la clase **App** en un bloque de compilación condicional de aplicación de la Tienda Windows, similar al ejemplo siguiente:

        public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
                    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
                

    Este código proporciona acceso al nuevo servicio móvil en su aplicación mediante el uso de una instancia de la [clase MobileServiceClient][clase MobileServiceClient]. El cliente se crea al suministrar el URI y la clave de aplicación del nuevo servicio móvil. Este campo estático está disponible para todas las páginas en la aplicación.

2.  Haga clic con el botón secundario en el proyecto de aplicación de Windows Phone y con el botón primario en **Agregar** y **Servicio conectado...**. Seleccione el servicio móvil que acaba de crear y haga clic en **Aceptar**.

    Se agrega el mismo código al archivo compartido App.xaml.cs, aunque esta vez en un bloque de compilación condicional de aplicación de Windows Phone.

En este punto, se conectan al nuevo servicio móvil las aplicaciones de la Tienda Windows y de la Tienda de Windows Phone. El paso siguiente consiste en crear la tabla TodoItem nueva en el servicio móvil.

## <a name="add-table"></a>Agregar una tabla nueva al servicio móvil

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

> [WACOM.NOTE]Las tablas nuevas se crean con las columnas Id, \_\_createdAt, \_\_updatedAt y \_\_version. Cuando está habilitado el esquema dinámico, Servicios móviles genera automáticamente columnas nuevas basadas en el objeto JSON en la solicitud de inserción o actualización. Para obtener más información, consulte [Esquema dinámico][Esquema dinámico].

# <a name="update-app"></a>Actualizar la aplicación para usar el servicio móvil

[WACOM.INCLUDE [mobile-services-windows-dotnet-update-data-app](../includes/mobile-services-windows-dotnet-update-data-app.md)]

## <a name="test-azure-hosted"></a>Probar el servicio móvil hospedado en Azure

Ya puede probar las dos versiones de la aplicación universal para Windows con el servicio móvil hospedado en Azure.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

1.  En el [Portal de administración][Portal de administración], haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

    <p>
2.  Haga clic en la pestaña **Data** y, a continuación, en **Browse**.

    Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.

![][]

Con esto concluye el tutorial **Introducción a los datos**.

## <a name="next-steps"> </a>Pasos siguientes

En este tutorial se demostraron los aspectos básicos de la habilitación de una aplicación universal de Windows para trabajar con datos en Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

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
    Obtenga más información sobre cómo utilizar Servicios móviles con .NET.



  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [Generación de aplicaciones universales dirigidas a Windows y a Windows Phone]: http://msdn.microsoft.com/es-es/library/windows/apps/xaml/dn609832.aspx
  [Introducción a los datos en Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-phone-get-started-data
  [esta versión]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
  [Descargar el proyecto de la aplicación de la Tienda Windows]: #download-app
  [Crear el servicio móvil desde Visual Studio]: #create-service
  [Agregar una tabla de datos para almacenamiento]: #add-table
  [Actualizar la aplicación para usar el servicio móvil]: #update-app
  [Probar la aplicación en Servicios móviles]: #test-app
  [Ver los datos cargados en el Portal de administración de Azure]: #view-data
  [Evaluación gratuita de Azure]: http://azure.microsoft.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-dotnet-get-started-data%2F
  [Visual Studio Express 2013 para Windows]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-windows-universal-dotnet-download-project]: ../includes/mobile-services-windows-universal-dotnet-download-project.md
  [mobile-services-create-new-service-vs2013]: ../includes/mobile-services-create-new-service-vs2013.md
  [clase MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  [mobile-services-create-new-table-vs2013]: ../includes/mobile-services-create-new-table-vs2013.md
  [Esquema dinámico]: http://msdn.microsoft.com/es-es/library/windowsazure/jj193175.aspx
  [mobile-services-windows-dotnet-update-data-app]: ../includes/mobile-services-windows-dotnet-update-data-app.md
  [mobile-services-windows-universal-test-app]: ../includes/mobile-services-windows-universal-test-app.md
  [Portal de administración]: https://manage.windowsazure.com/
  []: ./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png
  [Validación y modificación de datos con scripts]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/
  [Limitación de consultas con paginación]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data/
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
  [Referencia conceptual de Servicios móviles con .NET]: /es-es/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
