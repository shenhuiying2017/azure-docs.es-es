<properties urlDisplayName="Using Offline Data" pageTitle="Uso de la sincronización de datos sin conexión en Servicios móviles (iOS) | Centro de desarrollo móvil" metaKeywords="" description="Obtenga información acerca de cómo usar Servicios móviles de Azure para almacenar en caché y sincronizar datos sin conexión en su aplicación iOS" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using Offline data sync in Mobile Services" authors="krisragh" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Introducción a la sincronización de datos sin conexión en Servicios móviles


[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


En este tutorial se trata la característica de sincronización sin conexión de Servicios móviles en iOS, que permite a los desarrolladores escribir aplicaciones que se pueden usar incluso cuando el usuario final no tiene acceso de red.

La sincronización sin conexión tiene varios usos posibles:

* Mejore la capacidad de respuesta de las aplicaciones almacenando en caché datos de servidor de forma local en el dispositivo.
* Hacer que las aplicaciones sean resistentes ante una conectividad de red intermitente.
* Permitir a los usuarios finales crear y modificar datos incluso cuando no hay acceso de red, proporcionando escenarios con muy poca o ninguna conectividad.
* Sincronizar datos entre diferentes dispositivos y detectar conflictos cuando dos dispositivos modifican el mismo registro.

	Este tutorial le mostrará cómo actualizar la aplicación del tutorial [Introducción a Servicios móviles] para que admita las características sin conexión de Servicios móviles de Azure. A continuación, agregará datos en un escenario sin conexión desconectado, sincronizará dichos elementos con la base de datos en línea e iniciará sesión en el Portal de administración de Azure para ver los cambios efectuados en los datos al ejecutar la aplicación.

>[WACOM.NOTE] Necesita una cuenta de Azure para completar este tutorial. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 servicios móviles gratuitos que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Evaluación gratuita de Azure</a>.

El objeto de este tutorial es profundizar en el uso de Azure con Servicios móviles para almacenar y recuperar datos en una aplicación de la Tienda Windows. Para ello, en este tema se recorren muchos de los pasos que se completan automáticamente en el inicio rápido de Servicios móviles. Si esta es la primera vez que usa Servicios móviles, considere realizar antes el tutorial [Introducción a Servicios móviles].

>[WACOM.NOTE] Puede omitir estas secciones y pasar a descargar una versión del proyecto de introducción que ya incluye funcionalidad sin conexión y todos los elementos que se describen en este tema.  Para descargar un proyecto con la funcionalidad sin conexión habilitada, consulte [Ejemplo de sincronización sin conexión en iOS].


Este tutorial le guiará a través de estos pasos básicos:

1. [Obtención de la aplicación de ejemplo Quickstart]
2. [Descarga de la versión de vista previa del SDK y actualización del marco de trabajo]
3. [Configuración de Core Data]
4. [Definición del modelo de datos principal]
5. [Inicialización y uso de la tabla de sincronización y el contexto de sincronización]
6. [Prueba de la aplicación]

## <a name="get-app"></a>Obtención de la aplicación de ejemplo Quickstart

Siga las instrucciones del tutorial [Introducción a Servicios móviles] y descargue el proyecto Quickstart.

## <a name="update-app"></a>Descarga de la versión de vista previa del SDK y actualización del marco de trabajo

1. Para agregar compatibilidad sin conexión a nuestra aplicación, vamos a obtener una versión del SDK de Servicios móviles para iOS que permite la sincronización sin conexión. Puesto que vamos a iniciarlo como una característica de vista previa, no está todavía en el SDK oficialmente descargable. [Descargue la versión de vista previa del SDK aquí].

2. Después, quite la referencia **WindowsAzureMobileServices.framework** actual del proyecto en Xcode. Para ello, selecciónela y haga clic en el menú **Edit** (Editar) y elija "Move to Trash" (Mover a la papelera) para eliminar realmente los archivos.

      ![][update-framework-1]

3. Descomprima el contenido de la nueva vista previa del SDK y en lugar del antiguo SDK, arrastre y coloque el nuevo SDK **WindowsAzureMobileServices.framework**. Asegúrese de que está seleccionada la opción "Copy items into destination group's folder (if needed)" (Copiar elementos en la carpeta del grupo de destino (si es necesario)).

      ![][update-framework-2]


## <a name="setup-core-data"></a>Configuración de Core Data

1. El SDK de Servicios móviles para iOS permite utilizar cualquier almacén persistente siempre que cumpla el protocolo **MSSyncContextDataSource**. El SDK incluye un origen de datos que implementa este protocolo basado en [Core Data].

2. Puesto que la aplicación utiliza Core Data, navegue a **Targets** (Destinos) --> **Build Phases** (Fases de compilación) y, en **Link Binary with Libraries** (Vincular binarios con bibliotecas), agregue **CoreData.framework**.

      ![][core-data-1]

      ![][core-data-2]

3. Estamos agregando Core Data a un proyecto existente en Xcode que ya no admite Core Data. Por lo tanto, tenemos que agregar código adicional a distintas partes del proyecto. En primer lugar, agregue el código siguiente en **QSAppDelegate.h**:

        #import <UIKit/UIKit.h>  
        #import <CoreData/CoreData.h>  

        @interface QSAppDelegate : UIResponder <UIApplicationDelegate>  

        @property (strong, nonatomic) UIWindow *window;  

        @property (readonly, strong, nonatomic) NSManagedObjectContext *managedObjectContext;  
        @property (readonly, strong, nonatomic) NSManagedObjectModel *managedObjectModel;  
        @property (readonly, strong, nonatomic) NSPersistentStoreCoordinator *persistentStoreCoordinator;  

        - (void)saveContext;  
        - (NSURL *)applicationDocumentsDirectory;  

        @end

4. A continuación, reemplace el contenido de **QSAppDelegate.m** por el código siguiente. Este es prácticamente el mismo código que aparece al crear una nueva aplicación en Xcode y seleccionar la casilla "Use Core Data" (Usar Core Data), salvo que usa un tipo de simultaneidad de cola privada al inicializar **_managedObjectContext**. Con este cambio, está casi listo para usar Core Data, pero no está haciendo nada todavía con él.

        #import "QSAppDelegate.h"

        @implementation QSAppDelegate

        @synthesize managedObjectContext = _managedObjectContext;
        @synthesize managedObjectModel = _managedObjectModel;
        @synthesize persistentStoreCoordinator = _persistentStoreCoordinator;

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            return YES;
        }

        - (void)saveContext
        {
            NSError *error = nil;
            NSManagedObjectContext *managedObjectContext = self.managedObjectContext;
            if (managedObjectContext != nil) {
                if ([managedObjectContext hasChanges] && ![managedObjectContext save:&error]) {
                    // Replace this implementation with code to handle the error appropriately.
                    // abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
                    NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
                    abort();
                }
            }
        }

        #pragma mark - Core Data stack

        // Returns the managed object context for the application.
        // If the context doesn't already exist, it is created and bound to the persistent store coordinator for the application.
        - (NSManagedObjectContext *)managedObjectContext
        {
            if (_managedObjectContext != nil) {
                return _managedObjectContext;
            }

            NSPersistentStoreCoordinator *coordinator = [self persistentStoreCoordinator];
            if (coordinator != nil) {
                _managedObjectContext = [[NSManagedObjectContext alloc] initWithConcurrencyType:NSPrivateQueueConcurrencyType];
                [_managedObjectContext setPersistentStoreCoordinator:coordinator];
            }
            return _managedObjectContext;
        }

        // Returns the managed object model for the application.
        // If the model doesn't already exist, it is created from the application's model.
        - (NSManagedObjectModel *)managedObjectModel
        {
            if (_managedObjectModel != nil) {
                return _managedObjectModel;
            }
            NSURL *modelURL = [[NSBundle mainBundle] URLForResource:@"QSTodoDataModel" withExtension:@"momd"];
            _managedObjectModel = [[NSManagedObjectModel alloc] initWithContentsOfURL:modelURL];
            return _managedObjectModel;
        }

        // Returns the persistent store coordinator for the application.
        // If the coordinator doesn't already exist, it is created and the application's store added to it.
        - (NSPersistentStoreCoordinator *)persistentStoreCoordinator
        {
            if (_persistentStoreCoordinator != nil) {
                return _persistentStoreCoordinator;
            }

            NSURL *storeURL = [[self applicationDocumentsDirectory] URLByAppendingPathComponent:@"qstodoitem.sqlite"];

            NSError *error = nil;
            _persistentStoreCoordinator = [[NSPersistentStoreCoordinator alloc] initWithManagedObjectModel:[self managedObjectModel]];
            if (![_persistentStoreCoordinator addPersistentStoreWithType:NSSQLiteStoreType configuration:nil URL:storeURL options:nil error:&error]) {
                /*
                 Replace this implementation with code to handle the error appropriately.

                 abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.

                 Typical reasons for an error here include:
                 * The persistent store is not accessible;
                 * The schema for the persistent store is incompatible with current managed object model.
                 Check the error message to determine what the actual problem was.

                 If the persistent store is not accessible, there is typically something wrong with the file path. Often, a file URL is pointing into the application's resources directory instead of a writeable directory.

                 If you encounter schema incompatibility errors during development, you can reduce their frequency by:
                 * Simply deleting the existing store:
                 [[NSFileManager defaultManager] removeItemAtURL:storeURL error:nil]

                 * Performing automatic lightweight migration by passing the following dictionary as the options parameter:
                 @{NSMigratePersistentStoresAutomaticallyOption:@YES, NSInferMappingModelAutomaticallyOption:@YES}

                 Lightweight migration will only work for a limited set of schema changes; consult "Core Data Model Versioning and Data Migration Programming Guide" for details.

                 */

                NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
                abort();
            }

            return _persistentStoreCoordinator;
        }

        #pragma mark - Application's Documents directory

        // Returns the URL to the application's Documents directory.
        - (NSURL *)applicationDocumentsDirectory
        {
            return [[[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask] lastObject];
        }

        @end

## <a name="defining-core-data"></a>Definición del modelo de datos principal

1. Vamos a seguir configurando la aplicación con Core Data definiendo el modelo de datos. No comenzaremos a utilizar este modelo aún. Primero, vamos a definir el modelo de datos principal o esquema. Para empezar, haga clic en **File ** (Archivo) -> **New File** (Nuevo archivo) y seleccione **Data Model** (Modelo de datos) en la sección **Core Data**. Cuando se le pida un nombre de archivo, utilice **QSTodoDataModel.xcdatamodeld**.

      ![][defining-core-data-main-screen]

2. A continuación, vamos a definir las entidades reales (tablas) que necesitamos. Vamos a crear tres tablas (entidades) con el editor de modelos de Core Data. Para obtener más información, consulte la [Ayuda del editor de modelos de Core Data.].

  * TodoItem: para almacenar los propios elementos
  * MS_TableOperations: para realizar el seguimiento de los elementos que deben sincronizarse con el servidor (es necesario para que funcione la característica sin conexión)
  * MS_TableOperationErrors: para realizar el seguimiento de los errores que se producen durante la sincronización sin conexión (es necesaria para que funcione la característica sin conexión)

      ![][defining-core-data-model-editor]

3. Defina las tres entidades tal como se muestra a continuación. Guarde el modelo y compile el proyecto para asegurarse de que todo está bien. Ahora hemos terminado de configurar la aplicación para trabajar con Core Data, pero la aplicación no lo está utilizando todavía.

      ![][defining-core-data-todoitem-entity]

      ![][defining-core-data-tableoperations-entity]

      ![][defining-core-data-tableoperationerrors-entity]


    **TodoItem**

    | Atributo  |  Tipo   |
    |----------- |  ------ |
    | id         | Cadena  |
    | complete   | Booleano |
    | text       | Cadena  |
    | ms_version | Cadena  |

    **MS_TableOperations**

    | Atributo  |    Tipo     |
    |----------- |   ------    |
    | id         | Entero 64  |
    | properties | Datos binarios |
    | itemId     | Cadena      |
    | table      | Cadena      |

    **MS_TableOperationErrors**

    | Atributo  |    Tipo     |
    |----------- |   ------    |
    | id         | Cadena      |
    | properties | Datos binarios |

## <a name="setup-sync"></a> Inicialización y uso de la tabla de sincronización y el contexto de sincronización

1. Para iniciar el almacenamiento en caché de datos sin conexión, vamos a reemplazar el uso de **MSTable** por **MSSyncTable** para obtener acceso al servicio móvil. A diferencia de una tabla **MSTable** normal, una tabla de sincronización es como una tabla local que agrega la capacidad para insertar los cambios realizados localmente en una tabla remota y extraer los cambios de forma local. En **QSTodoService.h**, quite la definición de la propiedad **table**:

        @property (nonatomic, strong)   MSTable *table;

    Add a new line to define the **syncTable** property:

        @property (nonatomic, strong)   MSTable *syncTable;

2. Add the following import statement at the top of **QSTodoService.m**:

        #import "QSAppDelegate.h"

3. En **QSTodoService.m**, quite las dos líneas siguientes en **init**:

        // Create an MSTable instance to allow us to work with the TodoItem table
        self.table = [_client tableWithName:@"TodoItem"];

    Instead, add these two new lines in its place:

        // Create an MSSyncTable instance to allow us to work with the TodoItem table
        self.syncTable = [self.client syncTableWithName:@"TodoItem"];

4. A continuación, de nuevo en **QSTodoService.m**, inicialice el contexto de sincronización en el **MSClient** con la implementación de almacén de datos basado en datos anterior. El contexto es responsable de realizar el seguimiento de los elementos que se han cambiado localmente y enviarlos al servidor cuando se inicia una operación de inserción. Para inicializar el contexto, necesitamos un origen de datos (la implementación de **MSCoreDataStore** del protocolo) y una implementación de **MSSyncContextDelegate** opcional. Inserte estas líneas justo encima de las dos líneas insertadas anteriormente.

        QSAppDelegate *delegate = (QSAppDelegate *)[[UIApplication sharedApplication] delegate];
        NSManagedObjectContext *context = delegate.managedObjectContext;
        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];

5. A continuación, vamos a actualizar las operaciones de **QSTodoService.m** para usar la tabla de sincronización en lugar de la tabla normal. En primer lugar, reemplace **refreshDataOnSuccess** por la siguiente implementación. Esto recupera los datos desde el servicio, por lo que vamos a actualizarlo para usar una tabla de sincronización, pedir a la tabla de sincronización que extraiga solo los elementos que coinciden con los criterios y cargar datos de la tabla de sincronización local en la propiedad **items** del servicio. Con este código,  **refreshDataOnSuccess** extrae los datos de la tabla remota en la tabla local (sincronización). Generalmente nos debemos extraer solo un subconjunto de la tabla para que no sobrecargar el cliente con la información que no necesite.

    Para esto y las operaciones restantes que se indican más adelante, se encierran las llamadas a los bloques de finalización en una llamada de **dispatch_async** al subproceso principal. Cuando se inicializa el contexto de sincronización, no se pasa un parámetro de devolución de llamada, por lo que el marco de trabajo crea una cola de serie predeterminada que envía los resultados de todas las operaciones de syncTable en un subproceso en segundo plano. Para modificar los componentes de interfaz de usuario, es necesario distribuir el código nuevo al subproceso de interfaz de usuario.

          -(void) refreshDataOnSuccess:(QSCompletionBlock)completion
          {
              NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
              MSQuery *query = [self.syncTable queryWithPredicate:predicate];

              [query orderByAscending:@"text"];
              [query readWithCompletion:^(MSQueryResult *result, NSError *error) {
                  [self logErrorIfNotNil:error];

                  self.items = [result.items mutableCopy];

                  // Let the caller know that we finished
                  dispatch_async(dispatch_get_main_queue(), ^{
                      completion();
                  });
              }];
          }

6. Después, reemplace **addItem** en **QSTodoService.m** como se indica a continuación. Con este cambio, pone en cola la operación para que inserte los cambios en el servicio remoto y haga que esté visible para todos los usuarios:

        -(void)addItem:(NSDictionary *)item completion:(QSCompletionWithIndexBlock)completion
        {
            // Insert the item into the TodoItem table and add to the items array on completion
            [self.syncTable insert:item completion:^(NSDictionary *result, NSError *error)
             {
                 [self logErrorIfNotNil:error];

                 NSUInteger index = [items count];
                 [(NSMutableArray *)items insertObject:result atIndex:index];

                 // Let the caller know that we finished
                 dispatch_async(dispatch_get_main_queue(), ^{
                     completion(index);
                 });
             }];
        }

7. Actualice **completeItem** en **QSTodoService.m** como se indica a continuación. A diferencia de **MSTable**, el bloque de finalización de la operación **update** para **MSSyncTable** no tiene ningún elemento actualizado. Con **MSTable**, el servidor modifica el elemento que se está actualizando y esa modificación se refleja en el cliente. Con **MSSyncTable**, no se modifican los elementos actualizados y el bloque de finalización no tiene un parámetro.

        -(void) completeItem:(NSDictionary *)item completion:(QSCompletionWithIndexBlock)completion
        {
            // Cast the public items property to the mutable type (it was created as mutable)
            NSMutableArray *mutableItems = (NSMutableArray *) items;

            // Set the item to be complete (we need a mutable copy)
            NSMutableDictionary *mutable = [item mutableCopy];
            [mutable setObject:@YES forKey:@"complete"];

            // Replace the original in the items array
            NSUInteger index = [items indexOfObjectIdenticalTo:item];
            [mutableItems replaceObjectAtIndex:index withObject:item];

            // Update the item in the TodoItem table and remove from the items array on completion
            [self.syncTable update:mutable completion:^(NSError *error) {

                [self logErrorIfNotNil:error];

                NSUInteger index = [items indexOfObjectIdenticalTo:mutable];
                if (index != NSNotFound)
                {
                    [mutableItems removeObjectAtIndex:index];
                }

                // Let the caller know that we have finished
                dispatch_async(dispatch_get_main_queue(), ^{
                    completion(index);
                });

            }];
        }

8. Agregue la siguiente declaración de **syncData** a **QSTodoService.h**:

        - (void)syncData:(QSCompletionBlock)completion;

     Add the corresponding implementation of **syncData** to **QSTodoService.m**. We're adding this operation to update the sync table with remote changes.

          -(void)syncData:(QSCompletionBlock)completion
           {
              // Create a predicate that finds items where complete is false
              NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

              MSQuery *query = [self.syncTable queryWithPredicate:predicate];

              // Pulls data from the remote server into the local table. We're only
              // pulling the items which we want to display (complete == NO).
              [self.syncTable pullWithQuery:query completion:^(NSError *error) {
                  [self logErrorIfNotNil:error];
                  [self refreshDataOnSuccess:completion];
           }];
          }

9. Back in **QSTodoListViewController.m**, change the implementation of **refresh** to call **syncData** instead of **refreshDataOnSuccess**:

        -(void) refresh
        {
            [self.refreshControl beginRefreshing];
            [self.todoService syncData:^
             {
                  [self.refreshControl endRefreshing];
                  [self.tableView reloadData];
             }];
        }

10. De nuevo en **QSTodoListViewController.m**, reemplace la llamada a **[self refresh]** al final de la operación **viewDidLoad** por el siguiente código:

        // load the local data, but don't pull from server
        [self.todoService refreshDataOnSuccess:^
         {
             [self.refreshControl endRefreshing];
             [self.tableView reloadData];
         }];

11. Ahora, probemos realmente la aplicación sin conexión. Agregue algunos elementos a la aplicación, visite el Portal de administración de Azure y examine la ficha **Datos** de la aplicación. Verá que no se ha agregado todavía ningún elemento.

12. A continuación, realice el gesto de actualización en la aplicación arrastrándolo desde la parte superior. Visite el Portal de administración de Azure de nuevo y vuelva a observar la ficha **Datos**. Ahora sí verá los datos guardados en la nube. También puede cerrar la aplicación después de agregar un elemento (o después de la edición, si la aplicación tiene la funcionalidad habilitada para editar elementos.) Cuando se reinicie la aplicación, se sincronizará con el servidor y guardará los cambios.

13. Cuando el cliente realiza cambios en los elementos de forma local, los cambios se almacenan en el contexto de sincronización para enviarse al servidor. Una operación *push* envía los cambios realizados al servidor remoto, pero, en este caso, no tenemos llamadas de inserción al servidor. Sin embargo, *antes de ejecutar una operación pull, se envían al servidor (con una operación push) todas las operaciones pendientes*, de forma que tiene lugar al mismo tiempo una operación push para evitar conflictos. Este es el motivo por el que no hay llamadas explícitas a *push* en esta aplicación.

## <a name="test-app"></a>Prueba de la aplicación

Por último, vamos a probar la aplicación sin conexión. Agregue algunos elementos en la aplicación. A continuación, vaya al portal y examine los datos (o utilice una herramienta de red como PostMan o Fiddler para consultar directamente la tabla).

Verá que los elementos no se agregaron al servicio todavía. Realice ahora el gesto de actualizar en la aplicación arrastrándola desde la parte superior. Ahora verá que los datos se han guardado en la nube. Incluso puede cerrar la aplicación después de agregar algunos elementos. Cuando inicie la aplicación de nuevo se sincronizará con el servidor y guardará los cambios.

## Pasos siguientes

* [Control de conflictos con compatibilidad sin conexión para Servicios móviles]

<!-- URLs. -->

[Obtención de la aplicación de ejemplo Quickstart]: #get-app
[Descarga de la versión de vista previa del SDK y actualización del marco de trabajo]: #update-app
[Configuración de Core Data]: #setup-core-data
[Definición del modelo de datos principal]: #defining-core-data
[Inicialización y uso de la tabla de sincronización y el contexto de sincronización]: #setup-sync
[Prueba de la aplicación]: #test-app

[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png
[defining-core-data-tableoperationerrors-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-todoitem-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-todoitem-entity.png
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png




[Ayuda del editor de modelos de datos principales]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creación de una conexión de salida]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Creación de una interfaz de usuario]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Adición de un Segue entre bastidores en un guión gráfico]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adición de una escena a un guión gráfico]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Descargue la vista previa del SDK aquí.]: http://aka.ms/Gc6fex
[Uso de la biblioteca de cliente de Servicios móviles para iOS]: /es-es/documentation/articles/mobile-services-ios-how-to-use-client-library/
[Ejemplo de sincronización sin conexión en iOS]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611


[Introducción a Servicios móviles]: /es-es/documentation/articles/mobile-services-ios-get-started/
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-ios-get-started-data/
[Control de conflictos con compatibilidad sin conexión para Servicios móviles]: /es-es/documentation/articles/mobile-services-ios-handling-conflicts-offline-data/
