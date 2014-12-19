<properties urlDisplayName="Using Offline Data" pageTitle="Uso de datos sin conexión en Servicios móviles (Xamarin Android) | Centro de desarrollo móvil" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Xamarin Android application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data in Mobile Services" authors="donnam" editor="wesmc" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="donnam" />

# Uso de la sincronización de datos sin conexión en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]

En este tema se muestra cómo usar las capacidades sin conexión de Servicios móviles de Azure. Estas características permiten interactuar con una base de datos local cuando el usuario se encuentra en un escenario sin conexión con su servicio móvil. Las características sin conexión le permiten sincronizar los cambios locales con el servicio móvil cuando vuelva a estar en línea. 

En este tutorial actualizará la aplicación desde el tutorial [Introducción a Servicios móviles] o [Introducción a los datos] para que admita las características sin conexión de Servicios móviles de Azure. A continuación, agregará datos en un escenario sin conexión desconectado, sincronizará dichos elementos con la base de datos en línea e iniciará sesión en el Portal de administración de Azure para ver los cambios efectuados en los datos al ejecutar la aplicación.

>[WACOM.NOTE] Este tutorial está destinado a profundizar en el uso de Azure con Servicios móviles para almacenar y recuperar datos en una aplicación de la Tienda Windows. Para ello, en este tema se recorren muchos de los pasos que se completan automáticamente en el inicio rápido de Servicios móviles. Si esta es la primera vez que usa los Servicios móviles, considere la posibilidad de completar antes el tutorial [Introducción a Servicios móviles].
>
> Necesita una cuenta de Azure para completar este tutorial. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 servicios móviles gratuitos que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Evaluación gratuita de Azure</a>. 

Hay disponible un proyecto con el estado completado de este tutorial [aquí](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.Android).

Este tutorial le guiará a través de estos pasos básicos:

1. [Actualización de la aplicación para que admita características sin conexión]
2. [Prueba de la aplicación conectada al servicio móvil]

Este tutorial requiere lo siguiente:

* Visual Studio con la [extensión Xamarin] **o** [Xamarin Studio] 
* Finalización del tutorial [Introducción a Servicios móviles] o [Introducción a los datos]
* [SDK de Servicios móviles de Azure versión 1.3.0-beta2][Mobile Services SDK Nuget]
* [Almacén SQLite de Servicios móviles de Azure versión 1.0.0-beta2][SQLite store nuget]

>[WACOM.NOTE] Las instrucciones siguientes asumen que está utilizando Visual Studio 2012 o superior con la extensión Xamarin. Si está utilizando Xamarin Studio, use la compatibilidad integrada del administrador de paquetes de NuGet.

## <a name="enable-offline-app"></a>Actualización de la aplicación para que admita características sin conexión

La sincronización sin conexión de Servicios móviles de Azure permite que los usuarios finales interactúen con una base de datos local cuando la red no está accesible. Para usar estas características en la aplicación, inicialice `MobileServiceClient.SyncContext` en un almacén local. A continuación, obtenga una referencia a la tabla mediante la interfaz `IMobileServiceSyncTable` .

1. En Visual Studio, abra el proyecto que finalizó en el tutorial [Introducción a Servicios móviles] o el tutorial [Introducción a los datos]. En el Explorador de soluciones, quite la referencia al **SDK de Servicios móviles de Azure** en **Componentes**.

2. Instale el paquete de versión preliminar el almacén SQLite de Servicios móviles usando el siguiente comando en la Consola del Administrador de paquetes: 
    
        install-package WindowsAzure.MobileServices.SQLiteStore -Pre

    Esto también instalará todas las dependencias requeridas.
    
3. En el nodo de referencias, quite las referencias a `System.IO`, `System.Runtime` y `System.Threading.Tasks`.

### Edición de ToDoActivity.cs

1. Agregar las declaraciones

		using Microsoft.WindowsAzure.MobileServices.Sync;
		using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
		using System.IO;

2. Cambie el tiempo de miembro `ToDoActivity.toDoTable` de `IMobileServiceTable<>` a `IMobileServiceSyncTable<>`

3. En el método `OnCreate(Bundle)`, después de la línea que inicializa el miembro `client`, agregue el siguiente código:

	    // existing initializer
	    client = new MobileServiceClient (applicationURL, applicationKey, progressHandler);
	
		// new code to initialize the SQLite store
	    string path = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "test1.db");
	
	    if (!File.Exists(path))
	    {
	        File.Create(path).Dispose();
	    }

	    var store = new MobileServiceSQLiteStore(path);
	    store.DefineTable<ToDoItem>();
	
	    await client.SyncContext.InitializeAsync(store, new TodoSyncHandler(this));

4. En el mismo método, cambie la línea que inicializa `toDoTable` para usar el método `GetSyncTable<>` en lugar de `GetTable<>`:

		toDoTable = client.GetSyncTable <ToDoItem> ();

5. Modifique el método `OnRefreshItemsSelected` para agregar llamadas a `PushAsync` y `PullAsync`:

		async void OnRefreshItemsSelected ()
		{
		    await client.SyncContext.PushAsync();
			await this.todoTable.PullAsync("todoItems", todoTable.CreateQuery());
		    await RefreshItemsFromTableAsync();
		}

### Edición de ToDoItem.cs 

1. Agregue la instrucción using: 

        using Microsoft.WindowsAzure.MobileServices; 


2. Agregue los siguientes miembros a la clase "ToDoItem":
 
		[Version]
		public string Version { get; set; }
		
		
		public override string ToString()
		{
		    return "Text: " + Text + "\nComplete: " + Complete + "\n";
		}

## <a name="test-online-app"></a>Prueba de la aplicación 

En esta sección probará el método `SyncAsync` que sincroniza el almacén local con la base de datos del servicio móvil.

1. En Visual Studio, presione el botón **Ejecutar** para crear el proyecto e iniciar la aplicación en el emulador de iPhone, que es la solución predeterminada para este proyecto.

2. Observe que la lista de elementos de la aplicación está vacía. Como consecuencia del cambio del código en la sección anterior, la aplicación ya no lee elementos del servicio móvil, sino del almacén local. 

3. Agregue elementos a la lista de tareas pendientes.

    ![][1]


4. Inicie sesión en el Portal de administración de Azure y consulte la base de datos del servicio móvil. Si el servicio usa el back-end de JavaScript para servicios móviles, puede examinar los datos en la pestaña **Data** del servicio móvil. Si usa el back-end de .NET para el servicio móvil, puede hacer clic en el botón **Administrar** de su base de datos en la extensión de SQL Azure para ejecutar una consulta en la tabla.

    Observe que los datos no se han sincronizado entre la base de datos y el almacén local.

5. En la aplicación, presione el botón **Actualizar**. Esto hace que la aplicación llame a `MobileServiceClient.SyncContext.PushAsync`, a `IMobileServiceSyncTable.PullAsync()` y a `RefreshTodoItems` para actualizar la aplicación con los elementos del almacén local. 

    La operación de inserción se traduce en que la base de datos del servicio móvil recibe los datos del almacén. Se ejecuta de `MobileServiceClient.SyncContext` en lugar de `IMobileServicesSyncTable` e inserta los cambios en todas las tablas asociadas con ese contexto de sincronización. Esto tiene por objeto incluir escenarios en los que existen relaciones entre tablas.
    
    Por el contrario, la operación de extracción recupera registros solamente de la tabla que se especificó. Si hay operaciones pendientes para esta tabla en el contexto de sincronización, el SDK de Servicios móviles llamará explícitamente a una operación "PushAsync".
        
    ![][3] 



    ![][2]


  

##Resumen

Para admitir las características sin conexión de servicios móviles, usamos la interfaz `IMobileServiceSyncTable` e inicializamos `MobileServiceClient.SyncContext` con un almacén local. En este caso, el almacén local era una base de datos SQLite.

Las operaciones CRUD normales para servicios móviles funcionan como si la aplicación siguiera conectada, pero todas las operaciones se producen en el almacén local.

Cuando queramos sincronizar el almacén local con el servidor, usaremos los métodos `IMobileServiceSyncTable.PullAsync` y `MobileServiceClient.SyncContext.PushAsync`.

*  Para insertar cambios en el servidor, llamamos a `IMobileServiceSyncContext.PushAsync()`. Este método es miembro de `IMobileServicesSyncContext` y no de la tabla de sincronización porque insertará cambios en todas las tablas:

    Solo los registros que se han modificado localmente de alguna forma (mediante operaciones CUD) se enviarán al servidor.
   
* Para extraer datos de una tabla del servidor en la aplicación, llamamos a `IMobileServiceSyncTable.PullAsync`.

    Una extracción siempre realiza primero una inserción.  

    Este ejemplo usa una sobrecarga de **PullAsync()** que permite especificar una clave de consulta y una consulta. La clave de consulta se usa para la sincronización incremental. El SDK de Servicios móviles hace un seguimiento de la última marca de tiempo actualizada después de cada operación de extracción exitosa. En la siguiente extracción, solo se recuperarán registros más recientes. Si no se especifica ninguna clave de consulta, se realizará una sincronización completa para la tabla de sincronización.

## Pasos siguientes

Puede descargar la versión completa de este tutorial en nuestro [repositorio de muestras de GitHub](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.Android).

<!--* [Handling conflicts with offline support for Mobile Services]
-->
* [Uso del cliente del componente Xamarin para Servicios móviles de Azure]

<!-- Anchors. -->
[Actualización de la aplicación para que admita características sin conexión]: #enable-offline-app
[Prueba de la aplicación conectada al servicio móvil]: #test-online-app
[Pasos siguientes]:#next-steps

<!-- Images -->
[1]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-quickstart-startup-android.png
[2]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-data-browse.png
[3]: ./media/mobile-services-xamarin-android-get-started-offline-data/mobile-quickstart-completed-android.png



<!-- URLs. -->
[Control de conflictos con compatibilidad sin conexión para Servicios móviles]: /es-es/documentation/articles/mobile-services-xamarin-android-handling-conflicts-offline-data/ 
[Introducción a los datos]: /es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/
[Introducción a Servicios móviles]: /es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started/
[Uso del cliente del componente Xamarin para Servicios móviles de Azure]: /es-es/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/

[NuGet de SDK de Servicios móviles]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-beta2
[NuGet de almacén de SQLite]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-beta2
[Xamarin Studio]: http://xamarin.com/download
[Extensión Xamarin]: http://xamarin.com/visual-studio
[Complemento de NuGet para Xamarin]: https://github.com/mrward/monodevelop-nuget-addin
