

<properties linkid="develop-mobile-tutorials-handle-conflcits-offline-data-dotnet" urlDisplayName="Handle Conflicts with Offline Data" pageTitle="Handle Conflicts with offline data in Mobile Services (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to handle conflicts with offline data in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling conflicts with offline data in Mobile Services" authors="wesmc" />

Control de conflictos con datos sin conexión en Servicios móviles
=================================================================

<div class="dev-center-tutorial-selector sublanding">
<a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data" title="Windows Store C#" class="current">Windows Store C#</a>
</div>

<p>En este tema se explica cómo sincronizar los datos y controlar conflictos cuando se usan las capacidades sin conexión de Servicios móviles de Azure. En este tutorial, descargará una aplicación que admite datos sin conexión y con conexión, integrará el servicio móvil con la aplicación y, a continuación, iniciará sesión en el Portal de administración de Azure para ver y actualizar la base de datos cuando ejecute la aplicación.</p>

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos sin conexión](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data). Antes de comenzar este tutorial, primero debe completar [Introducción a los datos sin conexión](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data).

Este tutorial le guiará a través de estos pasos básicos:

1.  [Descarga del proyecto de la aplicación de la Tienda Windows](#download-app)
2.  [Incorporación de una columna de fecha de vencimiento para la base de datos](#add-column)
    -   [Actualización de la base de datos para servicios móviles back-end de .NET](#dotnet-backend)
    -   [Actualización de la base de datos para servicios móviles con JavaScript](#javascript-backend)

3.  [Prueba de la aplicación en un servicio móvil](#test-app)
4.  [Actualización manual de los datos en el back-end para crear un conflicto](#handle-conflict)

Para este tutorial se necesita Visual Studio 2013 en Windows 8.1.

<a name="download-app"></a>
Descarga del proyecto de ejemplo
--------------------------------

Este tutorial se basa en el [código de ejemplo de control de conflictos](http://go.microsoft.com/fwlink/?LinkId=394787) (en inglés), que es el proyecto de una aplicación de la Tienda Windows en Visual Studio 2013. La interfaz de usuario de esta aplicación es similar a la de la aplicación del tutorial [Introducción a los datos sin conexión](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data), salvo por el hecho de que hay una columna de fecha nueva para cada elemento TodoItem.

1.  Descargue la versión C\# del [código de ejemplo de control de conflictos](http://go.microsoft.com/fwlink/?LinkId=394787) (en inglés).

2.  Instale [SQLite para Windows 8.1](http://go.microsoft.com/fwlink/?LinkId=394776) si no se ha instalado.

3.  En Visual Studio 2013, abra el proyecto descargado. Presione la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

4.  En la aplicación, escriba algún texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**. También puede modificar la fecha de vencimiento de las tareas pendientes incorporadas.

Tenga en cuenta que la aplicación aún no está conectada a ningún servicio móvil, por lo que los botones **Push** y **Pull** lanzarán excepciones.

<a name="add-column"></a>
Incorporación de una columna al modelo de datos
-----------------------------------------------

En esta sección, va a actualizar la base de datos del servicio móvil para incluir una tabla TodoItem con una columna de fecha de vencimiento. La aplicación le permite cambiar la fecha de vencimiento de un elemento en tiempo de ejecución para que pueda generar conflictos de sincronización más adelante en este tutorial.

La clase `TodoItem` del ejemplo está definida en MainPage.xaml.cs. Observe que la clase tiene el siguiente atributo que dirigirá las operaciones de sincronización a dicha tabla.

        [DataTable("TodoWithDate")]

Actualice la base de datos para incluir esta tabla.

<a name="dotnet-backend"></a>
### Actualización de la base de datos para servicios móviles back-end de .NET

Si usa el back-end de .NET para el servicio móvil, siga estos pasos para actualizar el esquema de la base de datos.

1.  Abra el proyecto del servicio móvil de back-end de .NET en Visual Studio.
2.  En el Explorador de soluciones de Visual Studio, en el proyecto del servicio, expanda la carpeta **Modelos** y abra ToDoItem.cs. Agregue la propiedad `DueDate` como sigue.

           public class TodoItem : EntityData
           {
             public string Text { get; set; }
             public bool Complete { get; set; }
             public System.DateTime DueDate { get; set; }
           }

3.  En el Explorador de soluciones de Visual Studio, expanda la carpeta **App\_Start** y abra el archivo WebApiConfig.cs.

    En el archivo WebApiConfig.cs, observe que la clase del inicializador de base de datos predeterminado procede de la clase `DropCreateDatabaseIfModelChanges`. Esto significa que cualquier cambio en el modelo tendrá como resultado la anulación y la recreación de la tabla para adaptarse al modelo nuevo. De esta manera, los datos de la tabla se pierden y la tabla se reinicializa. Modifique el método Seed del inicializador de base de datos para que la inicialización de `Seed()` funcione como sigue para inicializar la nueva columna DueDate. Guarde el archivo WebApiConfig.cs.

    > [WACOM.NOTE] Al usar el inicializador de base de datos predeterminado, Entity Framework eliminará la base de datos y la volverá a crear siempre que detecte un cambio del modelo de datos en la definición del modelo de Code First. Para realizar este cambio en el modelo de datos y mantener los datos existentes en la base de datos, debe utilizar Migraciones de Code First. Para obtener más información, consulte [Uso de Migraciones de Code First para actualizar el modelo de datos](./articles/mobile-services-dotnet-backend-use-code-first-migrations).

         new TodoItem { Id = "1", Text = "First item", Complete = false, DueDate = DateTime.Today },
         new TodoItem { Id = "2", Text = "Second item", Complete = false, DueDate = DateTime.Today },

4.  En el Explorador de soluciones de Visual Studio, expanda la carpeta **Controladores** y abra ToDoItemController.cs. Cambie el nombre de la clase `TodoItemController` por `TodoWithDateController`. Esto cambiará el extremo de REST para las operaciones de tabla.

         public class TodoWithDateController : TableController<TodoItem>

5.  En el Explorador de soluciones de Visual Studio, haga clic con el botón secundario en el proyecto del servicio móvil back-end de .NET y haga clic en **Publicar** para publicar los cambios.

<a name="javascript-backend"></a>
### Actualización de la base de datos para servicios móviles back-end de JavaScript

Para los servicios móviles back-end de JavaScript, agregará una nueva tabla llamada **TodoWithDate**. Para agregar la tabla **TodoWithDate** para los servicios móviles back-end de JavaScript, siga estos pasos.

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/).

2.  Vaya a la pestaña **Data** del servicio móvil.

3.  Haga clic en **Create** en la parte inferior de la página y cree una nueva tabla llamada **TodoWithDate**.

<a name="test-app"></a>
Prueba de la aplicación en un servicio móvil
--------------------------------------------

Ahora es el momento de probar la aplicación en los servicios móviles.

1.  En el Portal de administración de Azure, busque la clave de la aplicación del servicio móvil; para ello, haga clic en **Administrar claves** en la barra de comandos. Copie la **clave de aplicación**.

2.  En el Explorador de soluciones de Visual Studio, abra el archivo App.xaml.cs en el proyecto de ejemplo cliente. Cambie la inicialización de **MobileServiceClient** para usar la URL del servicio móvil y la clave de aplicación:

          public static MobileServiceClient MobileService = new MobileServiceClient(
             "https://your-mobile-service.azure-mobile.net/",
             "Your AppKey"
         );

3.  En Visual Studio, presione la tecla **F5** para compilar y ejecutar la aplicación.

4.  Al igual que anteriormente, escriba texto en el cuadro de texto y, a continuación, haga clic en **Save**. De esta forma, los datos se guardan en la tabla de sincronización local, pero no en el servidor.

	![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png)

5.  Para ver el estado actual de la base de datos, inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.

-   Si usa el back-end de JavaScript para el servicio móvil, haga clic en la pestaña **Data** y, a continuación, haga clic en la tabla **TodoWithDate**. Haga clic en **Examinar** para ver si la tabla aún está vacía, ya que no hemos insertado los cambios de la aplicación en el servidor.

	![][1]

-   Si usa el back-end de .NET para el servicio móvil, haga clic en la pestaña **Configure** y, a continuación, haga clic en la base de datos SQL. Haga clic en **Manage** en la parte inferior de la pantalla para iniciar sesión en el Portal de administración de Azure en SQL para ver la base de datos.

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-empty-sql.png)

1.  De nuevo en la aplicación, haga clic en **Push**.

2.  En el Portal de administración, haga clic en **Refresh** en la tabla **TodoItem**. Ahora debe ver los datos que ha escrito en la aplicación.

	![][3]

<a name="handle-conflict"></a>
Actualización de los datos en el back-end para crear un conflicto
-----------------------------------------------------------------

En un escenario real, se produciría un conflicto de sincronización cuando una aplicación inserta las actualizaciones en un registro en la base de datos y, a continuación, otra aplicación trata de insertar un cambio en el mismo registro basado en una versión obsoleta de dicho registro. Si una instancia de la aplicación trata de actualizar el mismo registro, sin extraer el registro actualizado, se produciría un conflicto y se detectaría como una excepción `MobileServicePreconditionFailedException` en la aplicación.

Si desea implementar la aplicación en otra máquina para ejecutar dos instancias de la aplicación a fin de generar un conflicto, puede seguir las instrucciones de implementación descritas en el tutorial [Control de conflictos de la base de datos](/en-us/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/#test-app).

En los siguientes pasos se explica cómo puede actualizar la base de datos en Visual Studio para causar un conflicto.

1.  En Visual Studio, ejecute el Explorador de servidores y conéctese a la cuenta de Azure. Expanda **Bases de datos SQL** para la cuenta de Azure.

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-server-explorer.png)

2.  Haga clic con el botón secundario en la base de datos SQL de la lista y, a continuación, haga clic en **Open in SQL Server Object Explorer**.
3.  En el Explorador de objetos de SQL Server, expanda la base de datos y, a continuación, expanda **Tablas**. Haga clic con el botón secundario en la tabla **TodoWithDate** y, a continuación, haga clic en **Ver datos**.

4.  Cambie el campo **complete** de uno de los elementos a True.

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-sql-server-object-explorer-update-data.png)

5.  En la aplicación Todo, edite directamente el mismo elemento que ha modificado en la base de datos.

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run2.png)

6.  Haga clic en el botón **Push**. Se abrirá un cuadro de diálogo en que se preguntará cómo resolver el conflicto. Elija una de las opciones para resolver el conflicto.

    ![](./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run3.png)

Revisión del código para controlar los conflictos de sincronización
-------------------------------------------------------------------

Para configurar la característica sin conexión para detectar conflictos, debe incluir una columna de versión en la base de datos local y en el objeto de transferencia de datos. La clase `TodoItem` tiene el siguiente miembro:

        [Versión]
        public string Version { get; set; }

La columna `__version` también se especifica en la base de datos local configurada en el método `OnNavigatedTo()`.

Para controlar los conflictos de sincronización sin conexión en el código, cree una clase que implemente `IMobileServiceSyncHandler`. Transfiera un objeto de este tipo en la llamada a `InitializeAsync`:

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

La clase `SyncHandler` de **MainPage.xaml.cs** implementa `IMobileServiceSyncHandler`. Se llama al método `ExecuteTableOperationAsync` cuando se envía cada operación de inserción al servidor. Si se lanza una excepción del tipo `MobileServicePreconditionFailedException`, significa que hay un conflicto entre las versiones local y remota de un elemento.

Para resolver conflictos a favor del elemento local, simplemente debe reintentar la operación. Cuando se ha producido un conflicto, la versión del elemento local se actualizará para coincidir con la versión del servidor, por lo que la ejecución de la operación de nuevo sobrescribirá los cambios del servidor con los cambios locales:

    await operation.ExecuteAsync(); 

Para resolver los conflictos a favor del elemento del servidor, simplemente vuelva de `ExecuteTableOperationAsync`. La versión local del objeto se descartará y se reemplazará por el valor del servidor.

Para detener la operación de inserción (pero mantener los cambios en cola), use el método `AbortPush()`:

    operation.AbortPush();

Esto detendrá la operación de inserción actual, pero mantendrá todos los cambios pendientes, incluida la operación actual, si se llama a `AbortPush` desde `ExecuteTableOperationAsync`. La próxima vez que se llama a `PushAsync()`, estos cambios se enviarán al servidor.

Cuando se cancela la inserción, `PushAsync` lanzará una excepción `MobileServicePushFailedException` y la propiedad de excepción `PushResult.Status` tendrá el valor `MobileServicePushStatus.CancelledByOperation`.


<!-- Anchors. -->
[Download the Windows Store app project]: #download-app
[Create the mobile service]: #create-service
[Add a due date column for the database]: #add-column
[Updating the database for .NET backend mobile services]: #dotnet-backend  
[Updating the database for JavaScript mobile services]: #javascript-backend
[Test the app against a mobile service]: #test-app
[Manually update the data in the backend to create a conflict]: #handle-conflict
[Next Steps]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
[1]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-empty.png
[2]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-empty-sql.png
[3]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-push1.png
[4]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-todowithdate-design-edit.png
[5]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-server-explorer.png
[6]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-sql-server-object-explorer-update-data.png
[7]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run2.png
[8]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run3.png




<!-- URLs -->
[Handling conflicts code sample]: http://go.microsoft.com/fwlink/?LinkId=394787
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-windows-store-get-started/
[Get started with offline data]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[Azure Management Portal]: https://manage.windowsazure.com/
[Handling Database Conflicts]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/#test-app