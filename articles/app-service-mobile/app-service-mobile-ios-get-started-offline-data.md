---
title: "Habilitación de la sincronización sin conexión con aplicaciones móviles iOS | Microsoft Docs"
description: "Aprenda a usar App Service Mobile Apps para almacenar en caché y sincronizar datos sin conexión en aplicaciones iOS."
documentationcenter: ios
author: conceptdev
manager: crdun
editor: 
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: b676b51241e4883fb1b4c40caba8e281bfa68a4c
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Habilitación de la sincronización sin conexión con aplicaciones móviles iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Información general
En este tutorial, se trata la sincronización sin conexión con la característica Mobile Apps de Azure App Service para iOS. Con la sincronización sin conexión, los usuarios finales pueden interactuar con una aplicación móvil para ver, agregar o modificar datos, incluso si carecen de conexión a red. Los cambios se almacenan en una base de datos local. Una vez que el dispositivo se vuelve a conectar, los cambios se sincronizan con el back-end remoto.

Si esta es la primera vez que usa Mobile Apps, primero debería completar el tutorial [Creación de una aplicación iOS]. Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar los paquetes de extensión de acceso de datos al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para aprender acerca de la característica de sincronización sin conexión, consulte [Sincronización de datos sin conexión en Mobile Apps].

## <a name="review-sync"></a>Revisión del código de sincronización de cliente
El proyecto de cliente que descargó para el tutorial [Creación de una aplicación iOS] ya contiene el código que admite la sincronización sin conexión con una base de datos local basada en Core Data. En esta sección, se resume lo que ya está incluido en el código del tutorial. Para ver información general conceptual sobre la característica, consulte [Sincronización de datos sin conexión en Mobile Apps].

Mediante la característica de sincronización de datos sin conexión de Mobile Apps, los usuarios finales pueden interactuar con una base de datos local incluso si la red no está accesible. Para utilizar estas características en su aplicación, inicialice el contexto de sincronización de `MSClient` y haga referencia a un almacén local. Después, haga referencia a la tabla mediante la interfaz **MSSyncTable**.

En **QSTodoService.m** (Objective-C) o **ToDoTableViewController.swift** (Swift), tenga en cuenta que el tipo del miembro **syncTable** es **MSSyncTable**. La sincronización sin conexión usa esta interfaz de tabla de sincronización en lugar de **MSTable**. Cuando se usa una tabla de sincronización, todas las operaciones van al almacén local y solo se sincronizan con el back-end remoto con operaciones de inserción y extracción explícitas.

 Para obtener una referencia a una tabla de sincronización, utilice el método **syncTableWithName** en `MSClient`. Para quitar la funcionalidad de sincronización sin conexión, use **tableWithName** en su lugar.

Antes de poder realizar cualquier operación de tabla, se debe inicializar el almacén local. Este es el código pertinente:

* **Objective-C**. En el método **QSTodoService.init**:

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Swift**. En el método **ToDoTableViewController.viewDidLoad**:

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Este método crea un almacén local mediante la interfaz `MSCoreDataStore`, que se proporciona en el SDK de Mobile Apps. Como alternativa, puede proporcionar otro almacén local mediante la implementación del protocolo `MSSyncContextDataSource`. Además, el primer parámetro de **MSSyncContext** se utiliza para especificar un controlador de conflictos. Puesto que se ha pasado `nil`, se obtiene el controlador de conflictos predeterminado, que produce un error para cualquier conflicto.

Ahora, se va a realizar la operación de sincronización en sí y se van a obtener datos desde el back-end remoto:

* **Objective-C**. `syncData` primero inserta nuevos cambios y luego llama a **pullData** para obtener datos desde el back-end remoto. A su vez, el método **pullData** obtiene datos nuevos que coinciden con una consulta:

   ```objc
   -(void)syncData:(QSCompletionBlock)completion
   {
       // Push all changes in the sync context, and then pull new data.
       [self.client.syncContext pushWithCompletion:^(NSError *error) {
           [self logErrorIfNotNil:error];
           [self pullData:completion];
       }];
   }

   -(void)pullData:(QSCompletionBlock)completion
   {
       MSQuery *query = [self.syncTable query];

       // Pulls data from the remote server into the local table.
       // We're pulling all items and filtering in the view.
       // Query ID is used for incremental sync.
       [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
           [self logErrorIfNotNil:error];

           // Lets the caller know that we have finished.
           if (completion != nil) {
               dispatch_async(dispatch_get_main_queue(), completion);
           }
       }];
   }
   ```
* **SWIFT**:
   ```swift
   func onRefresh(sender: UIRefreshControl!) {
      UIApplication.sharedApplication().networkActivityIndicatorVisible = true

      self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
          (error) -> Void in

          UIApplication.sharedApplication().networkActivityIndicatorVisible = false

          if error != nil {
              // A real application would handle various errors like network conditions,
              // server conflicts, etc via the MSSyncContextDelegate
              print("Error: \(error!.description)")

              // We will discard our changes and keep the server's copy for simplicity
              if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                  for opError in opErrors {
                      print("Attempted operation to item \(opError.itemId)")
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
   ```

En la versión Objective-C, en `syncData`, se llama primero a **pushWithCompletion** en el contexto de sincronización. Este método es miembro de `MSSyncContext` (no de la propia tabla de sincronización), porque inserta cambios en todas las tablas. Solo se envían al servidor los registros que se han modificado localmente de alguna forma (mediante operaciones CUD). A continuación, se llama a la aplicación auxiliar **pullData**, que llama a **MSSyncTable.pullWithQuery** para recuperar datos remotos y almacenarlos en la base de datos local.

En la versión Swift, como la operación de inserción no era estrictamente necesaria, no hay ninguna llamada a **pushWithCompletion**. Si hay cambios pendientes en el contexto de sincronización para la tabla que está realizando una operación de inserción, la extracción siempre realiza primero una inserción. Sin embargo, si tiene más de una tabla de sincronización, es mejor llamar explícitamente a la inserción para asegurarse de que todo sea coherente en las tablas relacionadas.

Tanto en la versión Objective-C como en la Swift, puede usar el método **pullWithQuery** para especificar una consulta para filtrar los registros que desea recuperar. En este ejemplo, la consulta recupera todos los registros en la tabla `TodoItem` remota.

El segundo parámetro de **pullWithQuery** es un identificador de consulta que se utiliza para la *sincronización incremental*. La sincronización incremental recupera solo los registros que se modificaron desde la última sincronización, haciendo uso de la marca de tiempo `UpdatedAt` del registro (llamada `updatedAt` en el almacén local). El identificador de la consulta debe ser una cadena descriptiva que sea única para cada consulta lógica en la aplicación. Para desactivar la sincronización incremental, pase `nil` como identificador de la consulta. Es posible que este enfoque resulte ineficaz, ya que recupera todos los registros en cada operación de extracción.

La aplicación Objective-C se sincroniza cuando se modifican o agregan datos, cuando un usuario realiza el gesto de actualización y en el inicio.

La aplicación Swift se sincroniza cuando el usuario realiza el gesto de actualización y en el inicio.

Dado que la aplicación se sincroniza cada vez que se modifican datos (Objective-C) o siempre que se inicia la aplicación (Objective-C y Swift), la aplicación supone que el usuario está en línea. En una sección posterior, actualizará la aplicación para que los usuarios puedan editar incluso cuando estén sin conexión.

## <a name="review-core-data"></a>Revisión del modelo Core Data
Cuando use el almacén sin conexión Core Data, debe definir tablas y campos determinados en el modelo de datos. La aplicación de ejemplo ya incluye un modelo de datos con el formato correcto. En esta sección se le guiará por estas tablas y el modo de utilizarlas.

Abra **QSDataModel.xcdatamodeld**. Hay cuatro tablas definidas: tres usadas por el SDK y una para las tareas pendientes en sí:
  * MS_TableOperations: realiza el seguimiento de los elementos que deben sincronizarse con el servidor.
  * MS_TableOperationErrors: realiza el seguimiento de los errores que se producen durante la sincronización sin conexión.
  * MS_TableConfig: realiza el seguimiento de la hora de la última actualización de la última operación de sincronización para todas las operaciones de extracción.
  * TodoItem: almacena las tareas pendientes. Las columnas del sistema **createdAt**, **updatedAt** y **version** son propiedades del sistema opcionales.

> [!NOTE]
> El SDK de Mobile Apps tiene reservados los nombres de columna que empiezan por "**``**". Utilice este prefijo exclusivamente para las columnas del sistema. De lo contrario, se modifican los nombres de las columnas cuando se usa el back-end remoto.
>
>

Cuando use la característica de sincronización sin conexión, defina las tres tablas del sistema y la tabla de datos.

### <a name="system-tables"></a>Tablas del sistema

**MS_TableOperations**  

![Atributos de la tabla MS_TableOperations][defining-core-data-tableoperations-entity]

| Atributo | type |
| --- | --- |
| id | Integer 64 |
| itemId | string |
| propiedades | Binary Data |
| table | string |
| tableKind | Integer 16 |


**MS_TableOperationErrors**

 ![Atributos de la tabla MS_TableOperationErrors][defining-core-data-tableoperationerrors-entity]

| Atributo | type |
| --- | --- |
| id |string |
| operationId |Integer 64 |
| propiedades |Binary Data |
| tableKind |Integer 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Atributo | type |
| --- | --- |
| id |string |
| key |string |
| keyType |Integer 64 |
| table |string |
| value |string |

### <a name="data-table"></a>Tabla de datos

**TodoItem**

| Atributo | type | Nota: |
| --- | --- | --- |
| id | Cadena, marcado obligatorio |Clave principal en almacén remoto |
| complete | boolean | Campo de tarea pendiente |
| text |string |Campo de tarea pendiente |
| createdAt | Date | (opcional) Se asigna a la propiedad del sistema **createdAt** |
| updatedAt | Date | (opcional) Se asigna a la propiedad del sistema **updatedAt** |
| version | string | (opcional) Se usa para detectar conflictos; se asigna a version |

## <a name="setup-sync"></a>Cambio del comportamiento de sincronización de la aplicación
En esta sección, modifique la aplicación para que no se sincronice al iniciarse la aplicación ni cuando se inserten y actualicen elementos. Solo se sincroniza cuando se realiza el gesto de actualización.

**Objective-C**:

1. En **QSTodoListViewController.m**, cambie el método **viewDidLoad** para quitar la llamada a `[self refresh]` al final del método. Ahora los datos no se han sincronizado con el servidor al iniciarse la aplicación. En su lugar, se sincronizan con el contenido del almacén local.
2. En **QSTodoService.m**, modifique la definición de `addItem` para que no se sincronice tras la inserción del elemento. Quite el bloque `self syncData` y sustitúyalo por lo siguiente:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Modifique la definición de `completeItem` como se mencionó antes. Quite el bloque para `self syncData` y sustitúyalo por lo siguiente:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**SWIFT**:

En `viewDidLoad`, en **ToDoTableViewController.swift**, convierta en comentario las dos líneas que se muestran aquí para detener la sincronización al iniciarse la aplicación. En el momento de redactar este artículo, la aplicación Swift Todo no actualiza el servicio cuando alguien agrega o completa un elemento. Solo lo actualiza en el inicio de aplicación.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Prueba de la aplicación
En esta sección, se conecta a una dirección URL no válida para simular un escenario sin conexión. Cuando agrega elementos de datos, se guardan en el almacén local Core Data, pero no se sincronizan con el back-end de la aplicación móvil.

1. Cambie la dirección URL de la aplicación móvil en **QSTodoService.m** por una no válida y vuelva a ejecutar la aplicación:

   **Objective-C**. En QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Swift**. En ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Agregue algunas tareas pendientes. Salga del simulador (o fuerce el cierre de la aplicación) y reinícielo. Compruebe que los cambios se conserven.

3. Vea el contenido de la tabla **TodoItem** remota:
   * En un back-end de Node.js, vaya a [Azure Portal](https://portal.azure.com/) y, en el back-end de su aplicación móvil, haga clic en **Tablas fáciles** > **TodoItem**.  
   * En un back-end de .NET, use una herramienta SQL, como SQL Server Management Studio, o un cliente REST, como Fiddler o Postman.  

4. Compruebe que los nuevos elementos *no* se hayan sincronizado con el servidor.

5. Vuelva a cambiar la dirección URL por la correcta en **QSTodoService.m** y ejecute de nuevo la aplicación.

6. Realice el gesto de actualización desplegando la lista de elementos.  
Se muestra un control giratorio de progreso.

7. Vea los datos de **TodoItem** de nuevo. Ahora deben aparecer las tareas nuevas y modificadas.

## <a name="summary"></a>Resumen
Para admitir la característica de sincronización sin conexión, se usa la interfaz `MSSyncTable` y se inicializa `MSClient.syncContext` con un almacén local. En este caso, el almacén local era una base de datos Core Data.

Cuando usa un almacén local Core Data, debe definir varias tablas con las [propiedades del sistema correctas](#review-core-data).

Las operaciones normales de creación, lectura, actualización y eliminación (CRUD) para aplicaciones móviles funcionan como si la aplicación siguiera conectada, pero todas ellas se producen en el almacén local.

Cuando el almacén local se sincroniza con el servidor, se usa el método **MSSyncTable.pullWithQuery**.

## <a name="additional-resources"></a>Recursos adicionales
* [Sincronización de datos sin conexión en Mobile Apps]
* [Cloud Cover: sincronización sin conexión en Azure Mobile Services]\(El vídeo trata sobre Mobile Services, pero la sincronización sin conexión de Mobile Apps funciona de forma similar.\)

<!-- URLs. -->


[Creación de una aplicación iOS]: app-service-mobile-ios-get-started.md
[Sincronización de datos sin conexión en Mobile Apps]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover: sincronización sin conexión en Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/en-us/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
