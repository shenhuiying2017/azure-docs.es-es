<properties
	pageTitle="Activación de la sincronización sin conexión para la aplicación de la Plataforma universal de Windows (UWP) con Aplicaciones móviles | Servicio de aplicaciones de Azure"
	description="Obtenga información acerca de cómo usar una Aplicación móvil de Azure para almacenar en caché y sincronizar datos sin conexión en la aplicación de la Plataforma universal de Windows (UWP)"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/19/2016"
	ms.author="wesmc"/>

# Activación de la sincronización sin conexión para la aplicación de Windows

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## Información general

Este tutorial muestra cómo agregar compatibilidad sin conexión a una aplicación de la Plataforma universal de Windows (UWP) mediante un back-end de aplicación móvil de Azure. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil (ver, agregar o modificar datos), incluso cuando no hay ninguna conexión de red. Los cambios se almacenan en una base de datos local; una vez que el dispositivo se vuelve a conectar, estos cambios se sincronizan con el back-end remoto.

En este tutorial, actualizará el proyecto de aplicación para UWP del tutorial [Creación de una aplicación para Windows] a fin de conseguir compatibilidad con las características sin conexión de Aplicaciones móviles de Azure. Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar paquetes de extensión de acceso de datos al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para obtener más información acerca de la característica de sincronización sin conexión, consulte el tema [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].

## Requisitos

Este tutorial requiere lo siguiente:

* Visual Studio 2013 en Windows 8.1.
* Finalización del tutorial [Creación de una aplicación para Windows][create a windows app].
* [Almacén de SQLite de servicios móviles de Azure][sqlite store nuget]
* [SQLite for Universal Windows Platform development](http://www.sqlite.org/downloads) (SQLite para el desarrollo de la Plataforma universal de Windows)

## Actualización de la aplicación cliente para que admita características sin conexión

Las características sin conexión de Aplicaciones móviles de Azure permiten interactuar con una base de datos local cuando el usuario se encuentra en un escenario sin conexión. Para usar estas características en la aplicación, inicialice [SyncContext][synccontext] en un almacén local. A continuación, obtenga una referencia a la tabla mediante la interfaz [IMobileServiceSyncTable][IMobileServiceSyncTable]. SQLite se utiliza como almacén local en el dispositivo.

1. Instale el [entorno en tiempo de ejecución de SQLite para la plataforma universal de Windows](http://sqlite.org/2016/sqlite-uwp-3120200.vsix).

2. En Visual Studio, abra el Administrador de paquetes NuGet para el proyecto de aplicación para UWP que completó en el tutorial [Creación de una aplicación para Windows] y busque e instale el paquete NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore**.

4. En el Explorador de soluciones, haga clic con el botón derecho en **Referencias** > **Agregar referencia** > **Universal Windows** > **Extensiones** y habilite **SQLite for Universal Windows Platform** y **Visual C++ 2015 Runtime for Universal Windows Platform apps**.

    ![Incorporación de referencia de SQLite UWP][1]

5. Abra el archivo MainPage.xaml.cs y quite la marca de comentario a las siguientes instrucciones `using` en la parte superior del archivo:

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  
        using Microsoft.WindowsAzure.MobileServices.Sync;         

6. Agregue una marca de comentario a la línea de código que inicializa `todoTable` como **IMobileServiceTable** y quite el comentario de la línea de código que inicializa `todoTable` como **IMobileServiceSyncTable**:

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); 

7. En la región `Offline sync` de MainPage.xaml.cs, quite la marca de comentario de los métodos siguientes:

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

	En `SyncAsync` se inicia una operación de inserción desde [SyncContext][synccontext] seguido de una sincronización incremental. El contexto de sincronización realiza un seguimiento de los cambios que hizo el cliente para todas las tablas. Para más información sobre este comportamiento, consulte [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].

8. En el controlador de eventos `OnNavigatedTo`, quite la marca de comentario de la llamada a `InitLocalStoreAsync`. Una vez completado el [tutorial de autenticación](app-service-mobile-windows-store-dotnet-get-started-users.md), debe hacer lo mismo en el método `AuthenticateAsync`.

9. Quite la marca de comentario de las llamadas a [PushAsync] en los métodos `InsertTodoItem` y `UpdateCheckedTodoItem`; después, quite la marca de la llamada a `SyncAsync` en el método `ButtonRefresh_Click`.

10. En el método `SyncAsync`, agregue los siguientes controladores de excepciones:

        private async Task SyncAsync()
        {
            String errorString = null;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
                await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); 
            }

            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors. You may be offine.\nMessage: " +
                  ex.Message + "\nPushResult.Status: " + ex.PushResult.Status.ToString();
            }
            catch (Exception ex)
            {
                errorString = "Pull failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your pull again when connected with your backend.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }
        }

	En una situación sin conexión, [PullAsync][PullAsync] puede dar lugar a [MobileServicePushFailedException][MobileServicePushFailedException] con [PushResult.Status][Status] de [CancelledByNetworkError][CancelledByNetworkError]. Esto ocurre cuando una inserción implícita intenta insertar actualizaciones pendientes antes de la extracción y se produce un error. Para obtener más información, consulte [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].

11. En Visual Studio, presione la tecla **F5** para volver a compilar y ejecutar la aplicación cliente. La aplicación funciona igual que lo hacía antes de habilitar la sincronización sin conexión. Sin embargo, la base de datos se rellena con datos que pueden utilizarse en un escenario sin conexión. A continuación, creará un escenario sin conexión y utilizará datos almacenados localmente para iniciar la aplicación.

## <a name="update-sync"></a>Actualización de la aplicación para desconectarla del back-end

En esta sección, se interrumpe la conexión con el back-end de aplicación móvil para simular un escenario sin conexión. Al agregar elementos de datos, el controlador de excepciones le indicará que la aplicación está en modo sin conexión. En este estado, se agregan nuevos elementos al almacén local y se sincronizarán con el back-end de aplicación móvil cuando se vuelva a ejecutar la inserción en estado conectado.

1. Edite App.xaml.cs en el proyecto compartido. Convierta en comentario la inicialización de **MobileServiceClient** y agregue las siguientes líneas, que utilizan una dirección URL de una aplicación móvil no válida:

         public static MobileServiceClient MobileService =
				new MobileServiceClient("https://your-service.azurewebsites.fail");

	Tenga en cuenta que, cuando la aplicación también usa autenticación, el inicio de sesión dará error. Además, puede mostrar el comportamiento sin conexión deshabilitando las redes Wi-Fi y móvil en el dispositivo o usar el modo avión.

2. Presione **F5** para compilar y ejecutar la aplicación. Observe que la sincronización no se pudo actualizar cuando se inició la aplicación.
 
3. Escriba nuevos elementos y observe que la operación de inserción produce un error con un estado de [CancelledByNetworkError] cada vez que hace clic en **Guardar**. No obstante, los nuevos elementos Todo están en el almacén local hasta que se puedan insertar en el back-end de aplicación móvil.

	En una aplicación de producción, si suprime estas excepciones, la aplicación cliente se comporta como si aún estuviera conectada al back-end de aplicación móvil.

4. Cierre la aplicación y reiníciela para comprobar que los nuevos elementos que creó se mantienen en el almacén local.

5. (Opcional) En Visual Studio, abra el **Explorador de servidores**. Vaya a la base de datos en **Azure**->**Bases de datos SQL**. Haga clic con el botón derecho en la base de datos y seleccione **Abrir en el Explorador de objetos de SQL Server**. Ahora puede buscar la tabla de base de datos SQL y su contenido. Compruebe que no han cambiado los datos de la base de datos back-end.

6. (Opcional) Use una herramienta REST como Fiddler o Postman para consultar el back-end móvil mediante una consulta GET con la forma `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Actualización de la aplicación para volver a conectar el back-end de la aplicación móvil

En esta sección se vuelve a conectar al back-end de aplicación móvil. De este modo se simula que la aplicación pasa de un estado sin conexión a un estado en línea con el back-ende de aplicación móvil. Cuando ejecute la aplicación por primera vez, el controlador de eventos `OnNavigatedTo` llamará a `InitLocalStoreAsync`. Este llamará a su vez a `SyncAsync` para sincronizar el almacén local con la base de datos de back-end. Por lo tanto, la aplicación intentará sincronizarse en el inicio.

1. Abra App.xaml.cs en el proyecto compartido y quite la marca de comentario a la inicialización anterior de `MobileServiceClient` para usar la dirección URL de aplicación móvil correcta.

2. Presione la tecla **F5** para volver a compilar y ejecutar el proyecto. La aplicación sincroniza los cambios locales con el back-end de la aplicación móvil de Azure mediante las operaciones de inserción y extracción una vez que se ejecuta el controlador de eventos `OnNavigatedTo`.

3. (Opcional) Vea los datos actualizados mediante el Explorador de objetos de SQL Server o una herramienta REST como Fiddler. Observe que los datos se han sincronizado entre la base de datos del back-end de la aplicación móvil de Azure y el almacén local.

4. En la aplicación, haga clic en la casilla situada junto a algunos elementos para completarlos en el almacén local.

  `UpdateCheckedTodoItem` llama a `SyncAsync` para sincronizar cada elemento completo con el back-end de aplicación móvil. `SyncAsync` llama a las operaciones de inserción y extracción. Sin embargo, tenga en cuenta que **cada vez que se ejecuta una extracción en una tabla en la que el cliente ha realizado cambios, primero se ejecuta una inserción de forma automática en el contexto de sincronización de cliente**. De este modo, se asegurará de que todas las tablas del almacén local, junto con las relaciones, sean coherentes. Así que, en este caso, podríamos haber eliminado la llamada a `PushAsync` porque esta se ejecuta automáticamente cuando se ejecuta una extracción. Este comportamiento puede producir una inserción inesperada si no se está al tanto de la operación. Para más información sobre este comportamiento, consulte [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].


##Resumen de la API

Para admitir las características sin conexión de Servicios móviles, se usa la interfaz [IMobileServiceSyncTable] y se inicializa [MobileServiceClient.SyncContext][synccontext] con una base de datos SQLite local. En el modo sin conexión, las operaciones CRUD normales para Aplicaciones móviles funcionan como si la aplicación siguiera conectada y todas las operaciones se producen en el almacén local. Cuando se quiera sincronizar el almacén local con el servidor, se usan los métodos siguientes:

*  **[PushAsync]**:  
   como este método es miembro de [IMobileServicesSyncContext], los cambios de todas las tablas se insertan en el back-end. Solo se envían al servidor los registros con cambios locales.

* **[PullAsync]**:    
   se inicia una extracción desde [IMobileServiceSyncTable]. Cuando haya cambios marcados en la tabla, se ejecuta una inserción implícita para asegurarse de que todas las tablas del almacén local junto con las relaciones siguen siendo coherentes. El parámetro *pushOtherTables* controla si se insertan otras tablas del contexto en una inserción implícita. El parámetro *query* toma [IMobileServiceTableQuery&lt;U&gt;][IMobileServiceTableQuery] o la cadena de consulta de OData para filtrar los datos devueltos. El parámetro *queryId* se utiliza para definir la sincronización incremental. Para obtener más información, consulte [Sincronización de datos sin conexión en Aplicaciones móviles de Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

* **[PurgeAsync]**:  
   su aplicación debe llamar periódicamente a este método para purgar datos obsoletos del almacén local. Utilice el parámetro *force* cuando necesite purgar todos los cambios que aún no se hayan sincronizado.

Para obtener más información sobre estos conceptos, consulte [Sincronización de datos sin conexión en Aplicaciones móviles de Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

## Más información

En los temas siguientes se ofrece información de fondo adicional sobre la característica de sincronización sin conexión de Aplicaciones móviles:

* [Sincronización de datos sin conexión en Aplicaciones móviles de Azure]
* [Cloud Cover: sincronización sin conexión en Servicios móviles de Azure] (tenga en cuenta que el vídeo es para Servicios móviles, pero la sincronización sin conexión funciona de forma similar en Aplicaciones móviles de Azure)
* [Azure Friday: Aplicaciones habilitadas sin conexión en Servicios móviles de Azure]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Sincronización de datos sin conexión en Aplicaciones móviles de Azure]: app-service-mobile-offline-data-sync.md
[create a windows app]: app-service-mobile-windows-store-dotnet-get-started.md
[Creación de una aplicación para Windows]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[Cloud Cover: sincronización sin conexión en Servicios móviles de Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Aplicaciones habilitadas sin conexión en Servicios móviles de Azure]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_0824_2016-->