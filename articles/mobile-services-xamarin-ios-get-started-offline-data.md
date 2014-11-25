<properties linkid="develop-mobile-tutorials-get-started-offline-data-ios" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Xamarin iOS application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data in Mobile Services" authors="donnam,wesmc" editor="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam,wesmc" />

# Introducción a la sincronización de datos sin conexión en Servicios móviles

<div class="dev-center-tutorial-selector sublanding">
<a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="C# para Tienda Windows">C# para Tienda Windows</a>
<a href="/es-es/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/es-es/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS">iOS</a>
<a href="/es-es/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS" class="current">Xamarin.iOS</a>
<a href="/es-es/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android">Xamarin.Android</a>
</div>

En este tema se muestra cómo usar las capacidades sin conexión de Servicios móviles de Azure. Estas características permiten interactuar con una base de datos local cuando el usuario se encuentra en un escenario sin conexión con su servicio móvil. Las características sin conexión le permiten sincronizar los cambios locales con el servicio móvil cuando vuelva a estar en línea.

En este tutorial, actualizará la aplicación desde el tutorial [Introducción a los Servicios móviles][Introducción a los Servicios móviles] o [Introducción a los datos][Introducción a los datos] para que admita las características sin conexión de Servicios móviles de Azure. A continuación, agregará datos en un escenario sin conexión desconectado, sincronizará dichos elementos con la base de datos en línea e iniciará sesión en el Portal de administración de Azure para ver los cambios efectuados en los datos al ejecutar la aplicación.

> [WACOM.NOTE] Este tutorial está destinado a profundizar en el uso de Azure con los Servicios móviles para almacenar y recuperar datos en una aplicación de la Tienda Windows. Para ello, en este tema se recorren muchos de los pasos que se completan automáticamente en el inicio rápido de Servicios móviles. Si esta es la primera vez que usa los Servicios móviles, considere la posibilidad de completar antes el tutorial [Introducción a los Servicios móviles][Introducción a los Servicios móviles].
>
> para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][Evaluación gratuita de Azure].

Este tutorial le guiará a través de estos pasos básicos:

1.  [Actualización de la aplicación para que admita características sin conexión][Actualización de la aplicación para que admita características sin conexión]
2.  [Prueba de la aplicación conectada al servicio móvil][Prueba de la aplicación conectada al servicio móvil]

Este tutorial requiere lo siguiente:

-   XCode 4.5 y iOS 6.0 (o versiones anteriores)
-   Visual Studio con la [extensión Xamarin][extensión Xamarin] **o** [Xamarin Studio][Xamarin Studio] en OS X
-   Finalización del tutorial [Introducción a los Servicios móviles][Introducción a los Servicios móviles] o [Introducción a los datos][Introducción a los datos].
-   [SDK de Servicios móviles de Azure versión 1.3.0-alpha3 (o posterior)][SDK de Servicios móviles de Azure versión 1.3.0-alpha3 (o posterior)]
-   [Almacén SQLite de Servicios móviles de Azure versión 1.0.0-alpha2 (o posterior)][Almacén SQLite de Servicios móviles de Azure versión 1.0.0-alpha2 (o posterior)]

> [WACOM.NOTE] Las instrucciones siguientes asumen que está utilizando Visual Studio 2012 o superior con la extensión Xamarin. Si utiliza Xamarin Studio en OS X, la mayoría de las instrucciones serán las mismas, pero también debe instalar el [complemento NuGet para Xamarin][complemento NuGet para Xamarin] de forma que pueda agregar fácilmente los paquetes NuGet de Servicios móviles de versión preliminar al proyecto.

## <a name="enable-offline-app"></a>Actualización de la aplicación para que admita características sin conexión

Las características sin conexión de Servicios móviles de Azure permiten interactuar con una base de datos local cuando el usuario se encuentra en un escenario sin conexión con su servicio móvil. Para usar estas características en la aplicación, inicialice `MobileServiceClient.SyncContext` en un almacén local. A continuación, obtenga una referencia a la tabla mediante la interfaz `IMobileServiceSyncTable`.

1.  En Visual Studio, abra el proyecto que finalizó en el tutorial [Introducción a los Servicios móviles][Introducción a los Servicios móviles] o [Introducción a los datos][Introducción a los datos]. En el Explorador de soluciones, quite la referencia a **SDK de Servicios móviles de Azure** en **Componentes**.

2.  Instale el paquete de versión preliminar del almacén SQLite de Servicios móviles usando el siguiente comando en la Consola del Administrador de paquetes:

        install-package WindowsAzure.MobileServices.SQLiteStore -Pre

    Esto también instalará todas las dependencias requeridas.

3.  En el nodo de referencias, quite las referencias a `System.IO`, `System.Runtime` y `System.Threading.Tasks`.

### Edición del archivo QSTodoService.cs

Edite la clase `QSTodoService` para habilitar el uso de las características sin conexión de Servicios móviles con un almacén local de SQLite.

1.  Agregue las siguientes instrucciones using en la parte superior del archivo:

        using Microsoft.WindowsAzure.MobileServices; 
        using Microsoft.WindowsAzure.MobileServices.Sync; 
        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;

2.  Cambie el tipo del miembro `todoTable` de `IMobileServiceTable` a `IMobileServicesSyncTable`

        IMobileServiceSyncTable<ToDoItem> todoTable; 

3.  En el constructor para `QSTodoService`, cambie el inicializador para `todoTable`:

        todoTable = client.GetSyncTable <ToDoItem> ();

4.  En el constructor para `QSTodoService`, agregue una llamada a `SQLitePCL.CurrentPlatform.Init()` como la segunda línea del código:

        QSTodoService ()
        {
            CurrentPlatform.Init ();
            SQLitePCL.CurrentPlatform.Init(); // add this line

            // Initialize the Mobile Service client with your URL and key
            client = new MobileServiceClient (applicationURL, applicationKey, this);

            // Create an MSTable instance to allow us to work with the TodoItem table
            todoTable = client.GetSyncTable <ToDoItem> ();
        }

5.  En la clase `QSTodoService`, defina un nuevo método `InitializeAsync`:

        public async Task InitializeStoreAsync()
        {
            string path = "syncstore.db";
            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();
            await client.SyncContext.InitializeAsync(store);
        }

6.  En la clase `QSTodoService`, defina un nuevo método `SyncAsync`:

        public async Task SyncAsync()
        {
            try
            {
                await this.client.SyncContext.PushAsync();
                await this.todoTable.PullAsync();
            }
            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

### Edición de ToDoItem.cs

-   Agregue la instrucción using:

        using Microsoft.WindowsAzure.MobileServices; 

-   Agregue los siguientes métodos a la clase `ToDoItem`:

        [Version]
        public string Version { get; set; }


        public override string ToString()
        {
            return "Text: " + Text + "\nComplete: " + Complete + "\n";
        }

### Edición de QSTodoListViewController.cs

Modifique `QSTodoListViewController` para llamar al nuevo método `SyncAsync` cuando el usuario realiza el gesto de actualización.

1.  Agregue una llamada a `InitializeStoreAsync` en `ViewDidLoad()`, después de la inicialización de `todoService`:

        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            todoService = QSTodoService.DefaultService;
            await todoService.InitializeStoreAsync();

            ...    // the rest of the code in the method is unchanged
        }

2.  Modifique el método `AddRefreshControl` para llamar a `SyncAsync` antes de la llamada a `RefreshAsync`:

        RefreshControl.ValueChanged += async (sender, e) => {
            await todoService.SyncAsync();
            await RefreshAsync();
        }; 

<!--  DM: commenting this out because this tutorial doesn't show OC conflict handling ### Edit ToDoItem.cs   Modify the strongly-type data class to add a version field  1. In the top of the file, add the using statement:           using Microsoft.WindowsAzure.MobileServices;   2. Add the following members to the class `ToDoItem`:           [Version]         public string Version { get; set; }                  public override string ToString()         {             return "Text: " + Text + "\nComplete: " + Complete + "\n";         }  -->

## <a name="test-online-app"></a>Prueba de la aplicación

En esta sección probará el método `SyncAsync` que sincroniza el almacén local con la base de datos del servicio móvil.

1.  En Visual Studio, presione el botón **Ejecutar** para crear el proyecto e iniciar la aplicación en el emulador de iPhone, que es la solución predeterminada para este proyecto.

2.  Observe que la lista de elementos de la aplicación está vacía. Como consecuencia del cambio del código en la sección anterior, la aplicación ya no lee elementos del servicio móvil, sino del almacén local.

3.  Agregue elementos a la lista de tareas pendientes.

    ![][0]

4.  Inicie sesión en el Portal de administración de Azure y consulte la base de datos del servicio móvil. Si el servicio usa el back-end de JavaScript para servicios móviles, puede examinar los datos en la pestaña **Datos** del servicio móvil. Si usa el back-end de .NET para el servicio móvil, puede hacer clic en el botón **Manage** de su base de datos en la extensión de SQL Azure para ejecutar una consulta en la tabla.

    Observe que los datos no se han sincronizado entre la base de datos y el almacén local.

5.  En la aplicación, realice el gesto de actualización desplegando la lista de elementos. Esto hace que la aplicación llame a `MobileServiceClient.SyncContext.PushAsync` y `IMobileServiceSyncTable.PullAsync()` y luego a `RefreshTodoItems` para actualizar la aplicación con los elementos del almacén local.

    La operación de inserción se traduce en que la base de datos del servicio móvil recibe los datos del almacén. Se ejecuta fuera de `MobileServiceClient.SyncContext` en lugar de `IMobileServicesSyncTable`, e inserta cambios en todas las tablas asociadas a dicho contexto de sincronización. Esto tiene por objeto incluir escenarios en los que existen relaciones entre tablas.

    Por el contrario, la operación de extracción recupera registros solamente de la tabla que se especificó. Si hay operaciones pendientes para esta tabla en el contexto de sincronización, el SDK de Servicios móviles llamará explícitamente a una operación `PushAsync`.

    ![][1]

    ![][2]

## Resumen

Para admitir las características sin conexión de servicios móviles, usamos la interfaz `IMobileServiceSyncTable` e inicializamos `MobileServiceClient.SyncContext` con un almacén local. En este caso, el almacén local era una base de datos SQLite.

Las operaciones CRUD normales para servicios móviles funcionan como si la aplicación siguiera conectada, pero todas las operaciones se producen en el almacén local.

Cuando queramos sincronizar el almacén local con el servidor, usaremos los métodos `IMobileServiceSyncTable.PullAsync` y `MobileServiceClient.SyncContext.PushAsync`.

-   Para insertar cambios en el servidor, llamamos a `IMobileServiceSyncContext.PushAsync()`. Este método es miembro de `IMobileServicesSyncContext` y no de la tabla de sincronización porque insertará cambios en todas las tablas:

    Solo los registros que se han modificado localmente de alguna forma (mediante operaciones CUD) se enviarán al servidor.

-   Para extraer datos de una tabla del servidor en la aplicación, llamamos a `IMobileServiceSyncTable.PullAsync`.

    Una extracción siempre realiza primero una inserción.

    También existen sobrecargas de **PullAsync()** que permiten especificar una consulta. Observe que en la versión preliminar de compatibilidad sin conexión con Servicios móviles, **PullAsync** leerá todas las filas de la tabla (o consulta) correspondiente: por ejemplo, no intenta leer solo las filas posteriores a la última sincronización. Si las filas ya existen en la tabla de sincronización local, permanecerán intactas.

## Pasos siguientes

<!--* [Handling conflicts with offline support for Mobile Services] -->

-   [Uso del cliente del componente Xamarin para Servicios móviles de Azure][Uso del cliente del componente Xamarin para Servicios móviles de Azure]


<!-- Images -->


  [Introducción a los Servicios móviles]: /es-es/documentation/articles/partner-xamarin-mobile-services-ios-get-started/
  [Introducción a los datos]: /es-es/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=AE564AB28
  [Actualización de la aplicación para que admita características sin conexión]: #enable-offline-app
  [Prueba de la aplicación conectada al servicio móvil]: #test-online-app
  [extensión Xamarin]: http://xamarin.com/visual-studio
  [Xamarin Studio]: http://xamarin.com/download
  [complemento NuGet para Xamarin]: https://github.com/mrward/monodevelop-nuget-addin
  [0]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-quickstart-startup-ios.png
  [1]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-quickstart-completed-ios.png
  [2]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-data-browse.png
  [Uso del cliente del componente Xamarin para Servicios móviles de Azure]: /es-es/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/
