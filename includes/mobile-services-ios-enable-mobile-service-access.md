
Ahora que el servicio móvil está listo, puede actualizar la aplicación a fin de almacenar elementos en Servicios móviles en lugar de en la colección local.

1. Si todavía no ha instalado el [SDK de iOS para Servicios móviles](https://go.microsoft.com/fwLink/p/?LinkID=266533), hágalo ahora. Una vez instalado, copie el directorio WindowsAzureMobileServices.framework y sobrescriba el directorio  WindowsAzureMobileServices.framework incluido en el proyecto descargado. De este modo, utiliza el último SDK de Servicios móviles de Azure.

2. En el archivo TodoService.h, localice la siguiente línea de código comentada:

        // TODO - create an MSClient proeprty

   	Después de este comentario, agregue la siguiente línea de código:

        @property (nonatomic, strong)   MSClient *client;

   	De esta manera se crea una propiedad que representa el MSClient que se conecta al servicio.

3. En el archivo TodoService.m, localice la siguiente línea de código comentada:

        // TODO - create an MSTable property for your items

   	Después de este comentario, agregue la siguiente línea de código dentro de la declaración @interfaz:

        @property (nonatomic, strong)   MSTable *table;

   	Así se crea una representación de propiedad para su tabla de servicios móviles.

4. En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

5. Haga clic en la pestaña **Panel** y anote la **dirección URL del sitio**; a continuación, haga clic en **Administrar claves** y anote la **clave de la aplicación**.

   	![](./media/mobile-services-ios-enable-mobile-service-access/mobile-dashboard-tab.png)

  	Necesitará estos valores para obtener acceso al servicio móvil desde su código de aplicación.

6. Nuevamente en Xcode, abra TodoService.m y localice la siguiente línea de código comentada:

        // Initialize the Mobile Service client with your URL and key.

    Después de este comentario, agregue la siguiente línea de código:

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];

    Esto crea una instancia del cliente de Servicios móviles.

7. 	Reemplace los valores **APPURL** y **APPKEY** en este código con la URL y la clave de aplicación del servicio móvil que adquirió en el paso 6.

8. Localice la siguiente línea de código comentada:

        // Create an MSTable instance to allow us to work with the TodoItem table.

    Después de este comentario, agregue la siguiente línea de código:

        self.table = [self.client tableWithName:@"TodoItem"];

    De esta manera se crea la instancia de la tabla TodoItem.

9. Localice la siguiente línea de código comentada:

 	    // Create a predicate that finds items where complete is false

    Después de este comentario, agregue la siguiente línea de código:

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    Así se crea una consulta para devolver todas las tareas que no se hayan completado todavía.

10. Localice la siguiente línea de código comentada:

        // Query the TodoItem table and update the items property with the results from the service

   	Reemplace ese comentario y la invocación del bloque de **finalización** posterior por el siguiente código:

        [self.table readWhere:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
		{
		   self.items = [results mutableCopy];
           completion();
        }];

11. Localice el método **addItem** y reemplace el cuerpo del método por el siguiente código:

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }];

    Este código envía una solicitud de inserción al servicio móvil.

12. Busque el método **completeItem** y busque la siguiente línea de código comentada:

        // Update the item in the TodoItem table and remove from the items array on completion

    Reemplace el cuerpo del método, desde ese punto hasta el final del método, con el código siguiente:

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
	    }];

   	Este código quita los elementos TodoItems una vez que se han marcado como completados.

13. En TodoListController.m, busque el método **onAdd** y sobrescríbalo con el siguiente código:

      - (IBAction)onAdd:(id)sender
      {
          if (itemText.text.length  == 0)
          {
              return;
          }

          NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @NO };
          UITableView *view = self.tableView;
          [self.todoService addItem:item completion:^(NSUInteger index)
          {
              NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
              [view insertRowsAtIndexPaths:@[ indexPath ]
                          withRowAnimation:UITableViewRowAnimationTop];
          }];

          itemText.text = @"";
      }


Ahora que se ha actualizado la aplicación para utilizar Servicios móviles para almacenamiento back-end, es momento de probar la aplicación con Servicios móviles.
