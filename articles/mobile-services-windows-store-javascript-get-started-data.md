<properties linkid="develop-mobile-tutorials-get-started-with-data-js-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store JavaScript app." metaCanonical="https://www.windowsazure.com/es-es/develop/mobile/tutorials/get-started-with-data-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/19/2014" ms.author="glenga" />

# Introducción a los datos en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-center-tutorial-subselector">
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Back-end de .NET">Back-end de .NET</a> | 
    <a href="/es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-data/" title="Back-end de JavaScript" class="current">Back-end de JavaScript</a>
</div>

Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de la Tienda Microsoft. En este tutorial descargará un proyecto de Visual Studio 2013 para una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil a la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

> [WACOM.NOTE] Este tema muestra cómo puede usar Visual Studio 2013 agregar Servicios móviles de Azure a un proyecto de la Tienda Windows. Puede agregar el mismo servicio móvil de back-end de JavaScript a un proyecto de aplicación universal de Windows. Para obtener más información, consulte la [versión de la aplicación universal de Windows][versión de la aplicación universal de Windows] de este tutorial.

Este tutorial le guiará a través de estos pasos básicos:

1.  [Descarga del proyecto de la aplicación de la Tienda Windows][Descarga del proyecto de la aplicación de la Tienda Windows]
2.  [Creación del servicio móvil][Creación del servicio móvil]
3.  [Incorporación de una tabla de datos para almacenamiento][Incorporación de una tabla de datos para almacenamiento]
4.  [Actualización de la aplicación para usar Servicios móviles][Actualización de la aplicación para usar Servicios móviles]
5.  [Prueba de la aplicación en Servicios móviles][Prueba de la aplicación en Servicios móviles]

Para completar este tutorial, necesitará lo siguiente:

-   Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][Evaluación gratuita de Azure].
-   Visual Studio 2013, con lo que es más fácil conectar su aplicación de la Tienda Windows con Servicios móviles. Para completar el mismo procedimiento básico con Visual Studio 2012, siga los pasos en el tema [Introducción a los datos en Servicios móviles con Visual Studio 2012][Introducción a los datos en Servicios móviles con Visual Studio 2012].

## <a name="download-app"></a><span class="short-header">Descarga del proyecto</span>Descarga del proyecto GetStartedWithData

Este tutorial se basa en la [aplicación GetStartedWithMobileServices (en inglés)][aplicación GetStartedWithMobileServices (en inglés)], que es un proyecto de aplicación de la Tienda Windows en Visual Studio 2013. La interfaz de usuario de esta aplicación es idéntica a la aplicación generada por el inicio rápido de Servicios móviles, excepto en que los elementos agregados se almacenan de manera local en la memoria.

1.  Descargue la versión de JavaScript de la aplicación de ejemplo GetStartedWithData desde el [sitio de código de ejemplo para desarrolladores][aplicación GetStartedWithMobileServices (en inglés)] (en inglés).

2.  En Visual Studio 2012 Express para Windows 8, abra el proyecto descargado, expanda la carpeta **js** y examine el archivo default.js.

    Observe que los objetos **TodoItem** agregados se almacenan en un objeto **List** en memoria.

3.  Presione la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

4.  En la aplicación, escriba algún texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

    ![][0]

    Observe que el texto guardado se muestra en la segunda columna, bajo **Consultar y actualizar datos**.

## <a name="create-service"></a>Creación de un servicio móvil desde Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

1.  En el Explorador de soluciones, expanda las carpetas \*\*servicios\*\*, \*\*servicios móviles\*\*, \*\*\<su\_servicio\>\*\*, abra el archivo de script service.js y observe la nueva variable global, que se asemeja al siguiente ejemplo:

        var todolistClient = new WindowsAzure.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");

    Este código proporciona acceso a su nuevo servicio móvil en su aplicación mediante una variable global. El cliente se crea al suministrar el URI y la clave de aplicación del nuevo servicio móvil. Como se agregó una referencia a este script en el archivo default.html, esta variable está disponible para todos los archivos de script a los que también se hace referencia desde esta página.

## <a name="add-table"></a>Incorporación de una tabla nueva para el almacenamiento de datos

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

> [WACOM.NOTE] Las tablas nuevas se crean con las columnas Id, \_\_createdAt, \_\_updatedAt y \_\_version. Cuando está habilitado el esquema dinámico, Servicios móviles genera automáticamente columnas nuevas basadas en el objeto JSON en la solicitud de inserción o actualización. Para obtener más información, consulte [Esquema dinámico][Esquema dinámico].

# <a name="update-app"></a>Actualización de la aplicación para usar el servicio móvil

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

## <a name="test-app"></a>Prueba de la aplicación con su servicio móvil nuevo

1.  En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

2.  Al igual que anteriormente, escriba texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

    Esto envía un elemento nuevo como inserción al servicio móvil.

3.  En el [Portal de administración][Portal de administración], haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4.  Haga clic en la pestaña **Data** y, a continuación, en **Browse**.

    ![][1]

    Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.

5.  En la aplicación, marque uno de los elementos de la lista, vuelva a la pestaña Browse del portal y haga clic en **Refresh**.

    Observe que el valor completo ha cambiado de **false** a **true**.

6.  En el archivo de proyecto default.js, reemplace el método **RefreshTodoItems** existente por el siguiente código que filtra los elementos completados:

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7.  En la aplicación, marque otro de los elementos de la lista y, a continuación, haga clic en el botón **Refresh**.

    Observe que el elemento marcado ahora desaparece de la lista. Cada actualización resulta en un recorrido de ida y vuelta al servicio móvil, que ahora devuelve datos filtrados.

Con esto concluye el tutorial **Introducción a los datos**.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de la Tienda Windows para trabajar con datos en Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Validación y modificación de datos con scripts][Validación y modificación de datos con scripts]
    
	Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación][Limitación de consultas con paginación]
    
	Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Una vez que haya completado la serie de datos, intente con uno de estos otros tutoriales:

-   [Introducción a la autenticación][Introducción a la autenticación]
    
	Aprenda a autenticar a los usuarios de su aplicación.

-   [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción]
    
	Aprenda a enviar una notificación de inserción muy básica a la aplicación.

-   [Referencia conceptual de Servicios móviles con HTML/JavaScript][Referencia conceptual de Servicios móviles con HTML/JavaScript]
    
	Obtenga más información acerca de cómo utilizar los Servicios móviles con HTML y JavaScript.

 
 


  [versión de la aplicación universal de Windows]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data
  [Descarga del proyecto de la aplicación de la Tienda Windows]: #download-app
  [Creación del servicio móvil]: #create-service
  [Incorporación de una tabla de datos para almacenamiento]: #add-table
  [Actualización de la aplicación para usar Servicios móviles]: #update-app
  [Prueba de la aplicación en Servicios móviles]: #test-app
  [Evaluación gratuita de Azure]: http://azure.microsoft.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-javascript-get-started-data%2F
  [Introducción a los datos en Servicios móviles con Visual Studio 2012]: /es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-data-vs2012
  [0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png
  [Esquema dinámico]: http://msdn.microsoft.com/es-es/library/windowsazure/jj193175.aspx
  [Portal de administración]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
  [Validación y modificación de datos con scripts]: /es-es/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
  [Limitación de consultas con paginación]: /es-es/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
  [Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/
  [Referencia conceptual de Servicios móviles con HTML/JavaScript]: /es-es/documentation/articles/mobile-services-html-how-to-use-client-library/
