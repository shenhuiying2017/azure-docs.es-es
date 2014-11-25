Ahora que el servicio móvil está listo, puede actualizar la aplicación a fin de almacenar elementos en Servicios móviles en lugar de en la colección local.

1. Si todavía no ha instalado el [SDK de iOS para Servicios móviles][SDK de iOS para Servicios móviles], hágalo ahora.

2. En el Project Navigator en Xcode, abra los archivos TodoService.m y TodoService.h que se encuentran en la carpeta Quickstart y agregue la siguiente instrucción de importación:

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>  

3. En el archivo ToDoService.h, localice la siguiente línea de código comentada:

        // Create an MSClient property comment in the #interface declaration for the TodoService. 

    Después de este comentario, agregue la siguiente línea de código:

        @property (nonatomic, strong)   MSClient *client;

    De esta manera se crea una propiedad que representa el MSClient que se conecta al servicio.

4. En el archivo TodoService.m, localice la siguiente línea de código comentada:

        // Create an MSTable property for your items. 

    Después de este comentario, agregue la siguiente línea de código dentro de la declaración @interfaz:

        @property (nonatomic, strong)   MSTable *table;

    Así se crea una representación de propiedad para su tabla de servicios móviles.

5. En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

6. Haga clic en la pestaña **Panel** y anote la **dirección URL del sitio**, a continuación haga clic en **Administrar claves** y anote la **clave de la aplicación**.

    ![][0]

    Necesitará estos valores para obtener acceso al servicio móvil desde su código de aplicación.

7. Nuevamente en Xcode, abra TodoService.m y localice la siguiente línea de código comentada:

        // Initialize the Mobile Service client with your URL and key.

    Después de este comentario, agregue la siguiente línea de código:

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" withApplicationKey:@"APPKEY"];

    Esto crea una instancia del cliente de Servicios móviles.

8. Reemplace los valores **APPURL** y **APPKEY** en este código con la URL y la clave de aplicación del servicio móvil que adquirió en el paso 6.

9. Localice la siguiente línea de código comentada:

        // Create an MSTable instance to allow us to work with the TodoItem table.

    Después de este comentario, agregue la siguiente línea de código:

        self.table = [self.client getTable:@"TodoItem"];

    De esta manera se crea la instancia de la tabla TodoItem.

10. Localice la siguiente línea de código comentada:

        // Create a predicate that finds items where complete is false comment in the refreshDataOnSuccess method. 

    Después de este comentario, agregue la siguiente línea de código:

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    Así se crea una consulta para devolver todas las tareas que no se hayan completado todavía.

11. Localice la siguiente línea de código comentada:

        // Query the TodoItem table and update the items property with the results from the service.

    Reemplace ese comentario y la invocación del bloque de **finalización** posterior por el siguiente código:

        // Query the TodoItem table and update the items property with the results from the service
        [self.table readWhere:predicate completion:^(NSArray *items, NSInteger totalCount, NSError *error)
        {
           self.items = [items mutableCopy];
           completion();
        }]; 

12. Busque el método **addItem** y agregue el código siguiente al cuerpo del método:

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }];

    Este código envía una solicitud de inserción al servicio móvil.

13. Busque el método **completeItem** y agregue el código siguiente al cuerpo del método:

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // TODO
            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
        }]; 

    Este código quita los elementos TodoItems una vez que se han marcado como completados.

Ahora que se ha actualizado la aplicación para utilizar Servicios móviles para almacenamiento back-end, es momento de probar la aplicación con Servicios móviles.

  [SDK de iOS para Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [0]: ./media/mobile-services-ios-enable-mobile-service-access/mobile-dashboard-tab.png
