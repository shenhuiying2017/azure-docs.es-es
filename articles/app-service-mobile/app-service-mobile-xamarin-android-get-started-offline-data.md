---
title: "Activación de la sincronización sin conexión para la aplicación móvil de Azure (Xamarin Android)"
description: "Obtenga información acerca de cómo usar la aplicación móvil del Servicio de aplicaciones para almacenar en caché y sincronizar datos sin conexión en su aplicación Xamarin Android."
documentationcenter: xamarin
author: ggailey777
manager: syntaxc4
editor: 
services: app-service\mobile
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 471433c7ef2f6f128210ed145f685b42b44eea92
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Activación de la sincronización sin conexión para la aplicación móvil Xamarin.Android
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Información general
Este tutorial presenta la característica de sincronización sin conexión de Aplicaciones móviles de Azure para Xamarin.Android. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil (ver, agregar o modificar datos), incluso cuando no hay ninguna conexión de red. Los cambios se almacenan en una base de datos local.
Una vez que el dispositivo se vuelve a conectar, estos cambios se sincronizan con el servicio remoto.

En este tutorial, se actualiza el proyecto de cliente del tutorial [Creación de una aplicación Xamarin.Android] para conseguir compatibilidad con las características sin conexión de Azure Mobile Apps. Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar paquetes de extensión de acceso de datos al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para obtener más información acerca de la característica de sincronización sin conexión, consulte el tema [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Actualización de la aplicación cliente para que admita características sin conexión
Las características sin conexión de Aplicaciones móviles de Azure permiten interactuar con una base de datos local cuando el usuario se encuentra en un escenario sin conexión. Para usar estas características en una aplicación, inicialice [SyncContext] en un almacén local. A continuación, haga referencia a su tabla mediante la interfaz [IMobileServiceSyncTable][IMobileServiceSyncTable]. SQLite se utiliza como almacén local en el dispositivo.

1. En Visual Studio, abra el administrador de paquetes NuGet en el proyecto que completó en el tutorial [Creación de una aplicación Xamarin.Android].  Busque e instale el paquete NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore**.
2. Abra el archivo ToDoActivity.cs y quite la marca de comentario de la definición de `#define OFFLINE_SYNC_ENABLED`.
3. En Visual Studio, presione la tecla **F5** para volver a compilar y ejecutar la aplicación cliente. La aplicación funciona igual que lo hacía antes de habilitar la sincronización sin conexión. Sin embargo, la base de datos se rellena con datos que pueden utilizarse en un escenario sin conexión.

## <a name="update-sync"></a>Actualización de la aplicación para desconectarla del back-end
En esta sección, se interrumpe la conexión con el back-end de aplicación móvil para simular un escenario sin conexión. Al agregar elementos de datos, el controlador de excepciones le indicará que la aplicación está en modo sin conexión. En este estado, se agregan nuevos elementos al almacén local y se sincronizan con el back-end de la aplicación móvil cuando se ejecute una inserción en estado conectado.

1. Edite ToDoActivity.cs en el proyecto compartido. Cambie **applicationURL** para que apunte a una dirección URL no válida:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Además, puede mostrar el comportamiento sin conexión mediante la deshabilitación de las redes Wi-Fi y móvil en el dispositivo o el uso del modo avión.
2. Presione **F5** para compilar y ejecutar la aplicación. Observe que la sincronización no se pudo actualizar cuando se inició la aplicación.
3. Escriba nuevos elementos y observe que la operación de inserción produce un error con un estado de [CancelledByNetworkError] cada vez que hace clic en **Guardar**. No obstante, los nuevos elementos Todo están en el almacén local hasta que se puedan insertar en el back-end de aplicación móvil.  En una aplicación de producción, si suprime estas excepciones, la aplicación cliente se comporta como si aún estuviera conectada al back-end de aplicación móvil.
4. Cierre la aplicación y reiníciela para comprobar que los nuevos elementos que creó se mantienen en el almacén local.
5. (Opcional) En Visual Studio, abra el **Explorador de servidores**. Vaya a la base de datos en **Azure**->**SQL Databases**. Haga clic con el botón derecho en la base de datos y seleccione **Abrir en el Explorador de objetos de SQL Server**. Ahora puede buscar la tabla de base de datos SQL y su contenido. Compruebe que no han cambiado los datos de la base de datos back-end.
6. (Opcional) Use una herramienta REST como Fiddler o Postman para consultar el back-end móvil mediante una consulta GET con la forma `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Actualización de la aplicación para volver a conectar el back-end de la aplicación móvil
En esta sección, vuelva a conectar la aplicación al back-end de la aplicación móvil. La primera vez que se ejecuta la aplicación, el controlador de eventos `OnCreate` llama a `OnRefreshItemsSelected`. Este método llama a `SyncAsync` para sincronizar el almacén local con la base de datos back-end.

1. Abra ToDoActivity.cs en el proyecto compartido y revierta el cambio de la propiedad **applicationURL**.
2. Presione la tecla **F5** para volver a compilar y ejecutar el proyecto. La aplicación sincroniza los cambios locales con el back-end de Azure Mobile App mediante operaciones de inserción e incorporación de cambios cuando se ejecuta el método `OnRefreshItemsSelected`.
3. (Opcional) Vea los datos actualizados mediante el Explorador de objetos de SQL Server o una herramienta REST como Fiddler. Observe que los datos se han sincronizado entre la base de datos del back-end de la aplicación móvil de Azure y el almacén local.
4. En la aplicación, haga clic en la casilla situada junto a algunos elementos para completarlos en el almacén local.

   `CheckItem` llama a `SyncAsync` para sincronizar cada elemento completado con el back-end de Mobile App. `SyncAsync` llama a las operaciones de inserción y extracción. **Cada vez que se ejecuta una incorporación de cambios en una tabla en la que el cliente ha realizado cambios, siempre se ejecuta automáticamente una inserción**. De este modo se garantiza la coherencia de todas las tablas del almacén local, junto con sus relaciones. Este comportamiento puede provocar una inserción inesperada. Para obtener más información sobre este comportamiento, consulte [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].

## <a name="review-the-client-sync-code"></a>Revisión del código de sincronización de cliente
El proyecto de cliente de Xamarin que descargó cuando completó el tutorial [Creación de una aplicación Xamarin.Android] ya contiene el código que admite la sincronización sin conexión con una base de datos SQLite local. Esta es una breve descripción de lo que ya está incluido en el código del tutorial. Para obtener información general conceptual de la característica, consulte [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].

* Antes de poder realizar cualquier operación de tabla, se debe inicializar el almacén local. La base de datos del almacén local se inicializa cuando `ToDoActivity.OnCreate()` ejecuta `ToDoActivity.InitLocalStoreAsync()`. Este método crea una base de datos SQLite local que usa la clase `MobileServiceSQLiteStore` que proporciona el SDK de cliente de Azure Mobile Apps.

    El método `DefineTable` crea una tabla en el almacén local que coincide con los campos del tipo proporcionado, `ToDoItem` en este caso. El tipo no tiene que incluir todas las columnas que se encuentran en la base de datos remota. Es posible almacenar solo un subconjunto de columnas.

        // ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
                .GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
            // For more details, see http://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }
* El miembro `toDoTable` de `ToDoActivity` es del tipo `IMobileServiceSyncTable` en lugar de `IMobileServiceTable`. IMobileServiceSyncTable dirige todas las operaciones de la tabla de creación, lectura, actualización y eliminación (CRUD) a la base de datos del almacén local.

    Usted decide en qué momento se insertan los cambios en el back-end Azure Mobile App mediante una llamada a `IMobileServiceSyncContext.PushAsync()`. El contexto de sincronización ayuda a mantener las relaciones entre tablas mediante el seguimiento y la inserción de los cambios en todas las tablas modificadas por una aplicación cliente cuando se llama a `PushAsync` .

    El código proporcionado llama a `ToDoActivity.SyncAsync()` para sincronizarse cada vez que se actualiza la lista todoitem o se agrega o completa un todoitem. El código se sincroniza después de cada cambio local.

    En el ejemplo proporcionado, se consultan todos los registros de la tabla `TodoItem` remota, pero también es posible filtrar registros pasando un identificador de consulta y una consulta a `PushAsync`. Para más información, consulte la sección *Sincronización incremental* en [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].

        // ToDoActivity.cs
        private async Task SyncAsync()
        {
            try {
                await client.SyncContext.PushAsync();
                await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
            } catch (Java.Net.MalformedURLException) {
                CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
            } catch (Exception e) {
                CreateAndShowDialog (e, "Error");
            }
        }

## <a name="additional-resources"></a>Recursos adicionales
* [Sincronización de datos sin conexión en Aplicaciones móviles de Azure]
* [Procedimiento del SDK de .NET de Azure Mobile Apps][8]

<!-- URLs. -->
[Creación de una aplicación Xamarin.Android]: ../app-service-mobile-xamarin-android-get-started.md
[Sincronización de datos sin conexión en Aplicaciones móviles de Azure]: ../app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Creación de una aplicación Xamarin Android]: app-service-mobile-xamarin-android-get-started.md
[Sincronización de datos sin conexión en Aplicaciones móviles de Azure]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: http://xamarin.com/download
[Xamarin extension]: http://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
