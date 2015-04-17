<properties
    pageTitle="Activación de la sincronización sin conexión para la aplicación móvil (Xamarin iOS)"
    description="Obtenga información acerca de cómo usar la aplicación móvil de Servicios de aplicaciones para almacenar en caché y sincronizar datos sin conexión en su aplicación Xamarin iOS."
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="03/26/2015"
    ms.author="donnam"/>

# Activación de la sincronización sin conexión para la aplicación móvil Xamarin iOS

<!-- [AZURE.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]
 -->

Este tutorial explica la característica de sincronización sin conexión de Aplicaciones móviles para iOS. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil (ver, agregar o modificar datos), incluso cuando no hay ninguna conexión de red. Los cambios se almacenan en una base de datos local; una vez que el dispositivo se vuelve a conectar, estos cambios se sincronizan con el servicio remoto.

La sincronización sin conexión tiene varios usos posibles:

* Mejore la capacidad de respuesta de las aplicaciones almacenando en caché datos de servidor de forma local en el dispositivo.
* Hacer que las aplicaciones sean resistentes ante una conectividad de red intermitente.
* Permitir a los usuarios finales crear y modificar datos incluso cuando no hay acceso de red, proporcionando escenarios con muy poca o ninguna conectividad.
* Sincronizar datos entre diferentes dispositivos y detectar conflictos cuando dos dispositivos modifican el mismo registro.

Si esta es la primera vez que usa Aplicaciones móviles, complete el tutorial [Creación de una aplicación Xamarin iOS].

Este tutorial requiere lo siguiente:

* Visual Studio 2013
* Visual Studio con la [extensión Xamarin]** o **[Xamarin Studio] en OS X.

## Revisión del código de sincronización de Aplicaciones móviles

La sincronización sin conexión de Aplicaciones móviles de Azure permite a los usuarios finales interactuar con una base de datos local cuando la red no está accesible. Para usar estas características en la aplicación, inicialice `MobileServiceClient.SyncContext` en un almacén local. A continuación, obtenga una referencia a la tabla mediante la interfaz `IMobileServiceSyncTable`.
Esta sección le guía a través del código relacionado con la sincronización sin conexión en `QSTodoService.cs`.

1. En Visual Studio, abra el proyecto que completó en el tutorial [Introducción a las aplicaciones móviles]. Abra el archivo `QSTodoService.cs`.

2. Tenga en cuenta que el tipo del miembro `todoTable` es `IMobileServiceSyncTable`. La sincronización sin conexión usa esta interfaz de tabla de sincronización en lugar de `IMobileServiceTable`. Cuando se utiliza una tabla de sincronización, todas las operaciones van al almacén local y solo se sincronizan con el servicio remoto con operaciones de inserción y extracción explícitas.

    Para obtener una referencia a una tabla de sincronización, se utiliza el método `GetSyncTable()`. Para quitar la funcionalidad de sincronización sin conexión, se utilizaría en su lugar `GetTable()`.

3. Antes de poder realizar cualquier operación de tabla, se debe inicializar el almacén local. Esto se hace en el método `InitializeStoreAsync`:

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }

    Esto crea un almacén local mediante la clase `MobileServiceSQLiteStore`, que se proporciona en el SDK de Servicios móviles. También puede proporcionar una implementación de otro almacén local mediante la implementación de `IMobileServiceLocalStore`.

    El método `DefineTable` crea una tabla en el almacén local que coincide con los campos del tipo proporcionado, `ToDoItem` en este caso. El tipo no tiene que incluir todas las columnas que se encuentran en la base de datos remota, es posible almacenar solo un subconjunto de columnas.

<!--     Esta sobrecarga de `InitializeAsync` utiliza el controlador de conflictos predeterminado, que produce un error cuando hay un conflicto. Para proporcionar un controlador de conflictos personalizado, consulte el tutorial [Control de conflictos con compatibilidad sin conexión para Servicios móviles].
 -->
4. El método `SyncAsync` desencadena la operación de sincronización real:

        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

    En primer lugar, hay una llamada a `IMobileServiceSyncContext.PushAsync()`. Este método es miembro de `IMobileServicesSyncContext` y no de la tabla de sincronización porque insertará cambios en todas las tablas. Solo los registros que se han modificado localmente de alguna forma (mediante operaciones CUD) se enviarán al servidor.

    A continuación, el método llama a `IMobileServiceSyncTable.PullAsync()` para extraer datos de una tabla en el servidor a la aplicación. Tenga en cuenta que si hay cambios pendientes en el contexto de sincronización, una extracción siempre realiza primero una inserción. De este modo, se asegurará de que permanezcan sincronizadas todas las tablas del almacén local con las relaciones. En este caso, hemos llamado a la inserción explícitamente.

    En este ejemplo, recuperamos todos los registros de la tabla `TodoItem` remota, pero también es posible filtrar registros pasando una consulta. El primer parámetro para `PullAsync()` es un identificador de consulta que se utiliza en la sincronización incremental, que utiliza la marca de tiempo `UpdatedAt` para obtener solo aquellos registros modificados desde la última sincronización. El identificador de la consulta debe ser una cadena descriptiva que sea única para cada consulta lógica en la aplicación. Para la desactivación de la sincronización incremental, pase  `null` como identificador de la consulta. Así se recuperarán todos los registros de cada operación de extracción, lo cual es potencialmente ineficaz.

<!--     >[AZURE.NOTE] Para quitar registros del almacén local del dispositivo cuando se han eliminado de la base de datos del servicio móvil, debe habilitar la [eliminación temporal]. De lo contrario, la aplicación podría llamar periódicamente a `IMobileServiceSyncTable.PurgeAsync()` para purgar el almacén local.

    Tenga en cuenta que `MobileServicePushFailedException` puede producirse por una operación de inserción y una de extracción. El siguiente tutorial, [Control de conflictos con compatibilidad sin conexión para servicios móviles], muestra cómo manejar estas excepciones relacionadas con la sincronización.
-->

5. En la clase `QSTodoService`, se llama al método `SyncAsync()` después de las operaciones que modifican datos, `InsertTodoItemAsync()` y `CompleteItemAsync`. También se le llama desde `RefreshDataAsync()`, de modo que el usuario obtiene los datos más recientes cada vez que realizan los gestos de actualización. La aplicación también lleva a cabo una sincronización en el inicio, ya que `QSTodoListViewController.ViewDidLoad()` llama a `RefreshDataAsync()`.

    Dado que `SyncAsync()` se llama cada vez que se modifican datos, esta aplicación supone que el usuario está en línea siempre que se editan datos. En la siguiente sección, se actualizará la aplicación para que los usuarios puedan editar incluso cuando estén sin conexión.

## Actualización del comportamiento de sincronización de la aplicación

En esta sección, modificará la aplicación para que no se sincronice en el inicio de la aplicación o en las operaciones de inserción o actualización, sino solo cuando se presione el botón Actualizar. De este modo, interrumpirá la conexión de la aplicación con el back-end móvil para simular un escenario sin conexión. Al agregar elementos de datos, estos se mantendrán en el almacén local, pero no se sincronizarán de inmediato con el almacén de datos de back-end móvil.

1. Abra `QSTodoService.cs`. Comente las llamadas a `SyncAsync()` en los siguientes métodos:

    - `InsertTodoItemAsync`
    - `CompleteItemAsync`
    - `RefreshAsync`

    Ahora, `RefreshAsync()` solo cargará los datos del almacén local, pero no podrá conectar con el back-end de aplicación.

2. En `QSTodoService.cs`, cambie la definición de `applicationURL` para que apunte a un URI de aplicación móvil no válido:

        const string applicationURL = @"https://your-service.azurewebsites.xxx/"; // invalid URI

3. Para asegurarse de que los datos se sincronizan cuando se realiza el movimiento de actualización, modifique el método `QSTodoListViewController.RefreshAsync()`. Agregue una llamada a `SyncAsync()` antes de llamar a `RefreshDataAsync()`:

        private async Task RefreshAsync ()
        {
            RefreshControl.BeginRefreshing ();

            await todoService.SyncAsync();
            await todoService.RefreshDataAsync (); // add this line

            RefreshControl.EndRefreshing ();

            TableView.ReloadData ();
        }

4. Compile y ejecute la aplicación. Agregue nuevos elementos a la lista de tareas. Estos nuevos elementos solo existirán en el almacén local hasta que se puedan insertar en el back-end móvil. La aplicación cliente se comporta como si estuviera conectada al back-end y admite todas las operaciones de creación, lectura, actualización y eliminación (CRUD).

5. Cierre la aplicación y reiníciela para comprobar que los nuevos elementos que creó se mantienen en el almacén local.

## Actualización de la aplicación para volver a conectar el back-end móvil

En esta sección se volverá a conectar la aplicación al back-end móvil, que simula que la aplicación vuelve a un estado en línea. Al realizar el gesto de la actualización, los datos se sincronizarán con el back-end móvil.

1. Abra `QSTodoService.cs`. Quite la dirección URL de la aplicación móvil no válida y vuelva a agregar la clave de dirección URL y aplicación correctas.

2. Recompile y ejecute la aplicación. Observe que los datos no han cambiado, a pesar de que la aplicación ahora está conectada al back-end móvil. Esto se debe a que la aplicación siempre funciona con el elemento `IMobileServiceSyncTable` que apunta al almacén local.

3. Conéctese a la base de datos SQL del back-end para ver los datos que se ha almacenado. En Visual Studio, vaya a **Explorador de servidores** -> **Azure** -> **Bases de datos SQL**. Haga clic con el botón derecho en la base de datos y seleccione **Abrir en el Explorador de objetos de SQL Server**.

    Observe que los datos  *not* se han sincronizado entre la base de datos y el almacén local.

4. En la aplicación, realice el gesto de actualización desplegando la lista de elementos. Esto hace que la aplicación llame a `RefreshDataAsync()`, que a su vez llama a `SyncAsync()`. Esto llevará a cabo las operaciones de inserción y extracción, primero enviando los elementos del almacén local al back-end móvil y después recuperando nuevos datos desde el back-end.

5. Actualice la vista de base de datos y confirme que los cambios se han sincronizado.

<!-- ##Resumen

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../includes/mobile-services-offline-summary-csharp.md)]

## Pasos siguientes

* [Control de conflictos con compatibilidad sin conexión para servicios móviles]

* [Uso del cliente del componente Xamarin para servicios móviles de Azure]
 -->

<!-- Images -->

<!-- URLs. -->
[Creación de una aplicación Xamarin iOS]: app-service-mobile-dotnet-backend-xamarin-ios-get-started.md

[Uso del cliente del componente Xamarin para servicios móviles de Azure]: partner-xamarin-mobile-services-how-to-use-client-library.md

[Xamarin Studio]: http://xamarin.com/download
[Extensión de Xamarin]: http://xamarin.com/visual-studio

<!--HONumber=49-->