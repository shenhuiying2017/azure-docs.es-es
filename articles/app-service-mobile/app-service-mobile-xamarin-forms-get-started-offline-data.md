---
title: "Habilitación de la sincronización sin conexión para una aplicación móvil de Azure (Xamarin.Forms) | Microsoft Docs"
description: "Obtenga información acerca de cómo usar la aplicación móvil del Servicio de aplicaciones para almacenar en caché y sincronizar datos sin conexión en su aplicación Xamarin.Forms."
documentationcenter: xamarin
author: ggailey777
manager: yochayk
editor: 
services: app-service\mobile
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: glenga
ms.openlocfilehash: f2bed0a7124517319cc82405c4ab6b4d79aacfe1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Habilitación de la sincronización sin conexión para la aplicación móvil Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Información general
Este tutorial presenta la característica de sincronización sin conexión de Aplicaciones móviles de Azure para Xamarin.Forms. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil (ver, agregar o modificar datos), incluso cuando no hay ninguna conexión de red. Los cambios se almacenan en una base de datos local. Una vez que el dispositivo se vuelve a conectar, estos cambios se sincronizan con el servicio remoto.

Este tutorial se basa en la solución de inicio rápido de Xamarin.Forms para Mobile Apps que crea al completar el tutorial [Crear una aplicación Xamarin.iOS]. La solución de inicio rápido para Xamarin.Forms contiene el código para admitir la sincronización sin conexión, que solo debe habilitarse. En este tutorial, actualizará la solución de inicio rápido para activar las características sin conexión de Azure Mobile Apps. También nos centraremos en el código sin conexión específico de la aplicación. Si no usa la solución de inicio rápido descargada, debe agregar paquetes de extensión de acceso de datos al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte el artículo sobre cómo [trabajar con el SDK del servidor back-end de .NET para Azure Mobile Apps][1].

Para más información sobre la característica de sincronización sin conexión, consulte el tema [Sincronización de datos sin conexión en Azure Mobile Apps][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Habilitación de la funcionalidad de sincronización sin conexión en la solución de inicio rápido
Se incluye el código de sincronización sin conexión en el proyecto mediante el uso de directivas de preprocesador de C#. Cuando se define el símbolo **OFFLINE\_SYNC\_ENABLED**, se incluyen en la compilación estas rutas de acceso del código. Para las aplicaciones de Windows, también debe instalar la plataforma de SQLite.

1. En Visual Studio, haga clic con el botón derecho en la solución > **Administrar paquetes NuGet para la solución...** y, después, busque e instale el paquete NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** en todos los proyectos de la solución.
2. En el Explorador de soluciones, abra el archivo TodoItemManager.cs desde el proyecto con **Portable** en el nombre, que es el proyecto de biblioteca de clases portable, y, después, quite la directiva de preprocesador siguiente:

        #define OFFLINE_SYNC_ENABLED
3. (Opcional) Para admitir dispositivos de Windows, instale uno de los siguientes paquetes en el sistema de tiempo de ejecución de SQLite:

   * **Runtime de Windows 8.1:** instale [SQLite para Windows 8.1][3].
   * **Windows Phone 8.1:** instale [SQLite para Windows Phone 8.1][4].
   * **Plataforma universal de Windows**: instale [SQLite para la plataforma universal de Windows][5].

     Aunque el tutorial rápido no contiene un proyecto de Windows universal, la plataforma de Windows universal es compatible con Xamarin Forms.
4. (Opcional) En cada proyecto de aplicación de Windows, haga clic con el botón derecho en **Referencias** > **Agregar referencia...**, amplíe la carpeta **Windows** > **Extensiones**.
    Habilite el SDK de **SQLite para Windows** junto con el SDK **Visual C++ 2013 Runtime para Windows**.
    Los nombres de SDK de SQLite varían ligeramente con cada plataforma de Windows.

## <a name="review-the-client-sync-code"></a>Revisión del código de sincronización de cliente
Esta es una breve descripción de lo que ya está incluido en el código del tutorial dentro de las directivas `#if OFFLINE_SYNC_ENABLED` . La funcionalidad de sincronización sin conexión está en el archivo de proyecto TodoItemManager.cs en el proyecto de biblioteca de clases portable. Para obtener información general conceptual de la característica, consulte [Sincronización de datos sin conexión en Azure Mobile Apps][2].

* Antes de poder realizar cualquier operación de tabla, se debe inicializar el almacén local. La base de datos del almacén local se inicializa en el constructor de clase **TodoItemManager** con el código siguiente:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Este código crea una nueva SQLite local con la clase **MobileServiceSQLiteStore**.

    El método **DefineTable** crea una tabla en el almacén local que coincide con los campos del tipo proporcionado.  El tipo no tiene que incluir todas las columnas que se encuentran en la base de datos remota. Es posible almacenar solo un subconjunto de columnas.
* El campo **todoTable** de **TodoItemManager** es un tipo **IMobileServiceSyncTable** en lugar de **IMobileServiceTable**. Esta clase usa la base de datos local para todas las operaciones de creación, lectura, actualización y eliminación (CRUD) de tablas. Para decidir cuándo se deben integrar esos cambios en el back-end de aplicación móvil, llame a **PushAsync** en el método **IMobileServiceSyncContext**. El contexto de sincronización ayuda a mantener las relaciones entre tablas mediante el seguimiento y la inserción de los cambios en todas las tablas modificadas por una aplicación cliente cuando se llama a **PushAsync**.

    El siguiente método **SyncAsync** se llama para realizar la sincronización con el back-end de aplicación móvil:

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    Este ejemplo utiliza un control de errores sencillo con el controlador de sincronización predeterminado. Una aplicación real controlaría los diversos errores como condiciones de red y conflictos de red mediante el uso de una implementación personalizada de **IMobileServiceSyncHandler** .

## <a name="offline-sync-considerations"></a>Consideraciones sobre la sincronización sin conexión
En el ejemplo, solo se llama al método **SyncAsync** al inicio y cuando se solicita una sincronización.  Para iniciar la sincronización en una aplicación Android o iOS, despliegue la lista de elementos; para Windows, utilice el botón **Sincronizar** . En una aplicación real, también puede hacer que la sincronización se desencadene cuando cambia el estado de la red.

Si se ejecuta una extracción en una tabla que tiene actualizaciones locales pendientes seguidas por el contexto, la operación de extracción desencadenará de forma automática una inserción de contexto anterior. Al actualizar, agregar y completar elementos en este ejemplo, se puede omitir la llamada explícita a **PushAsync**.

En el ejemplo proporcionado, se consultan todos los registros de la tabla TodoItem remota, pero también es posible filtrar registros pasando un identificador de consulta y una consulta a **PushAsync**. Para más información, consulte la sección *Sincronización incremental* en [Sincronización de datos sin conexión en Azure Mobile Apps][2].

## <a name="run-the-client-app"></a>Ejecución de la aplicación cliente
Con la sincronización sin conexión habilitada, ejecute la aplicación cliente al menos una vez en cada plataforma para rellenar la base de datos del almacén local. Después, simulará un escenario sin conexión y modificará los datos del almacén local mientras la aplicación está sin conexión.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Actualización del comportamiento de sincronización de la aplicación cliente
En esta sección, modificará el proyecto de cliente para simular un escenario sin conexión usando una dirección URL de aplicación no válida para el back-end. Como alternativa, puede desactivar las conexiones de red cambiando el dispositivo a "Modo de avión".  Al agregar o cambiar elementos de datos, estos cambios se conservan en el almacén local, pero no se sincronizan con el almacén de datos de back-end hasta que se restablezca la conexión.

1. En el Explorador de soluciones, abra el archivo de proyecto Constants.cs desde el proyecto **Portable** y cambie el valor de `ApplicationURL` para que apunte a una dirección URL no válida:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Abra el archivo TodoItemManager.cs desde el proyecto **Portable**, agregue después un valor **catch** adicional para la clase **Exception** de base al bloque **try...catch** de **SyncAsync**. Este bloque **catch** escribe el mensaje de excepción en la consola, de la forma siguiente:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Compile y ejecute la aplicación cliente.  Agregue nuevos elementos. Tenga en cuenta que la excepción se registra en la consola para cada intento de sincronización con el back-end. Estos nuevos elementos solo existirán en el almacén local hasta que se puedan insertar en el back-end móvil. La aplicación cliente se comporta como si estuviera conectada al back-end y admite todas las operaciones de creación, lectura, actualización y eliminación (CRUD).
4. Cierre la aplicación y reiníciela para comprobar que los nuevos elementos que creó se mantienen en el almacén local.
5. (Opcional) Use Visual Studio para ver la tabla de base de datos SQL de Azure y observar que los datos de la base de datos de back-end no han cambiado.

    En Visual Studio, abra el **Explorador de servidores**. Vaya a la base de datos en **Azure**->**SQL Databases**. Haga clic con el botón derecho en la base de datos y seleccione **Abrir en el Explorador de objetos de SQL Server**. Ahora puede buscar la tabla de base de datos SQL y su contenido.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Actualización de la aplicación cliente para volver a conectar el back-end móvil
En esta sección se volverá a conectar la aplicación al back-end móvil, que simula que la aplicación vuelve a un estado en línea. Al realizar el gesto de la actualización, los datos se sincronizarán con el back-end móvil.

1. Vuelva a abrir Constants.cs. Corrija `applicationURL` para que apunte a la dirección URL correcta.
2. Vuelva a compilar la aplicación cliente y ejecútela. La aplicación intenta sincronizarse con el back-end de aplicación móvil después de iniciarse. Compruebe que no hay excepciones registradas en la consola de depuración.
3. (Opcional) Vea los datos actualizados mediante el Explorador de objetos de SQL Server o una herramienta REST como Fiddler o [Postman][6]. Observe que los datos se han sincronizado entre la base de datos back-end y el almacén local.

    Observe que los datos se han sincronizado entre la base de datos y el almacén local, y contienen los elementos que agregó mientras la aplicación estaba desconectada.

## <a name="additional-resources"></a>Recursos adicionales
* [Sincronización de datos sin conexión en Azure Mobile Apps][2]
* [Procedimiento del SDK de .NET de Azure Mobile Apps][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: http://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: http://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: http://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: http://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
