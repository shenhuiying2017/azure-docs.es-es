<properties
	pageTitle="Activación de la sincronización sin conexión para la aplicación móvil de Azure (iOS)"
	description="Obtenga información acerca de cómo usar las aplicaciones móviles de Servicios de aplicaciones para almacenar en caché y sincronizar datos sin conexión en su aplicación iOS"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="krisragh"/>

# Activación de la sincronización sin conexión para la aplicación móvil iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## Información general

Este tutorial explica la característica de sincronización sin conexión de Aplicaciones móviles de Azure para iOS. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil (ver, agregar o modificar datos), incluso cuando no hay ninguna conexión de red. Los cambios se almacenan en una base de datos local; una vez que el dispositivo se vuelve a conectar, estos cambios se sincronizan con el back-end remoto.

Si esta es la primera vez que usa Aplicaciones móviles de Azure, primero debería completar el tutorial [Creación de una aplicación iOS]. Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar paquetes de extensión de acceso de datos al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para obtener más información acerca de la característica de sincronización sin conexión, consulte el tema [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].

## <a name="review-sync"></a>Revisión del código de sincronización de cliente

El proyecto de cliente que descargó para el tutorial [Creación de una aplicación iOS] ya contiene el código que admite la sincronización sin conexión con una base de datos local basada en Core Data. Esta sección es un resumen de lo que ya está incluido en el código del tutorial. Para obtener información general conceptual de la característica, consulte [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].

La característica de sincronización de datos sin conexión de Aplicaciones móviles de Azure permite a los usuarios finales interactuar con una base de datos local cuando la red no está accesible. Para utilizar estas características en su aplicación, inicialice el contexto de sincronización de `MSClient` y haga referencia a un almacén local. A continuación, obtenga una referencia a la tabla mediante la interfaz `MSSyncTable`.

1. En **QSTodoService.m** (Objective-C) o **ToDoTableViewController.swift** (Swift), tenga en cuenta que el tipo del miembro `syncTable` es `MSSyncTable`. La sincronización sin conexión usa esta interfaz de tabla de sincronización en lugar de `MSTable`. Cuando se utiliza una tabla de sincronización, todas las operaciones van al almacén local y solo se sincronizan con el back-end remoto con operaciones de inserción y extracción explícitas.

    Para obtener una referencia a una tabla de sincronización, utilice el método `syncTableWithName` en `MSClient`. Para quitar la funcionalidad de sincronización sin conexión, use en su lugar `tableWithName`.

2. Antes de poder realizar cualquier operación de tabla, se debe inicializar el almacén local. Este es el código pertinente.
	
	**Objective-C**:
	
	En el método `QSTodoService.init`:
	
	
	        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
	        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
	
	
	**SWIFT**:
	
	En el método `ToDoTableViewController.viewDidLoad`:
	
	
	        let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
	        let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
	        self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
	        client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
	

	Esto crea un almacén local mediante la interfaz `MSCoreDataStore`, que se proporciona en el SDK de Aplicaciones móviles. En su lugar puede proporcionar otro almacén local mediante la implementación del protocolo `MSSyncContextDataSource`.
	
	Además, el primer parámetro de `MSSyncContext` se utiliza para especificar un controlador de conflictos. Puesto que hemos pasado `nil`, obtenemos el controlador de conflictos predeterminado, que produce un error en cualquier conflicto.
	
3. Ahora, vamos a realizar la operación de sincronización real y a obtener datos desde el back-end remoto.

	**Objective-C**:
	
	`syncData` primero inserta nuevos cambios y, a continuación, llama a `pullData` para obtener datos desde el back-end remoto. A su vez, el método `pullData` obtiene datos nuevos que coinciden con una consulta:
	
	
	        -(void)syncData:(QSCompletionBlock)completion
	        {
	            // push all changes in the sync context, then pull new data
	            [self.client.syncContext pushWithCompletion:^(NSError *error) {
	                [self logErrorIfNotNil:error];
	                [self pullData:completion];
	            }];
	        }
	
	        -(void)pullData:(QSCompletionBlock)completion
	        {
	            MSQuery *query = [self.syncTable query];
	
	            // Pulls data from the remote server into the local table.
	            // We're pulling all items and filtering in the view
	            // query ID is used for incremental sync
	            [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
	                [self logErrorIfNotNil:error];
	
	                // Let the caller know that we have finished
	                if (completion != nil) {
	                    dispatch_async(dispatch_get_main_queue(), completion);
	                }
	            }];
	        }
        
        
      **Swift**:
        
        
		func onRefresh(sender: UIRefreshControl!) {
		    UIApplication.sharedApplication().networkActivityIndicatorVisible = true
		    
		    self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
		        (error) -> Void in
		        
		        UIApplication.sharedApplication().networkActivityIndicatorVisible = false
		        
		        if error != nil {
		            // A real application would handle various errors like network conditions,
		            // server conflicts, etc via the MSSyncContextDelegate
		            print("Error: (error!.description)")
		            
		            // We will just discard our changes and keep the servers copy for simplicity
		            if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
		                for opError in opErrors {
		                    print("Attempted operation to item (opError.itemId)")
		                    if (opError.operation == .Insert || opError.operation == .Delete) {
		                        print("Insert/Delete, failed discarding changes")
		                        opError.cancelOperationAndDiscardItemWithCompletion(nil)
		                    } else {
		                        print("Update failed, reverting to server's copy")
		                        opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
		                    }
		                }
		            }
		        }
		        self.refreshControl?.endRefreshing()
		    }
		} 
	
	
	En la versión Objective-C, en `syncData`, llamamos primero a `pushWithCompletion` en el contexto de sincronización. Este método es miembro de y no `MSSyncContext` de la tabla de sincronización porque insertará cambios en todas las tablas. Solo los registros que se han modificado localmente de alguna forma (mediante operaciones CUD) se enviarán al servidor. A continuación, se llama a la aplicación auxiliar `pullData`, que llama a `MSSyncTable.pullWithQuery` para recuperar datos remotos y almacenarlos en la base de datos local.
	
	En la versión Swift, no hay ninguna llamada a `pushWithCompletion`. Esto se debe a que la operación de inserción no era estrictamente necesaria. Si hay cambios pendientes en el contexto de sincronización para la tabla que está realizando una operación de inserción, la extracción siempre realiza primero una inserción. Sin embargo, si tiene más de una tabla de sincronización, es mejor llamar explícitamente a la inserción para asegurarse de que todo sea coherente en las tablas relacionadas.
	
	Tanto en la versión Objective-C como en la versión Swift, el método `pullWithQuery` le permite especificar una consulta para filtrar los registros que desea recuperar. En este ejemplo, la consulta recupera simplemente todos los registros en la tabla `TodoItem` remota.
	
	El segundo parámetro para `pullWithQuery` es un identificador de consulta que se utiliza para la *sincronización incremental*. La sincronización incremental recupera solo aquellos registros modificados desde la última sincronización, mediante la marca de tiempo `UpdatedAt` del registro (llamada `updatedAt` en el almacén local). El identificador de la consulta debe ser una cadena descriptiva que sea única para cada consulta lógica en la aplicación. Para la desactivación de la sincronización incremental, pase `nil` como identificador de la consulta. Tenga en cuenta que esto puede resultar potencialmente ineficaz, ya que se recuperarán todos los registros de cada operación de extracción.

5. La aplicación Objective-C se sincroniza cuando modificamos o agregamos datos, cuando un usuario realiza el gesto de actualización y en el inicio. La aplicación Swift se sincroniza cuando un usuario realiza el gesto de actualización y en el inicio.

Dado que la aplicación se sincroniza cada vez que se modifican datos (Objective-C) o cada vez que se inicia la aplicación (Objective-C y Swift), la aplicación supone que el usuario está en línea. En otra sección, actualizaremos la aplicación para que los usuarios puedan editar incluso cuando estén sin conexión.

## <a name="review-core-data"></a>Revisión del modelo Core Data

Cuando se usa el almacén sin conexión Core Data, tendrá que definir tablas y campos determinados en el modelo de datos. La aplicación de ejemplo ya incluye un modelo de datos con el formato correcto. En esta sección se le guiará a través de estas tablas y el modo de utilizarlas.

- Abra **QSDataModel.xcdatamodeld**. Hay cuatro tablas definidas: tres que utiliza el SDK y una para los propios elementos de la lista de tareas:
      * MS\_TableOperations: para realizar el seguimiento de los elementos que deben sincronizarse con el servidor
      * MS\_TableOperationErrors: para realizar el seguimiento de los errores que se producen durante la sincronización sin conexión
      * MS\_TableConfig: para realizar el seguimiento de la hora de la última actualización de la última operación de sincronización para todas las operaciones de extracción
      * TodoItem: para almacenar elementos de lista de tareas. Las columnas de sistema **createdAt**, **updatedAt** y **version** son propiedades del sistema opcionales.

>[AZURE.NOTE] El SDK de Aplicaciones móviles de Azure reserva los nombres de columna que comienzan con "**``**". No debe utilizar este prefijo en otra cosa que no sean las columnas del sistema; en caso contrario, se modificarán los nombres de columna cuando se use el back-end remoto.

- Al utilizar la característica de sincronización sin conexión, debe definir las tablas del sistema, tal como se muestra a continuación.

    ### Tablas del sistema

    **MS\_TableOperations**

    ![][defining-core-data-tableoperations-entity]

    | Atributo | Tipo |
    |----------- |   ------    |
    | id | Integer 64 |
    | itemId | String |
    | propiedades | Binary Data |
    | table | String |
    | tableKind | Integer 16 |

    <br>**MS\_TableOperationErrors**

    ![][defining-core-data-tableoperationerrors-entity]

    | Atributo | Tipo |
    |----------- |   ------    |
    | id | String |
    | operationId | Integer 64 |
    | propiedades | Binary Data |
    | tableKind | Integer 16 |

    <br>**MS\_TableConfig**

    ![][defining-core-data-tableconfig-entity]

    | Atributo | Tipo |
    |----------- |   ------    |
    | id | String |
    | key | String |
    | keyType | Integer 64 |
    | table | String |
    | value | String |

    ### Tabla de datos

    **TodoItem**

    | Atributo | Tipo | Nota: |
    |-----------   |  ------ | -------------------------------------------------------|
    | id | Cadena, marcado obligatorio | primary key in remote store |
    | complete | Booleano | todo item field |
    | text | String | todo item field |
    | createdAt | Date | (opcional) se asigna a la propiedad del sistema createdAt |
    | updatedAt | Date | (opcional) se asigna a la propiedad del sistema updatedAt |
    | versión | String | (opcional) se usa para detectar conflictos, se asigna a la versión |


## <a name="setup-sync"></a>Cambio del comportamiento de sincronización de la aplicación

En esta sección, modificará la aplicación para que no se sincronice en el inicio de la aplicación o al insertar y actualizar elementos, sino solo cuando se realice el gesto de actualización.

**Objective-C**:

1. En **QSTodoListViewController.m**, cambie el método **viewDidLoad** para quitar la llamada a `[self refresh]` al final del método. Ahora, los datos no se sincronizarán con el servidor al inicio de la aplicación, sino que lo hará el contenido del almacén local.

2. En **QSTodoService.m**, modifique la definición de `addItem` para que no se sincronice tras la inserción del elemento. Quite el bloque `self syncData` y sustitúyalo por lo siguiente:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. Modifique la definición de `completeItem` como anteriormente; quite el bloque de `self syncData` y sustitúyalo por lo siguiente:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

**Swift**:

1. En `viewDidLoad` en **ToDoTableViewController.swift**, comente estas dos líneas para detener la sincronización en el inicio de la aplicación. En el momento de redactar este artículo, la aplicación Swift Todo no actualiza el servicio cuando alguien agrega o completa un elemento, solo en el inicio de la aplicación.

		self.refreshControl?.beginRefreshing()
		self.onRefresh(self.refreshControl)


## <a name="test-app"></a>Prueba de la aplicación

En esta sección se conectará a una dirección URL no válida para simular un escenario sin conexión. Al agregar elementos de datos, estos se guardarán en el almacén local Core Data, pero no se sincronizarán con el back-end móvil.

1. Cambie la URL de la aplicación móvil en **QSTodoService.m** por una dirección URL válida y vuelva a ejecutar la aplicación:

	**Objective-C** en QSTodoService.m:
	
        	self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
	
	**Swift** en ToDoTableViewController.swift:

		let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")

2. Agregue elementos a la lista de tareas. Salga del simulador (o fuerce el cierre de la aplicación) y reinicie. Compruebe que se han guardado los cambios.

3. Vea el contenido de la tabla TodoItem remota:

    + En un back-end de Node.js, vaya al [Portal de Azure](https://portal.azure.com/) y, en el back-end de la aplicación móvil, haga clic en **Tablas fáciles** > **TodoItem** para ver el contenido de la `TodoItem` tabla.
   	+ En el back-end de .NET, vea el contenido de tabla con una herramienta SQL, como SQL Server Management Studio, o con un cliente REST como Fiddler o Postman.

    Compruebe que los nuevos elementos *no* se han sincronizado con el servidor:

4. Vuelva a cambiar la dirección URL por la correcta en **QSTodoService.m** y vuelva a ejecutar la aplicación. Realice el gesto de actualización desplegando la lista de elementos. Verá una rueda con el progreso.

5. Vea los datos de TodoItem de nuevo. Ahora deberían aparecer los elementos de TodoItems nuevos y modificados.

## Resumen

Para admitir la característica de sincronización sin conexión, usamos la interfaz `MSSyncTable` e inicializamos `MSClient.syncContext` con un almacén local. En este caso, el almacén local era una base de datos Core Data.

Cuando se utiliza un almacén local Core Data, debe definir varias tablas con las [propiedades del sistema de corrección](#review-core-data).

Las operaciones CRUD normales para Aplicaciones móviles de Azure funcionan como si la aplicación siguiera conectada, pero todas las operaciones se producen en el almacén local.

Cuando quisimos sincronizar el almacén local con el servidor, usamos el método `MSSyncTable.pullWithQuery`.


## Recursos adicionales

* [Sincronización de datos sin conexión en Aplicaciones móviles de Azure]

* [Cloud Cover: sincronización sin conexión en Servicios móviles de Azure] (nota: el vídeo trata sobre Servicios móviles, pero la sincronización sin conexión funciona de forma similar en Aplicaciones móviles de Azure)

<!-- URLs. -->


[Creación de una aplicación iOS]: ../app-service-mobile-ios-get-started.md
[Sincronización de datos sin conexión en Aplicaciones móviles de Azure]: ../app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover: sincronización sin conexión en Servicios móviles de Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_0810_2016-->