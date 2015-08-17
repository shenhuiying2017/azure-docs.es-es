<properties
	pageTitle="Uso de datos sin conexión con Aplicaciones móviles de Azure (Tienda Windows) | Centro de desarrollo móvil"
	description="Obtenga información acerca de cómo usar Aplicaciones móviles de Azure para almacenar en caché y sincronizar datos sin conexión en su aplicación de la Tienda Windows"
	documentationCenter="windows"
	authors="christopheranderson"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/22/2015"
	ms.author="chrande"/>

# Uso de la sincronización de datos sin conexión en Aplicaciones móviles de Azure

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]


Este tutorial muestra cómo agregar compatibilidad sin conexión a una aplicación de Tienda universal Windows mediante un back-end de Aplicaciones móviles de Azure. La compatibilidad sin conexión permitirá interactuar con una base de datos local cuando la aplicación no disponga de conexión. Cuando la aplicación esté conectada a la base de datos de back-end, deberá sincronizar los cambios locales mediante las características sin conexión.



En este tutorial, actualizará el proyecto de aplicación universal del tutorial [Creación de una aplicación para Windows] para conseguir compatibilidad con las características sin conexión de Aplicaciones móviles de Azure. A continuación, agregará datos en un escenario sin conexión desconectado, sincronizará dichos elementos con la base de datos en línea e iniciará sesión en el Portal de administración de Azure para ver los cambios efectuados en los datos al ejecutar la aplicación.

Este tutorial le guiará a través de estos pasos básicos:

1. [Actualización de la aplicación para que admita características sin conexión]
2. [Actualización del comportamiento de sincronización de la aplicación]
3. [Actualización de la aplicación para volver a conectar el back-end de Aplicaciones móviles]

Este tutorial requiere lo siguiente:

* Visual Studio 2013 en Windows 8.1.
* Finalización del tutorial [Creación de una aplicación para Windows][create a windows app].
* [SDK de Servicios móviles de Azure versión 2.0.0 (o posterior)][azure mobile app sdk nuget]
* [Almacén de SQLite de Servicios móviles de Azure versión 1.0.2 (o posterior)][sqlite store nuget]
* [SQLite para Windows 8.1](www.sqlite.org/downloads)

>[AZURE.NOTE]Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Evaluación gratuita de Azure</a>.

##<a name="review"></a>Revisión de la configuración del proyecto de servidor (opcional)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-offline-preview](../../includes/app-service-mobile-dotnet-backend-enable-offline-preview.md)]

## <a name="enable-offline-app"></a>Actualización de la aplicación para que admita características sin conexión

Las características sin conexión de Aplicaciones móviles de Azure permiten interactuar con una base de datos local cuando el usuario se encuentra en un escenario sin conexión con su servicio móvil. Para usar estas características en la aplicación, inicialice `MobileServiceClient.SyncContext` en un almacén local. A continuación, obtenga una referencia a la tabla mediante la interfaz `IMobileServiceSyncTable`. En este tutorial se utiliza SQLite para el almacén local.

1. Instale el tiempo de ejecución de SQLite para Windows 8.1 y Windows Phone 8.1.

    * **Tiempo de ejecución de Windows 8.1:** Instalar [SQLite para Windows 8.1].
    * **Windows Phone 8.1:** Instalar [SQLite para Windows Phone 8.1].

    >[AZURE.NOTE]Si utiliza Internet Explorer, es posible que al hacer clic en el vínculo para instalar SQLite se le solicite descargar .vsix como archivo .zip. Guarde el archivo en una ubicación del disco duro con la extensión .vsix en lugar de .zip. A continuación, haga doble clic en el archivo .vsix en el Explorador de Windows para ejecutar la instalación.

2. En Visual Studio, abra el proyecto que completó en el tutorial [Creación de una aplicación para Windows]. Instale paquete NuGet **WindowsAzure.MobileServices.SQLiteStore** para el tiempo de ejecución de Windows 8.1 y los proyectos de Windows Phone 8.1.

    * **Windows 8.1:** En el Explorador de soluciones, haga clic con el botón secundario del mouse en el proyecto de Windows 8.1 y haga clic en **Administrar paquetes de Nuget** para ejecutar el Administrador de paquetes de NuGet. Busque **SQLiteStore** para instalar el paquete `WindowsAzure.MobileServices.SQLiteStore`.
    * **Windows Phone 8.1:** Haga clic con el botón secundario del mouse en el proyecto de Windows Phone 8.1 y haga clic en **Administrar paquetes de Nuget** para ejecutar el Administrador de paquetes de NuGet. Busque **SQLiteStore** para instalar el paquete `WindowsAzure.MobileServices.SQLiteStore`.

    >[AZURE.NOTE]Si la instalación crea una referencia a una versión anterior de SQLite, solo podrá eliminar dicha referencia duplicada.

3. En el Explorador de soluciones, haga clic con el botón derecho en **Referencias** para el tiempo de ejecución de Windows 8.1 y los proyectos de la plataforma Windows Phone 8.1 y asegúrese de que hay una referencia a SQLite, que se encuentra en la sección **Extensiones**.

    ![][1] </br>

    **Tiempo de ejecución de Windows 8.1**

    ![][11] </br>

    **Windows Phone 8.1**

4. El tiempo de ejecución de SQLite requiere cambiar la arquitectura del proyecto que se va a crear a **x86**, **x64** o **ARM**. No se admite **cualquier CPU**. En el Explorador de soluciones, haga clic en Solución que aparece en la parte superior, luego cambie el cuadro desplegable de la arquitectura del procesador a uno de la configuración compatible que desea probar.

    ![][13]

5. En el Explorador de soluciones, en el proyecto compartido, abra el archivo MainPage.cs. Quite la marca de comentario de las siguientes instrucciones using en la parte superior del archivo:

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. En MainPage.cs, comente la definición de `todoTable` y quite la marca de comentario de la que aparece en la línea siguiente que llama a `MobileServicesClient.GetSyncTable()`:

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync

7. En MainPage.cs, en el área marcada como `Offline sync`, quite los comentarios de los métodos `InitLocalStoreAsync` y `SyncAsync`. El método `InitLocalStoreAsync` inicializa el contexto de sincronización de cliente con un almacén SQLite.

        private async Task InitLocalStoreAsync()
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localstore.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store);
            }

            await SyncAsync();
        }

        private async Task SyncAsync()
        {
            await App.MobileService.SyncContext.PushAsync();
            await todoTable.PullAsync("todoItems", todoTable.CreateQuery());
        }

8. En el controlador de eventos `OnNavigatedTo`, quite el comentario de la llamada a `InitLocalStoreAsync`:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await InitLocalStoreAsync(); // offline sync
            await RefreshTodoItems();
        }

9. Quite la marca de comentario de las 3 llamadas a `SyncAsync` en los métodos `InsertTodoItem`, `UpdateCheckedTodoItem` y `ButtonRefresh_Click`:

        private async Task InsertTodoItem(TodoItem todoItem)
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);

            await SyncAsync(); // offline sync
        }

        private async Task UpdateCheckedTodoItem(TodoItem item)
        {
            await todoTable.UpdateAsync(item);
            items.Remove(item);
            ListItems.Focus(Windows.UI.Xaml.FocusState.Unfocused);

            await SyncAsync(); // offline sync
        }

        private async void ButtonRefresh_Click(object sender, RoutedEventArgs e)
        {
            ButtonRefresh.IsEnabled = false;

            await SyncAsync(); // offline sync
            await RefreshTodoItems();

            ButtonRefresh.IsEnabled = true;
        }

10. Agregue controladores de excepciones en el método `SyncAsync`:

        private async Task SyncAsync()
        {
            String errorString = null;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
                await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); // first param is query ID, used for incremental sync
            }

            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " +
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Pull failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Pull again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }
        }

    En este ejemplo, recuperamos todos los registros en el `todoTable` remoto, pero también es posible filtrar registros pasando una consulta. El primer parámetro para `PullAsync` es un identificador de consulta que se utiliza en la sincronización incremental, que utiliza la marca de tiempo `UpdatedAt` para obtener únicamente los registros modificados desde la última sincronización. El identificador de la consulta debe ser una cadena descriptiva que sea única para cada consulta lógica en la aplicación. Para la desactivación de la sincronización incremental, pase `null` como identificador de la consulta. Así se recuperarán todos los registros de cada operación de extracción, lo cual es potencialmente ineficaz.

    Tenga en cuenta que `MobileServicePushFailedException` puede producirse por una operación de inserción y una de extracción. Puede producirse una extracción porque la operación de extracción ejecuta internamente una inserción para asegurarse de que todas las tablas junto con las relaciones son coherentes.

11. En Visual Studio, presione la tecla **F5** para volver a realizar la compilación y ejecute la aplicación. La aplicación se comportará igual que antes de que cambiara la sincronización sin conexión, porque realiza una operación de sincronización en las operaciones de inserción y actualización.

## <a name="update-sync"></a>Actualización del comportamiento de sincronización de la aplicación

En esta sección, modificará la aplicación para que no se sincronice en las operaciones de inserción y actualización, sino solo cuando esté presionado el botón **Actualizar**. De este modo, interrumpirá la conexión de la aplicación con el back-end de aplicación móvil para simular un escenario sin conexión. Al agregar elementos de datos, estos se mantendrán en el almacén local, pero no se sincronizarán con el back-end de aplicación móvil.

1. En el proyecto compartido, abra App.xaml.cs. Modifique los métodos `InsertTodoItem` y `UpdateCheckedTodoItem` para convertir en comentario las llamadas a `SyncAsync`.

2. Edite App.xaml.cs en el proyecto compartido. Convierta en comentario la inicialización de **MobileServiceClient** y agregue las siguientes líneas, que utilizan una dirección URL de una aplicación móvil no válida:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://<your-mobile-service>-code.azurewebsites.fail",
            "https://<your-mobile-service>gateway.azurewebsites.fail",
            "AppKey"
        );

3. En `InitLocalStoreAsync()`, convierta en comentario la llamada a `SyncAsync()`, de modo que la aplicación no realice una sincronización en el inicio.

4. Presione **F5** para compilar y ejecutar la aplicación. Escriba algunos nuevos elementos de lista de tareas y haga clic en **Guardar** para cada uno. Los nuevos elementos todo solo están en el almacén local hasta que se puedan insertar en el back-end de aplicación móvil. La aplicación cliente se comporta como si estuviera conectada al back-end de aplicación móvil que admite todas las operaciones de creación, lectura, actualización y eliminación (CRUD).

5. Cierre la aplicación y reiníciela para comprobar que los nuevos elementos que creó se mantienen en el almacén local.

## <a name="update-online-app"></a>Actualización de la aplicación para volver a conectar el back-end de aplicación móvil

En esta sección se vuelve a conectar al back-end de aplicación móvil. De este modo se simula que la aplicación pasa de un estado sin conexión a un estado en línea con el back-ende de aplicación móvil. Cuando se presione el botón de actualización, los datos se sincronizarán con el back-end de aplicación móvil.

1. En el proyecto compartido, abra App.xaml.cs. Quite la marca de comentario de la inicialización anterior de `MobileServiceClient` para volver a agregar la clave de aplicación, la dirección URL de la puerta de enlace y la dirección URL del back-end de servicio de aplicaciones móviles correctas.

2. Presione la tecla **F5** para volver a compilar y ejecutar el proyecto. Observe que los datos tienen el mismo aspecto que en el escenario sin conexión aunque la aplicación ahora está conectada con el back-end de aplicación móvil. Esto se debe a que la aplicación siempre funciona con el elemento `IMobileServiceSyncTable` que apunta al almacén local.

3. Inicie sesión en el portal de Azure y consulte la base de datos del back-end de aplicación móvil.

    En Visual Studio, vaya a **Explorador de servidores** -> **Azure** -> **Bases de datos SQL**. Haga clic con el botón derecho en la base de datos y seleccione **Abrir en el Explorador de objetos de SQL Server**.

    Observe que los datos no se han sincronizado entre la base de datos y el almacén local.

4. En la aplicación, presione el botón **Actualizar**. Esto hace que la aplicación llame a `PushAsync` y `PullAsync`. Esta operación de inserción envía los elementos de almacén local al servicio móvil, y a continuación, recupera los nuevos datos del servicio móvil.

    Una operación de inserción se ejecuta de `MobileServiceClient.SyncContext` y no de `IMobileServicesSyncTable`, e inserta cambios en todas las tablas asociadas a dicho contexto de sincronización. Esto tiene por objeto incluir escenarios en los que existen relaciones entre tablas.

5. En la aplicación, haga clic en la casilla situada junto a algunos elementos para completarlos en el almacén local.

6. Inserte de nuevo el botón **Actualizar**, lo que hace que se llame a `SyncAsync`. `SyncAsync` llama tanto a inserción como a extracción; sin embargo, en este caso, podríamos haber eliminado la llamada a `PushAsync`. Esto se debe a que toda **extracción siempre realiza primero una inserción**. De este modo, se asegurará de que todas las tablas del almacén local sean coherentes con las relaciones.


##Resumen

Para admitir las características sin conexión de servicios móviles, usamos la interfaz `IMobileServiceSyncTable` e inicializamos `MobileServiceClient.SyncContext` con un almacén local. En este caso, el almacén local era una base de datos SQLite.

Las operaciones CRUD normales para servicios móviles funcionan como si la aplicación siguiera conectada, pero todas las operaciones se producen en el almacén local.

Cuando quisimos sincronizar el almacén local con el servidor, usamos los métodos `IMobileServiceSyncTable.PullAsync` y `MobileServiceClient.SyncContext.PushAsync`.

*  Para insertar cambios en el servidor, llamamos a `IMobileServiceSyncContext.PushAsync()`. Este método es miembro de `IMobileServicesSyncContext` y no de la tabla de sincronización porque insertará cambios en todas las tablas.

    Solo los registros que se han modificado localmente de alguna forma (mediante operaciones CUD) se enviarán al servidor.

* Para extraer datos de una tabla del servidor en la aplicación, llamamos a `IMobileServiceSyncTable.PullAsync`.

    Una extracción siempre realiza primero una inserción. De este modo, se asegurará de que todas las tablas del almacén local sean coherentes con las relaciones.

    También hay sobrecargas de `PullAsync()` que permiten que una consulta se especifique para filtrar los datos que se almacenan en el cliente. Si no se pasa ninguna consulta, `PullAsync()` extraerá todas las filas de la tabla (o consulta) correspondiente. Puede pasar la consulta para filtrar solo los cambios con los que su aplicación debe sincronizarse.

* Para habilitar la sincronización incremental, pase un identificador de consulta a `PullAsync()`. El identificador de consulta se utiliza para almacenar la última marca de tiempo actualizada de los resultados de la última operación de extracción. El identificador de la consulta debe ser una cadena descriptiva que sea única para cada consulta lógica en la aplicación. Si la consulta tiene un parámetro, el mismo valor de parámetro debe formar parte del identificador de consulta.

    Por ejemplo, si está filtrando en userid, debe formar parte del identificador de consulta:

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    Si desea cancelar la sincronización incremental, pase `null` como identificador de consulta. En este caso, se recuperarán todos los registros en cada llamada a `PullAsync`, que es potencialmente ineficaz.

* De lo contrario, la aplicación podría llamar periódicamente a `IMobileServiceSyncTable.PurgeAsync()` para purgar el almacén local.


<!-- Anchors. -->
[Actualización de la aplicación para que admita características sin conexión]: #enable-offline-app
[Actualización del comportamiento de sincronización de la aplicación]: #update-sync
[Actualización de la aplicación para volver a conectar el back-end de Aplicaciones móviles]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/cpu-architecture.png


<!-- URLs. -->
[todolist offline sample]: http://go.microsoft.com/fwlink/?LinkId=394777
[create a windows app]: /documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/
[Creación de una aplicación para Windows]: /documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/
[SQLite para Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite para Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953

[azure mobile app sdk nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/2.0.0-beta
[sqlite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.2
 

<!---HONumber=August15_HO6-->