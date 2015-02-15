
<h2><a name="update-app"></a>Actualización de la aplicación para llamar a la API personalizada</h2>

1. Cree un botón. De esa manera, podrá hacer clic en él para llamar a la API personalizada. Arrastre un botón **Round Rect Button** de la biblioteca **Object Library** que se encuentra en la parte inferior del panel **Utilities** y colóquelo debajo del campo de texto o junto a él. Haga doble clic para agregar el texto **"All"**. 

	De esta forma, se agrega un nuevo botón **"All"**.

2. Abra el archivo de código **QSTodoService.m**, busque el método  `refreshDataOnSuccess` y asegúrese de que contiene el siguiente código:

		- (void)refreshDataOnSuccess:(QSCompletionBlock)completion
		{		   
		    // Create a predicate that finds items where complete is false
		    NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
		    
		    // Query the TodoItem table and update the items property with the results from the service
		    [self.table readWithPredicate:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
		    {
		        [self logErrorIfNotNil:error];
		        
		        items = [results mutableCopy];
		        
		        // Let the caller know that we finished
		        completion();
		    }];		    		    		    
		}

	Esto filtra los elementos para que la consulta no devuelva los elementos completados.

3. Ahora es el momento de conectar este objeto al código fuente del controlador de vista. **Presione la tecla Control y haga clic** en el nuevo botón **"All"** y coloque el mouse justo antes de la línea `@end` en **QSTodoListViewController.h**. Conecte el objeto a una nuevo valor de **Action** con el nombre  `onCompleteAll` en **QSTodoListViewController**. Xcode insertará automáticamente la siguiente línea antes de la línea `@end`:

		   - (IBAction)onCompleteAll:(id)sender;

4. El objetivo de este método  `onCompleteAll` es administrar el evento Click para el nuevo botón. Invoca un nuevo método  `completeAll` que agregaremos a nuestra clase personalidad, que envía a cambio una solicitud POST a la nueva API personalizada. El resultado devuelto por la API personalizada se muestra en un cuadro de diálogo de mensaje, al igual que todos los errores. Edite **QSTodoListViewController.m** para agregar la siguiente implementación delante de la línea `@end`:

		   - (IBAction)onCompleteAll:(id)sender {
		    [self.todoService completeAll:^(id result, NSHTTPURLResponse* response, NSError* error)
		     {
		         if (error)
		         {
		             NSString* errorMessage = @"There was a problem! ";
		             errorMessage = [errorMessage stringByAppendingString:[error localizedDescription]];
		             UIAlertView* myAlert = [[UIAlertView alloc]
		                                     initWithTitle:@"Error!"
		                                     message:errorMessage
		                                     delegate:nil
		                                     cancelButtonTitle:@"Okay"
		                                     otherButtonTitles:nil];
		             [myAlert show];
		             [self refresh];
		         } else {
		             NSString* successMessage = [NSString stringWithFormat:@"%d items marked as complete", [[result objectForKey:@"count"] integerValue]];					 
		             UIAlertView* myAlert = [[UIAlertView alloc]
		                                     initWithTitle:@"Success!"
		                                     message:successMessage
		                                     delegate:nil
		                                     cancelButtonTitle:@"Okay"
		                                     otherButtonTitles:nil];
		             [myAlert show];
		             [self refresh];
		         }
		     }];
  		   }

5. Tenga en cuenta que el código anterior hace referencia a un nuevo método  `completeAll` que no se ha definido aún en **QSTodoService**. Edite **QSTodoService.h** y agregue la siguiente línea antes de la línea `@end`:

		- (void) completeAll:(MSAPIBlock)completion;

6. Agregue la implementación correspondiente de  `completeAll` en **QSTodoService.m** delante de la línea `@end`. iOS se parece a JavaScript porque no es compatible con la serialización JSON de tipos arbitrarios. En este aspecto, dispone también de una API bastante simple para invocar API personalizadas, que consiste en el método  `invokeAPI`. 

		- (void) completeAll:(MSAPIBlock)completion
		{
		    [self.client
		     invokeAPI:@"completeall"
		     body:nil
		     HTTPMethod:@"POST"
		     parameters:nil
		     headers:nil
		     completion:completion ];
		}

## <a name="test-app"></a>Prueba de la aplicación

1. En Xcode, seleccione un emulador para implementar en (iPhone o iPad), presione el botón **Ejecutar** (o la tecla **Cmd+R**) para volver a crear el proyecto e iniciar la aplicación. Esta acción ejecuta su cliente de Servicios móviles de Azure, creado con el SDK de iOS, que consulta elementos desde su servicio móvil.

2. Escriba texto en el campo de texto y, a continuación, haga clic en el botón **+**. Esto envía un elemento nuevo como inserción al servicio móvil.

3. Repita el paso anterior hasta que haya agregado varios elementos a la lista.

4. Haga clic en el botón **All**. Se mostrará un cuadro de alerta que indica el número de elementos marcados como completados y la consulta filtrada se vuelve a ejecutar, por lo que se borran todos los elementos de la lista.

  	![](./media/mobile-services-ios-call-custom-api/mobile-custom-api-ios-completed.png)

<!--HONumber=42-->
