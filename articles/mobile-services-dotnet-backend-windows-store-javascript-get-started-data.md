<properties urlDisplayName="Get Started with Data" pageTitle="Introducción a datos (Tienda Windows) | Centro de desarrollo móvil" metaKeywords="" description="Obtenga información acerca de cómo empezar a usar Servicios móviles para aprovechar datos en su aplicación de la Tienda Windows." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/27/2014" ms.author="wesmc" />

# Incorporación de Servicios móviles a una aplicación existente

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

Este tema muestra cómo usar Servicios móviles de Azure como origen de datos back-end para una aplicación de la Tienda Windows. En este tutorial descargará un proyecto de Visual Studio 2013 para una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil a la aplicación y verá los cambios que se hicieron en los datos durante la ejecución de la aplicación.

El servicio móvil que se crea en este tutorial es un servicio móvil back-end .NET. El back-end .NET permite usar lenguajes .NET y Visual Studio para la lógica de negocios del lado servidor en el servicio móvil. Este servicio puede ejecutarse y depurarse en el equipo local. Para crear un servicio móvil que permita escribir la lógica de negocios del lado servidor en JavaScript, consulte la versión back-end JavaScript de este tema.

>[WACOM.NOTE]Este tema muestra cómo puede agregar Servicios móviles de Azure a un proyecto de la Tienda Windows. Puede usar las herramientas de Visual Studio 2013 para agregar el mismo servicio móvil de back-end a un proyecto de aplicación universal de Windows. Para obtener más información, consulte la [versión de la aplicación universal de Windows](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data) de este tutorial.

Este tutorial le guiará a través de estos pasos básicos:

1. [Descargar el proyecto de la aplicación de la Tienda Windows]
2. [Creación de un servicio móvil]
3. [Descarga del servicio móvil de manera local]
4. [Actualización de la aplicación de la Tienda Windows para utilizar el servicio móvil]
5. [Prueba de la aplicación de la Tienda Windows con el servicio hospedado de manera local]
6. [Publicar el servicio móvil en Azure]
7. [Prueba de la aplicación de la Tienda Windows con el servicio hospedado en Azure]

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-javascript-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Hay disponible una versión de prueba gratuita.

##<a name="download-app"></a>Descarga del proyecto GetStartedWithData

Este tutorial se basa en la [aplicación GetStartedWithMobileServices (en inglés)][Sitio de ejemplos de código para desarrolladores], que es un proyecto de aplicación de la Tienda Windows en Visual Studio 2013. La interfaz de usuario de esta aplicación es similar a la aplicación generada por el inicio rápido de Servicios móviles, excepto en que los elementos agregados se almacenan de manera local en la memoria. 

1. Descargue la versión de JavaScript de la aplicación de ejemplo GetStartedWithMobileServices desde el [sitio de ejemplos de código para desarrolladores] (en inglés). 

2. Ejecute Visual Studio 2013 con privilegios administrativos haciendo clic con el botón secundario en Visual Studio y, a continuación, haciendo clic en **Ejecutar como administrador**.

3. En Visual Studio 2013, abra el proyecto descargado. Expanda la carpeta js en el Explorador de soluciones y examine el archivo default.js.

   	Observe que los objetos **TodoItem** agregados se almacenan en un elemento `WinJS.Binding.List` en memoria.

4. Presione la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

5. En la aplicación, escriba algún texto en el cuadro que aparece a continuación **Insertar un TodoItem** y, a continuación, haga clic en **Guardar**.

   	![][0]  

   	Observe que el texto guardado se muestra en la segunda columna, bajo **Consultar y actualizar datos**.

##<a name="create-service"></a>Creación de un servicio móvil

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

##<a name="download-the-service-locally"></a>Descarga del proyecto de servicio móvil y agregarlo a la solución

1. En el [Portal de administración de Azure], haga clic en el nuevo servicio móvil o en la pestaña del icono de nube para ir a la página de información general.

    ![][2]

2. Haga clic en la plataforma de la **Tienda de Windows**. En la sección **Introducción**, expanda **Conectar a una aplicación de la Tienda Windows existente** y haga clic en el botón **Descargar** para descargar un proyecto de inicio personalizado para el servicio móvil. 

    ![][3]

3. Deslícese hasta el final de la sección **Introducción** hasta el paso titulado **Publicar su servicio en la nube**. Haga clic en el vínculo que aparece en la captura de pantalla siguiente para descargar un archivo de perfil de publicación para el servicio móvil que acaba de descargar.

    > [WACOM.NOTE] Guarde el archivo en un lugar seguro, ya que contiene información confidencial correspondiente a su cuenta de Azure. Más adelante en este tutorial, eliminará este archivo una vez publicado el servicio móvil. 

    ![][5]


4. Descomprima el proyecto inicial personalizado de servicio descargado. Copie las carpetas que se encontraban en el archivo comprimido en el mismo directorio de **JavaScript** en que está ubicado el archivo de la solución Introducción a los datos (.sln). Esto permitirá al Administrador de paquetes NuGet mantener todos los paquetes sincronizados más fácilmente. 

    ![][26]

5. En el Explorador de soluciones de Visual Studio, haga clic con el botón secundario en la solución para obtener la aplicación de la Tienda Windows Introducción a los datos. Haga clic en **Agregar** y, a continuación, en **Proyecto existente**.

    ![][4]

6. En el cuadro de diálogo Agregar proyecto existente, desplácese hasta la carpeta del proyecto de servicio móvil que movió al directorio **JavaScript**. Seleccione el archivo de proyecto C# (.csproj) en el subdirectorio del servicio. Haga clic en **Abrir** para agregar el proyecto a la solución.

    ![][6]

7. En el Explorador de soluciones de Visual Studio, haga clic con el botón secundario en el proyecto de servicio que acaba de agregar y, a continuación, haga clic en **Compilar** para comprobar que se compile sin errores. Durante la compilación, el Administrador de paquetes NuGet puede necesitar restaurar algunos paquetes NuGet a los que se hace referencia en el proyecto.

    ![][20]

8. Haga clic con el botón secundario de nuevo en el proyecto de servicio. En esta ocasión, haga clic en **Iniciar nueva instancia** en el menú contextual **Depurar**.

    ![][21]

    Visual Studio abre la página web predeterminada del servicio. Puede hacer clic en **Probar ahora** para probar los métodos del servicio móvil desde la página web predeterminada.

    ![][22]

    De forma predeterminada, Visual Studio hospedó el servicio móvil de manera local en IIS Express. Puede verlo haciendo clic con el botón secundario en el icono de bandeja correspondiente a IIS Express de la barra de tareas.

    ![][23]


#<a name="update-app"></a>Actualización de la aplicación de la Tienda Windows para utilizar el servicio móvil

En esta sección, actualizará la aplicación de Tienda Windows para utilizar el servicio móvil como un servicio de back-end para la aplicación.


1. En el Explorador de soluciones de Visual Studio, haga clic con el botón secundario en el proyecto de la aplicación de Tienda Windows y, a continuación, en **Administrar paquetes de NuGet**.

    ![][7]

2. En el cuadro de diálogo Administrar paquetes de NuGet, busque **WindowsAzure.MobileServices.WinJS** en la recopilación de paquetes en línea y haga clic para instalar el paquete de NuGet de Servicios móviles de Azure. A continuación, cierre el cuadro de diálogo.

    ![][8]

3. De vuelta en el Portal de administración de Azure, en la página de información general del servicio móvil, busque el paso con la etiqueta **Conectar la aplicación y almacenar datos en el servicio**. Haga clic en **JavaScript** como el lenguaje y copie el fragmento de código para crear `MobileServiceClient`.

    ![][9]

4. En el Explorador de soluciones de Visual Studio, expanda la carpeta **js** y abra default.js. Pegue el fragmento de código que copió en el controlador de eventos `app.onactivated` justo antes de la definición de la variable `todoItems`. Observe que el fragmento de código incluye un constructor de comentario para conectarse al servicio móvil en Azure mediante la clave de aplicación. Quitará la marca de comentario en cuestión más adelante.

    ![][10]


5. En default.js, reemplace el resto del código por el controlador de eventos `app.onactiviated` con el siguiente código que define `todoItems` y las operaciones que probaremos con el servicio móvil. A continuación, guarde el archivo.

    Este código utiliza el SDK de los Servicios móviles para JavaScript para que la aplicación pueda almacenar sus datos en una tabla proporcionada por el servicio, en lugar de en memoria local. Los tres principales métodos son `insertTodoItem`, `refreshTodoItems` y `updateCheckedTodoItem`. Estos tres métodos le permiten insertar, consultar y actualizar de manera asincrónica la recopilación de datos con una tabla en Azure.

            var todoTable = client.getTable('TodoItem');
            var todoItems = new WinJS.Binding.List();
            var insertTodoItem = function (todoItem) {
                // This code inserts a new TodoItem into the database. 
                // Mobile Services assigns each item an id and the 
                // item is added to the Binding List
                todoTable.insert(todoItem).done(function (item) {
                    todoItems.push(item);
                });
            };
            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view 
                // by querying the TodoItems table.
                todoTable.where()
                    .read()
                    .done(function (results) {
                        todoItems = new WinJS.Binding.List(results);
                        listItems.winControl.itemDataSource = todoItems.dataSource;
                    });
            };
            var updateCheckedTodoItem = function (todoItem) {
                // This code updates the database. 
                // When the MobileService responds, 
                // the item is removed from the list 
                todoTable.update(todoItem).done(function (item) {
                    todoItems.splice(todoItems.indexOf(item), 1);
                });
            };
            buttonSave.addEventListener("click", function () {
                insertTodoItem({
                    text: textInput.value,
                    complete: false
                });
            });
            buttonRefresh.addEventListener("click", function () {
                refreshTodoItems();
            });
            listItems.addEventListener("change", function (eventArgs) {
                var todoItem = eventArgs.target.dataContext.backingData;
                todoItem.complete = eventArgs.target.checked;
                updateCheckedTodoItem(todoItem);
            });
            refreshTodoItems();

6. En el Explorador de soluciones de Visual Studio, abra el archivo default.html. En la parte superior del archivo agregue una nueva referencia de script de WinJS para MobileServices.js. A continuación, guarde el archivo.

        <script type="text/javascript" src="/js/MobileServices.js"></script>

    ![][19]

##<a name="test-locally-hosted"></a>Prueba de la aplicación de la Tienda Windows con el servicio hospedado de manera local

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-data](../includes/mobile-services-dotnet-backend-test-local-service-data.md)]

##<a name="publish-mobile-service"></a>Publicar el servicio móvil en Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="test-azure-hosted"></a>Prueba del servicio móvil publicado en Azure

1. En el Explorador de soluciones de Visual Studio, expanda la carpeta **js** y abra default.js.  Convierta en comentario el código que crea el cliente `MobileServiceClient` que se conecta al servicio móvil hospedado de manera local. Quite la marca de comentario del código que crea el cliente `MobileServiceClient` que se conecta al servicio de Azure. Guarde los cambios del archivo.

        //var client = new WindowsAzure.MobileServiceClient(
        //          "http://localhost:59226"
        //);
        // Use this constructor instead after publishing to the cloud
        var client = new WindowsAzure.MobileServiceClient(
            "https://todolist.preview.azure-mobile-preview.net/",
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");


2. En Visual Studio, presione la tecla F5 o haga clic en **Iniciar depuración** en el menú Depurar. Esto hará que se vuelva a compilar la aplicación de la Tienda Windows con el cambio anterior antes de ejecutar la aplicación para conectarse al servicio móvil hospedado de manera remota en Azure. 

    ![][12]


3. Escriba algunos TodoItems nuevos y haga clic en **Guardar** para cada uno de ellos. Haga clic en la casilla para completar algunos de los nuevos elementos. Cada nuevo TodoItem se almacenará y actualizará en la base de datos SQL que configuró previamente para el servicio móvil en el Portal de administración de Azure. 

    ![][16]

    Puede reiniciar la aplicación para ver si los cambios se aplicaron en la base de datos de Azure. También puede examinar la base de datos utilizando el Portal de administración de Azure o el Explorador de objetos de SQL Server de Visual Studio. En los dos pasos siguientes se utilizará el Portal de administración de Azure para ver los cambios en la base de datos.

4. En el Portal de administración de Azure, haga clic en la opción Administrador correspondiente a la base de datos asociada con el servicio móvil.

    ![][17]

5. En el Portal de administración, ejecute una consulta para ver los cambios que realiza la aplicación de la Tienda Windows. La consulta será similar a la siguiente, pero con el nombre de la base de datos en lugar de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![][18]

Con esto concluye el tutorial **Introducción a los datos**.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de la Tienda Windows para trabajar con datos en Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

* [Validación y modificación de datos con scripts]
  <br/>Más información sobre el uso de scripts de servidor en Servicios móviles para validar y cambiar los datos enviados desde su aplicación.

* [Limitación de consultas con paginación]
  <br/>Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos manipulados en una única solicitud.

Una vez que haya completado la serie de datos, intente con uno de estos otros tutoriales:

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar usuarios de la aplicación.

* [Introducción a las notificaciones de inserción] 
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

* [Referencia conceptual de Servicios móviles con .NET]
  <br/>Obtenga más información acerca de cómo utilizar Servicios móviles con HTML y JavaScript.
  
<!-- Anchors. -->

[Descargar el proyecto de la aplicación de la Tienda Windows]: #download-app
[Creación de un servicio móvil]: #create-service
[Descarga del servicio móvil de manera local]: #download-the-service-locally
[Actualización de la aplicación de la Tienda Windows para utilizar el servicio móvil]: #update-app
[Prueba de la aplicación de la Tienda Windows con el servicio hospedado de manera local]: #test-locally-hosted
[Publicar el servicio móvil en Azure]: #publish-mobile-service
[Prueba de la aplicación de la Tienda Windows con el servicio hospedado en Azure]: #test-azure-hosted
[Pasos siguientes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-data-sample-download-javascript-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-run-solution.png

[16]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/sql-azure-query.png
[19]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-mobileservices-script-reference.png
[20]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-service-and-packages-folder.png

<!-- URLs. -->
[Validación y modificación de datos con scripts]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-js
[Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-js
[Introducción a Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/


[Portal de administración de Azure]: https://manage.windowsazure.com/
[Portal de administración]: https://manage.windowsazure.com/
[SDK de Servicios móviles]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Sitio de ejemplos de código para desarrolladores]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Referencia conceptual de Servicios móviles con .NET]: /es-es/documentation/articles/mobile-services-html-how-to-use-client-library/
[Clase MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  
