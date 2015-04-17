<properties 
	pageTitle="Activación de la sincronización sin conexión para la aplicación móvil (iOS)" 
	description="Obtenga información acerca de cómo usar la aplicación móvil de Servicios de aplicaciones para almacenar en caché y sincronizar datos sin conexión en su aplicación iOS" 
	documentationCenter="ios" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	services="app-service\mobile"/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="donnam"/>

# Activación de la sincronización sin conexión para la aplicación móvil iOS

<!-- [AZURE.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)] -->

Este tutorial explica la característica de sincronización sin conexión de Aplicaciones móviles para iOS. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil (ver, agregar o modificar datos), incluso cuando no hay ninguna conexión de red. Los cambios se almacenan en una base de datos local; una vez que el dispositivo se vuelve a conectar, estos cambios se sincronizan con el back-end remoto.

La sincronización sin conexión tiene varios usos posibles:

* Mejore la capacidad de respuesta de las aplicaciones almacenando en caché datos de servidor de forma local en el dispositivo.
* Hacer que las aplicaciones sean resistentes ante una conectividad de red intermitente.
* Permitir a los usuarios finales crear y modificar datos incluso cuando no hay acceso de red, proporcionando escenarios con muy poca o ninguna conectividad.
* Sincronizar datos entre diferentes dispositivos y detectar conflictos cuando dos dispositivos modifican el mismo registro.

Si esta es la primera vez que usa Aplicaciones móviles, complete el tutorial [Creación de una aplicación iOS].

## <a name="get-app"></a>Obtención de la aplicación de ejemplo sin conexión ToDo

En el [repositorio de muestras de Aplicaciones móviles en GitHub], clone el repositorio y abra el proyecto [Muestra de iOS sin conexión] en Xcode.

### SDK de la versión beta
Para agregar compatibilidad sin conexión con una aplicación existente, obtenga el [SDK de la versión beta de iOS](http://aka.ms/gc6fex).

## <a name="review-sync"></a>Revisión del código de sincronización de Aplicaciones móviles

La sincronización sin conexión de Aplicaciones móviles de Azure permite a los usuarios finales interactuar con una base de datos local cuando la red no está accesible. Para utilizar estas características en su aplicación, inicialice el contexto de sincronización de `MSClient` y haga referencia a un almacén local. A continuación, obtenga una referencia a la tabla mediante la interfaz `MSSyncTable`.

Esta sección le guía a través del código relacionado con la sincronización sin conexión en la muestra.

1. En **QSTodoService.m**, observe que el tipo del miembro `syncTable` es `MSSyncTable`. La sincronización sin conexión usa esta interfaz de tabla de sincronización en lugar de `MSTable`. Cuando se utiliza una tabla de sincronización, todas las operaciones van al almacén local y solo se sincronizan con el back-end remoto con operaciones de inserción y extracción explícitas.

    Para obtener una referencia a una tabla de sincronización, utilice el método `syncTableWithName`. Para quitar la funcionalidad de sincronización sin conexión, use `tableWithName` en su lugar.

3. Antes de poder realizar cualquier operación de tabla, se debe inicializar el almacén local. Este es el código pertinente en el método `QSTodoService.init`:

        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
        
        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];

    Esto crea un almacén local mediante la interfaz `MSCoreDataStore`, que se proporciona en el SDK de Aplicaciones móviles. En su lugar puede proporcionar otro almacén local mediante la implementación del protocolo `MSSyncContextDataSource`.

    El primer parámetro de `initWithDelegate` se utiliza para especificar un controlador de conflictos. Puesto que hemos pasado `nil`, obtenemos el controlador de conflictos predeterminado, que produce un error en cualquier conflicto. 

<!-- For details on how to implement a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services]. -->

4. Los métodos `pullData` y `syncData` realizan la operación de sincronización real: `syncData` inserta primero los nuevos cambios y, a continuación, se llama a `pullData` para obtener datos desde el back-end remoto.

        -(void)syncData:(QSCompletionBlock)completion
        {
            // push all changes in the sync context, then pull new data
            [self.client.syncContext pushWithCompletion:^(NSError *error) {
                [self logErrorIfNotNil:error];
                [self pullData:completion];
            }];
        }

    A su vez, el método `pullData` obtiene datos nuevos que coinciden con una consulta:

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

    En `syncData`, llamamos primero `pushWithCompletion` en el contexto de sincronización. Este método es miembro de `MSSyncContext` y no de la tabla de sincronización porque insertará cambios en todas las tablas. Solo los registros que se han modificado localmente de alguna forma (mediante operaciones CUD) se enviarán al servidor. A continuación, se llama a la aplicación auxiliar `pullData`, que llama a `MSSyncTable.pullWithQuery` para recuperar datos remotos y almacenarlos en la base de datos local. 

    Tenga en cuenta que, en este ejemplo, la operación de inserción no es estrictamente necesaria. Si hay cambios pendientes en el contexto de sincronización para la tabla que está realizando una operación de inserción, la extracción siempre realiza primero una inserción. Sin embargo, si tiene más de una tabla de sincronización, es mejor llamar explícitamente a la inserción para asegurarse de que todo sea coherente en las tablas relacionadas.

    El método `pullWithQuery` le permite especificar una consulta para filtrar los registros que desea recuperar. En este ejemplo, la consulta recupera simplemente todos los registros en la tabla `TodoItem` remota.

    El segundo parámetro para `pullWithQuery` es un identificador de consulta que se utiliza para la *sincronización incremental*. La sincronización incremental recupera solo aquellos registros modificados desde la última sincronización, mediante la marca de tiempo `UpdatedAt` del registro (llamada `ms_updatedAt` en el almacén local). El identificador de la consulta debe ser una cadena descriptiva que sea única para cada consulta lógica en la aplicación. Para la desactivación de la sincronización incremental, pase `nil` como identificador de la consulta. Tenga en cuenta que esto puede resultar potencialmente ineficaz, ya que se recuperarán todos los registros de cada operación de extracción.

<!--     >[AZURE.NOTE] Para quitar registros del almacén local del dispositivo cuando se han eliminado de la base de datos del servicio móvil, debe habilitar la [eliminación temporal]. De lo contrario, la aplicación podría llamar periódicamente a `MSSyncTable.purgeWithQuery` para purgar el almacén local.
 -->

5. En la clase `QSTodoService`, se llama al método  `syncData` después de las operaciones que modifican datos, `addItem` y `completeItem`. También se le llama desde `QSTodoListViewController.refresh`, de modo que el usuario obtiene los datos más recientes cada vez que realizan los gestos de actualización. La aplicación también lleva a cabo una sincronización en el inicio, ya que `QSTodoListViewController.init` llama a `refresh`.

    Dado que `syncData` se llama cada vez que se modifican datos, esta aplicación supone que el usuario está en línea siempre que se editan datos. En otra sección, actualizaremos la aplicación para que los usuarios puedan editar incluso cuando estén sin conexión.

## <a name="review-core-data"></a>Revisión del modelo Core Data

Cuando se usa el almacén sin conexión Core Data, tendrá que definir tablas y campos determinados en el modelo de datos. La aplicación de ejemplo ya incluye un modelo de datos con el formato correcto. En esta sección se le guiará a través de estas tablas y el modo de utilizarlas.

- Abra **QSDataModel.xcdatamodeld**. Hay cuatro tablas definidas: tres que utiliza el SDK y una para los propios elementos de la lista de tareas:
      * MS_TableOperations: para realizar el seguimiento de los elementos que deben sincronizarse con el servidor
      * MS_TableOperationErrors: para realizar el seguimiento de los errores que se producen durante la sincronización sin conexión 
      * MS_TableConfig: para realizar el seguimiento de la hora de la última actualización de la última operación de sincronización para todas las operaciones de extracción
      * TodoItem: para almacenar los elementos de lista de tareas Las columnas de sistema **ms_createdAt**, **ms_updatedAt** y **ms_version** son propiedades del sistema opcionales. 

>[AZURE.NOTE] El SDK de Aplicaciones móviles reserva los nombres de columna que contienen "**`ms_`**". No debe utilizar este prefijo en otra cosa que no sean las columnas del sistema; en caso contrario, se modificarán los nombres de columna cuando se use el back-end remoto.

- Al utilizar la característica de sincronización sin conexión, debe definir las tablas del sistema, tal como se muestra a continuación.

    ### Tablas del sistema

    **MS_TableOperations**

    ![][defining-core-data-tableoperations-entity]

    | Atributo  |    Tipo     |
    |----------- |   ------    |
    | id         | Integer 64  |
    | itemId     | String      |
    | propiedades | Binary Data |
    | table      | String      |
    | tableKind  | Integer 16  |

    <br>**MS_TableOperationErrors**

    ![][defining-core-data-tableoperationerrors-entity]

    | Atributo  |    Tipo     |
    |----------- |   ------    |
    | id         | String      |
    | operationId | Integer 64 |
    | propiedades | Binary Data |
    | tableKind  | Integer 16  |

    <br>**MS_TableConfig**

    ![][defining-core-data-tableconfig-entity]

    | Atributo  |    Tipo     |
    |----------- |   ------    |
    | id         | Integer 64  |
    | key        | String      |
    | keyType    | Integer 64  |
    | table      | String      |
    | value      | String      |

    ### Tabla de datos

    ![][defining-core-data-todoitem-entity]

    **TodoItem**


    | Atributo    |  Tipo   | Nota:                                                   | 
    |-----------   |  ------ | -------------------------------------------------------|
    | id           | String  | primary key in remote store                            |
    | complete     | Boolean | todo item field                                        |
    | text         | String  | todo item field                                        |
    | ms_createdAt | Date    | (optional) maps to __createdAt system property         |
    | ms_updatedAt | Date    | (optional) maps to __updatedAt system property         |
    | ms_version   | String  | (optional) used to detect conflicts, maps to __version |


## <a name="setup-sync"></a>Cambio del comportamiento de sincronización de la aplicación

En esta sección, modificará la aplicación para que no se sincronice en el inicio de la aplicación o al insertar y actualizar elementos, sino solo cuando se realice el gesto de actualización. 

1. En **QSTodoListViewController.m**, cambie el método **viewDidLoad** para quitar la llamada a `[self refresh]` al final del método. Ahora, los datos no se sincronizarán con el servidor al inicio de la aplicación, sino que lo hará el contenido del almacén local.

2. En **QSTodoService.m**, modifique la definición de `addItem` para que no se sincronice tras la inserción del elemento. Quite el bloque `self syncData` y sustitúyalo por lo siguiente:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. Modifique la definición de `completeItem` como anteriormente; quite el bloque de `self syncData` y sustitúyalo por lo siguiente:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

## <a name="test-app"></a>Prueba de la aplicación


En esta sección, desactivará el Wi-Fi en el simulador para crear un escenario sin conexión. Al agregar elementos de datos, estos se guardarán en el almacén local Core Data, pero no se sincronizarán con el back-end móvil.

1. Desactive el Wi-Fi en el simulador de iOS.

2. Agregue algunos elementos de la lista de pendientes o complete algunos elementos. Salga del simulador (o fuerce el cierre de la aplicación) y reinicie. Compruebe que se han guardado los cambios.

3. Vea el contenido de la tabla TodoItem remota:
   - Para el back-end de JavaScript, vaya al Portal de administración y haga clic en la pestaña Datos para ver el contenido de la tabla `TodoItem`.
   - Para el back-end de .NET, vea el contenido de tabla con una herramienta SQL, como SQL Server Management Studio, o con un cliente REST como Fiddler o Postman.

    Compruebe que los nuevos elementos *not* se han sincronizado con el servidor:

4. Active el Wi-Fi en el simulador de iOS y, a continuación, realice el gesto de actualización desplegando la lista de elementos. Verá una rueda con el progreso y el texto "Sincronizando...".

5. Vea los datos de TodoItem de nuevo. Ahora deberían aparecer los elementos de TodoItems nuevos y modificados.

## Resumen

Para admitir la característica de sincronización sin conexión, usamos la interfaz `MSSyncTable` e inicializamos `MSClient.syncContext` con un almacén local. En este caso, el almacén local era una base de datos Core Data. 

Cuando se utiliza un almacén local Core Data, debe definir varias tablas con las [propiedades del sistema de corrección][Revisión del modelo Core Data].

Las operaciones CRUD normales para Aplicaciones móviles funcionan como si la aplicación siguiera conectada, pero todas las operaciones se producen en el almacén local.

Cuando quisimos sincronizar el almacén local con el servidor, usamos los métodos `MSSyncTable.pullWithQuery` y `MSClient.syncContext.pushWithCompletion`.

*  Para insertar cambios en el servidor, llamamos a `Review the Core Data model`. Este método es miembro de `MSSyncContext` y no de la tabla de sincronización porque insertará cambios en todas las tablas.

    Solo los registros que se han modificado localmente de alguna forma (mediante operaciones CUD) se enviarán al servidor.
   
* Para extraer datos de una tabla del servidor en la aplicación, llamamos a `MSSyncTable.pullWithQuery`.

    Una extracción siempre realiza primero una inserción. De este modo, se asegurará de que todas las tablas del almacén local sean coherentes con las relaciones.

    Tenga en cuenta que `pullWithQuery` puede utilizarse para filtrar los datos que se almacenan en el cliente mediante la personalización del parámetro `query`. 

* Para habilitar la sincronización incremental, pase un identificador de consulta a `pullWithQuery`. El identificador de consulta se utiliza para almacenar la última marca de tiempo actualizada de los resultados de la última operación de extracción. El identificador de la consulta debe ser una cadena descriptiva que sea única para cada consulta lógica en la aplicación. Si la consulta tiene un parámetro, el mismo valor de parámetro debe formar parte del identificador de consulta.

    Si desea cancelar la sincronización incremental, pase `nil` como identificador de consulta. En este caso, se recuperarán todos los registros en cada llamada a `pullWithQuery`, que es potencialmente ineficaz.

<!-- * Para quitar registros del almacén local del dispositivo cuando se han eliminado de la base de datos del servicio móvil, debe habilitar la [eliminación temporal]. De lo contrario, la aplicación podría llamar periódicamente a `MSSyncTable.purgeWithQuery` para quitar los registros de la base de datos local, en caso de que se hayan eliminado en el servicio remoto.
 -->

## Recursos adicionales

* [Descripción de la nube: Sincronización sin conexión en Servicios móviles de Azure]

* [Azure Friday: Aplicaciones habilitadas sin conexión en Servicios móviles de Azure] \ (Nota: las demostraciones son para Windows, pero las características tratadas son aplicables a todas las plataformas\)

<!-- URLs. -->

[Creación de una aplicación iOS]: app-service-mobile-dotnet-backend-ios-get-started.md

[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png
[defining-core-data-tableoperationerrors-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-todoitem-entity.png
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png

[Ayuda del editor de modelos de Core Data]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creación de una conexión de salida]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Creación de una interfaz de usuario]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Adición de un elemento Segue entre escenas de un guión gráfico]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adición de una escena a un guion gráfico]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Descarga del SDK de versión preliminar aquí]: http://aka.ms/Gc6fex
[Uso de la biblioteca de cliente de Servicios móviles para iOS]: mobile-services-ios-how-to-use-client-library.md
[Ejemplo de iOS sin conexión]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Repositorio de muestras de Aplicaciones móviles en GitHub]: https://github.com/Azure/mobile-services-samples

[Introducción a los Servicios móviles]: mobile-services-ios-get-started.md
[Introducción a los datos]: mobile-services-ios-get-started-data.md
[Control de conflictos con compatibilidad sin conexión para Servicios móviles]: mobile-services-ios-handling-conflicts-offline-data.md
[Eliminación temporal]: mobile-services-using-soft-delete.md

[Descripción de la nube: Sincronización sin conexión en Servicios móviles de Azure]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Aplicaciones habilitadas sin conexión en Servicios móviles de Azure]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!--HONumber=49-->