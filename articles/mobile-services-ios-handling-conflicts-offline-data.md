<properties urlDisplayName="Handle Conflicts with Offline Data" pageTitle="Gesti&oacute;n de conflictos con datos sin conexi&oacute;n en Servicios m&oacute;viles (iOS) | Centro de desarrollo m&oacute;vil" metaKeywords="" description="Aprenda a usar Servicios m&oacute;viles de Azure para gestionar los conflictos que se producen al sincronizar datos sin conexi&oacute;n en su aplicaci&oacute;n iOS." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Control de conflictos con datos sin conexi&oacute;n en Servicios m&oacute;viles" authors="krisragh" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="dotnet" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Control de conflictos con la sincronización de datos sin conexión en Servicios móviles

<div class="dev-center-tutorial-selector sublanding">
<a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data" title="C# para Tienda Windows">C# para Tienda Windows</a>
<a href="/es-es/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/es-es/documentation/articles/mobile-services-ios-handling-conflicts-offline-data" title="iOS" class="current">iOS</a>
</div>

En este tema se explica cómo sincronizar los datos y controlar conflictos cuando se usan las capacidades sin conexión de Servicios móviles de Azure. Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos sin conexión][Introducción a los datos sin conexión]. Antes de comenzar este tutorial, primero debe completar [Introducción a los datos sin conexión][Introducción a los datos sin conexión].

> [WACOM.NOTE] Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][Evaluación gratuita de Azure].

## Realización del tutorial Introducción a los datos sin conexión

Siga las instrucciones que se describen en [Introducción a los datos sin conexión][Introducción a los datos sin conexión] y realice ese proyecto. Usaremos el proyecto acabado de ese tutorial como punto de partida para este otro.

## Actualización del proyecto de aplicación para permitir la edición

Vamos a actualizar el proyecto terminado en [Introducción a los datos sin conexión][Introducción a los datos sin conexión] para permitir la edición de elementos. Actualmente, si ejecuta esta misma aplicación en dos teléfonos, cambia el mismo elemento en ambos teléfonos localmente e inserta los cambios de vuelta en el servidor, se provoca un conflicto.

Las características de sincronización sin conexión del SDK le permiten gestionar tales conflictos mediante código y decidir dinámicamente lo que hacer con los elementos en conflicto. Cambiar el proyecto de inicio rápido nos permite experimentar con esta característica.

### Actualización del controlador de vista de lista Todo

1.  Vamos a actualizar el guión gráfico del iPhone. Si trabaja con un iPad, siga también los mismos pasos para el guión gráfico del iPad.

2.  Seleccione **MainStoryboard\_iPhone.storyboard** en Project Navigator en Xcode y luego seleccione el **controlador de vista de lista Todo**. En el menú de la parte superior, haga clic en **Editor -\> Incrustar en -\> Controlador de navegación**.

    ![][0]

3.  A continuación, en el **controlador de vista de lista Todo**, seleccione la celda de la vista de tabla y establezca su modo Accesorio en **Indicador de divulgación**. El indicador de divulgación indica a los usuarios que si tocan en el controlador de vista de tabla asociada, se mostrará una nueva vista. El indicador de divulgación no produce ningún evento.

    ![][1]

4.  En **TodoListViewController.m**, elimine las siguientes operaciones y su contenido todo junto. No los necesitamos:

        -(NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath

        -(UITableViewCellEditingStyle)tableView:(UITableView *)tableView editingStyleForRowAtIndexPath:(NSIndexPath *)indexPath

        -(void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle
         forRowAtIndexPath:(NSIndexPath *)indexPath

### Adición del controlador de vista de elementos Todo

1.  Agregue una nueva clase Objective-C llamada **QSTodoItemViewController**, obtenida de **UIViewController**, a su proyecto:

    ![][2]

    ![][3]

2.  En **QSTodoItemViewController.h**, agregue una propiedad para contener el elemento que se va a modificar:

        @property (nonatomic, weak) NSMutableDictionary *item;

3.  En **QSTodoItemViewController.m**, agregue dos propiedades privadas para los dos campos del elemento Todo que vamos a editar: el estado de finalización y el texto del elemento Todo propiamente dicho:

        @interface QSTodoItemViewController ()

        @property (nonatomic, strong) IBOutlet UITextField *itemText;
        @property (nonatomic, strong) IBOutlet UISegmentedControl *itemComplete;

        @end

4.  En **QSTodoItemViewController.m**, actualice la implementación de código auxiliar de **viewDidLoad** al siguiente código:

        - (void)viewDidLoad
        {
            [super viewDidLoad];

            UINavigationItem *nav = [self navigationItem];
            [nav setTitle:@"Todo Item"];

            NSDictionary *theItem = [self item];
            [self.itemText setText:[theItem objectForKey:@"text"]];

            BOOL isComplete = [[theItem objectForKey:@"complete"] boolValue];
            [self.itemComplete setSelectedSegmentIndex:(isComplete ? 0 : 1)];

            [self.itemComplete addTarget:self
                                  action:@selector(completedValueChanged:)
                        forControlEvents:UIControlEventValueChanged];
        }

5.  En **QSTodoItemViewController.m**, agregue cuatro métodos adicionales para gestionar varios eventos:

        - (BOOL)textFieldShouldEndEditing:(UITextField *)textField {
            [textField resignFirstResponder];
            return YES;
        }

        - (BOOL)textFieldShouldReturn:(UITextField *)textField {
            [textField resignFirstResponder];
            return YES;
        }


        - (void)completedValueChanged:(id)sender {
            [[self view] endEditing:YES];
        }

        - (void)viewWillDisappear:(BOOL)animated {
            [self.item setValue:[self.itemText text] forKey:@"text"];
            [self.item setValue:[NSNumber numberWithBool:self.itemComplete.selectedSegmentIndex == 0] forKey:@"complete"];
        }

### Adición del controlador de vista de elementos Todo y de un objeto segue al guión gráfico

1.  Vuelva al archivo **MainStoryboard\_iPhone.storyboard** mediante Project Navigator.

2.  Agregue un nuevo controlador de vista para el elemento Todo al guión gráfico, a la derecha del **controlador de vista de lista Todo** existente. Establezca la clase personalizada de este nuevo controlador de vista en **QSTodoItemViewController**. Para obtener más información, consulte [Adición de una escena a un guión gráfico][Adición de una escena a un guión gráfico].

3.  Agregue un objeto segue de inserción desde el **controlador de vista de lista Todo** hasta el **controlador de vista de elementos Todo** y denomine a la transición **detailSegue**. Para obtener más información, consulte [Adición de un objeto segue entre escenas en un guión gráfico][Adición de un objeto segue entre escenas en un guión gráfico]. No cree este objeto segue desde cualquier celda o botón del controlador de vista de origen. En su lugar, presione CTRL y arrastre desde el icono del controlador de vista que se muestra debajo del **controlador de vista de lista Todo** en la interfaz del guión gráfico hasta el **controlador de vista de elementos Todo**. Si por accidente aplica el objeto segue desde una celda, desencadenará la transición dos veces cuando ejecute la aplicación, lo que dará como resultado este error:

        Nested push animation can result in corrupted navigation bar

4.  Agregue un campo de texto para el texto del elemento y un control segmentado para el estado de finalización al nuevo **controlador de vista de elementos Todo**, también con etiquetas. En el control segmentado, establezca el título del **segmento 0** en **Sí** y el de **segmento 1** en **No**. Conecte estos nuevos campos a salidas en el código. Para obtener más información, consulte [Crear una interfaz de usuario][Crear una interfaz de usuario] and [Controles segmentados][Controles segmentados].

    ![][4]

5.  Conecte estos nuevos campos a las salidas correspondientes que ya ha agregado a **QSTodoItemViewController.m**. Conecte el campo de texto del elemento a la salida **itemText** y el control segmentado de estado de finalización a la salida **itemComplete**. Para obtener más información, consulte [Creación de una conexión de salida][Creación de una conexión de salida].

6.  Establezca el delegado del campo de texto en el controlador de vista. Esta acción reasigna el campo de texto cuando finalmente edita un elemento y presiona RETORNO. Presione la tecla CTRL y arrastre desde el campo de texto al icono del controlador de vista que se muestra debajo del **controlador de vista de elementos Todo** en la interfaz del guión gráfico, y seleccione la salida del delegado; esta acción indica al guión gráfico que el delegado de este campo de texto es este controlador de vista.

7.  Compruebe que la aplicación funciona con todos los cambios realizados hasta el momento. Ejecute ahora la aplicación en el simulador. Agregue elementos a la lista Todo y haga clic en ellos. Verá el controlador de vista de elementos (actualmente vacío).

    ![][5]

    ![][6]

### Adición de detalles de elementos al controlador de vista de elementos Todo

1.  Haremos referencia a **QSTodoItemViewController** desde **QSTodoListViewController.m**. De modo que, en **QSTodoListViewController.m**, vamos a agregar una línea para importar **QSTodoItemViewController.h**.

        #import "QSTodoItemViewController.h"

2.  Agregue dos nuevas propiedades a la interfaz **QSTodoListViewController** en **QSTodoListViewController.m** para almacenar el elemento que se va a editar:

        @property (nonatomic)           NSInteger       editedItemIndex;
        @property (strong, nonatomic)   NSMutableDictionary *editedItem;

3.  Implemente **tableView:didSelectRowAtIndexPath:** en **QSTodoListViewController.m** para guardar el elemento que se va a editar y luego llame al objeto segue para mostrar la vista detallada.

          - (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
              self.editedItemIndex = [indexPath row];
              self.editedItem = [[self.todoService.items objectAtIndex:[indexPath row]] mutableCopy];

              [self performSegueWithIdentifier:@"detailSegue" sender:self];
          }

4.  Implemente **prepareForSegue:sender:** en **QSTodoListViewController.m** para pasar el elemento al **controlador de vista de elementos Todo**.

        - (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
            if ([[segue identifier] isEqualToString:@"detailSegue"]) {
                QSTodoItemViewController *ivc = (QSTodoItemViewController *)[segue destinationViewController];
                ivc.item = self.editedItem;
            }
        }

5.  Compruebe que la aplicación funciona con todos los cambios realizados hasta el momento. Ejecute ahora la aplicación en el simulador. Agregue elementos a la lista Todo y haga clic en ellos. Verá que el controlador de vista de elementos ya no está vacío, sino que muestra los detalles de los elementos Todo.

    ![][7]

### Adición de compatibilidad para guardar ediciones

1.  Cuando hace clic en el botón "Atrás" en la vista de navegación, las ediciones se pierden. Hemos enviado los datos a la vista detallada, pero los datos no se reenvían a la vista maestra. Dado que ya pasamos un puntero a una copia del elemento, podemos usar ese puntero para recuperar la lista de actualizaciones realizadas en el elemento y actualizarlo en el servidor. Para comenzar, primero actualice la clase contenedora de servidor de **QSTodoService** en **QSTodoService.m** quitando la operación **completeItem** y agregando una nueva operación **updateItem**. El motivo es que **completeItem** solo marca los elementos como completados, mientras que **updateItem** los actualiza.

        - (void)updateItem:(NSDictionary *)item atIndex:(NSInteger)index completion:(QSCompletionWithIndexBlock)completion {
            // Cast the public items property to the mutable type (it was created as mutable)
            NSMutableArray *mutableItems = (NSMutableArray *) items;

            // Replace the original in the items array
            [mutableItems replaceObjectAtIndex:index withObject:item];

            // Update the item in the TodoItem table and remove from the items array when we mark an item as complete
            [self.syncTable update:item completion:^(NSError *error) {
                [self logErrorIfNotNil:error];

                NSInteger index = -1;
                if (!error) {
                    BOOL isComplete = [[item objectForKey:@"complete"] boolValue];
                    NSString *remoteId = [item objectForKey:@"id"];
                    index = [items indexOfObjectPassingTest:^BOOL(id obj, NSUInteger idx, BOOL *stop) {
                        return [remoteId isEqualToString:[obj objectForKey:@"id"]];
                    }];

                    if (index != NSNotFound && isComplete)
                    {
                        [mutableItems removeObjectAtIndex:index];
                    }
                }

                // Let the caller know that we have finished
                dispatch_async(dispatch_get_main_queue(), ^{
                    completion(index);
                });
            }];
        }

2.  Elimine la declaración de **completeItem** de **QSTodoService.h** y agregue esta declaración a **updateItem**:

        - (void)updateItem:(NSDictionary *)item atIndex:(NSInteger)index completion:(QSCompletionWithIndexBlock)completion;

3.  En **QSTodoListViewController.m**, agregue la operación **viewWillAppear** para llamar al método de actualización cada vez que se muestre la vista maestra después de volver del controlador de vista detallada.

        - (void)viewWillAppear:(BOOL)animated {
            if (self.editedItem && self.editedItemIndex >= 0) {
                // Returning from the details view controller
                NSDictionary *item = [self.todoService.items objectAtIndex:self.editedItemIndex];

                BOOL changed = ![item isEqualToDictionary:self.editedItem];
                if (changed) {
                    [self.tableView setUserInteractionEnabled:NO];

                    // Change the appearance to look greyed out until we remove the item
                    NSIndexPath *indexPath = [NSIndexPath indexPathForRow:self.editedItemIndex inSection:0];

                    UITableViewCell *cell = [self.tableView cellForRowAtIndexPath:indexPath];
                    cell.textLabel.textColor = [UIColor grayColor];

                    // Ask the todoService to update the item, and remove the row if it's been completed
                    [self.todoService updateItem:self.editedItem atIndex:self.editedItemIndex completion:^(NSUInteger index) {
                        if ([[self.editedItem objectForKey:@"complete"] boolValue]) {
                            // Remove the row from the UITableView
                            [self.tableView deleteRowsAtIndexPaths:@[ indexPath ]
                                                  withRowAnimation:UITableViewRowAnimationTop];
                        } else {
                            [self.tableView reloadRowsAtIndexPaths:[NSArray arrayWithObject:indexPath]
                                                  withRowAnimation:UITableViewRowAnimationAutomatic];
                        }

                        [self.tableView setUserInteractionEnabled:YES];

                        self.editedItem = nil;
                        self.editedItemIndex = -1;
                    }];
                } else {
                    self.editedItem = nil;
                    self.editedItemIndex = -1;
                }
            }
        }

4.  Ahora vamos a probar la aplicación. Compruebe que la aplicación funciona con todos los cambios realizados hasta el momento. Ejecute ahora la aplicación en el simulador. Agregue elementos a la lista Todo y haga clic en ellos. Intente editar un elemento y vuelva. Compruebe que la descripción del elemento está actualizada en la vista maestra de la aplicación. Actualice la aplicación con el gesto de arrastrar abajo y compruebe que la edición se refleja en la nube.

### Problema de gestión de conflictos

1.  Vamos a examinar lo que pasa cuando dos clientes diferentes intentan modificar los mismos datos al mismo tiempo. En el ejemplo que se indica a continuación, hay un elemento "Hello world 3". Vamos a cambiarlo para que diga "Hello world 13" en un dispositivo y "Hello world 23" en otro.

    ![][8]

2.  Inicie la aplicación en dos lugares: en dos dispositivos iOS o en el simulador y en un dispositivo iOS. Si no tiene un dispositivo físico en el que probar, inicie una instancia en el simulador y, usando un cliente REST, envíe una solicitud PATCH al servicio móvil. La URL de la solicitud PATCH refleja el nombre del servicio móvil, el nombre de la tabla de elementos Todo y el Id. de la tabla de elementos Todo que va a editar, mientras que el encabezado de x-zumo-application es la clave de la aplicación:

        PATCH https://todolist.azure-mobile.net/tables/todoitem/D265929E-B17B-42D1-8FAB-D0ADF26486FA?__systemproperties=__version
        Content-Type: application/json
        x-zumo-application: shYOoDFdKhmzLEbnMQqPYrCLhwGOVA10

        {
            "id": "D265929E-B17B-42D1-8FAB-D0ADF26486FA",
            "text": "Hello world 23"
        }

3.  Ahora, actualice los elementos en las dos instancias de la aplicación. Verá un error impreso en el registro de salida en Xcode:

        TodoList[1575:4a2f] ERROR Error Domain=com.Microsoft.WindowsAzureMobileServices.ErrorDomain Code=-1170 "Not all operations completed successfully" UserInfo=0x8dd6310 {com.Microsoft.WindowsAzureMobileServices.ErrorPushResultKey=(
            "The server's version did not match the passed version"
        ), NSLocalizedDescription=Not all operations completed successfully}

Esto se debe a que en el bloque de finalización, en la llamada a **pullWithQuery:completion:**, el parámetro de error será distinto de nil, lo que provocará que se imprima el error en la salida a través de **NSLog**.

### Actualización de QSTodoService para permitir la gestión de conflictos

1.  Vamos a dejar que el usuario decida cómo gestionar el conflicto tratándolo en el cliente. Para ello, vamos a implementar el protocolo **MSSyncContextDelegate**. En **QSTodoService.h** y **QSTodoService.m**, cambie la declaración del método de fábrica \*\*(QSTodoService \*)defaultService;\*\* por la siguiente instrucción, para recibir el delegado del contexto de sincronización como parámetro:

        + (QSTodoService *)defaultServiceWithDelegate:(id)delegate;

2.  En **QSTodoService.m**, cambie la línea **init** como se muestra a continuación, para recibir de nuevo el delegado del contexto de sincronización como parámetro:

        -(QSTodoService *)initWithDelegate:(id)syncDelegate

3.  En **QSTodoService.m**, cambie la llamada **init** en **defaultServiceWithDelegate** por **initWithDelegate**:

        service = [[QSTodoService alloc] initWithDelegate:delegate];

4.  De vuelta en **QSTodoService.m**, cambie la inicialización de **self.client.syncContext** para pasar **syncDelegate** en lugar de **nil** en el delegado:

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:syncDelegate dataSource:store callback:nil];

### Adición del ayudante de vistas de alertas de UI para permitir la gestión de conflictos

1.  Si hay un conflicto, vamos a permitir que el usuario elija qué versión desea mantener:

-   mantener la versión de cliente (que anula la versión en el servidor),
-   mantener la versión de servidor (que actualiza la tabla local del cliente) o
-   no mantener ninguna versión (cancela la inserción y deja la operación pendiente).

Dado que también puede tener lugar otra actualización mientras visualizamos el mensaje, seguiremos mostrando opciones hasta que el servidor deje de devolver una respuesta de error. En nuestro código, vamos a usar una clase auxiliar que muestra una vista de alertas y toma un delegado que se invoca cuando se muestra dicha vista. Vamos a definir primero la clase auxiliar **QSUIAlertViewWithBlock**.

1.  Agregue esta nueva clase, **QSUIAlertViewWithBlock**, mediante Xcode, y sobrescriba **QSUIAlertViewWithBlock.h** con el siguiente contenido:

        #import <Foundation/Foundation.h>

        typedef void (^QSUIAlertViewBlock) (NSInteger index);

        @interface QSUIAlertViewWithBlock : NSObject <UIAlertViewDelegate>

        - (id) initWithCallback:(QSUIAlertViewBlock)callback;
        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles;

        @end

2.  A continuación, sobrescriba **QSUIAlertViewWithBlock.m** con el siguiente archivo:

        #import "QSUIAlertViewWithBlock.h"
        #import <objc/runtime.h>

        @interface QSUIAlertViewWithBlock()

        @property (nonatomic, copy) QSUIAlertViewBlock callback;

        @end

        @implementation QSUIAlertViewWithBlock

        static const char *key;

        @synthesize callback = _callback;

        - (id) initWithCallback:(QSUIAlertViewBlock)callback
        {
            self = [super init];
            if (self) {
                _callback = [callback copy];
            }
            return self;
        }

        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title
                                                            message:message
                                                           delegate:self
                                                  cancelButtonTitle:cancelButtonTitle
                                                  otherButtonTitles:nil];

            if (otherButtonTitles) {
                for (NSString *buttonTitle in otherButtonTitles) {
                    [alert addButtonWithTitle:buttonTitle];
                }
            }

            [alert show];

            objc_setAssociatedObject(alert, &key, self, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
        }

        - (void) alertView:(UIAlertView *)alertView didDismissWithButtonIndex:(NSInteger)buttonIndex
        {
            if (self.callback) {
                self.callback(buttonIndex);
            }
        }

        @end

### Adición de un controlador de conflictos al controlador de vista de lista Todo

1.  En **QSTodoListViewController.m**, sustituya la llamada a **defaultService** en **viewDidLoad** por una llamada a **defaultServiceWithDelegate**, como se muestra a continuación:

        self.todoService = [QSTodoService defaultServiceWithDelegate:self];

2.  En **QSTodoListViewController.h**, agregue **\<MSSyncContextDelegate\>** a la declaración de la interfaz, así que vamos a implementar el protocolo **MSSyncContextDelegate**.

        @interface QSTodoListViewController : UITableViewController<MSSyncContextDelegate>

3.  Agregue la siguiente instrucción de importación en la parte superior de **QSTodoListViewController.m**:

        #import "QSUIAlertViewWithBlock.h"

4.  Finalmente, vamos a agregar las dos operaciones siguientes a **QSTodoListViewController.m** para usar esta clase auxiliar y pedir al usuario que concilie el conflicto de una de tres maneras.

        - (void)tableOperation:(MSTableOperation *)operation onComplete:(MSSyncItemBlock)completion
        {
            [self doOperation:operation complete:completion];
        }

        -(void)doOperation:(MSTableOperation *)operation complete:(MSSyncItemBlock)completion
        {
            [operation executeWithCompletion:^(NSDictionary *item, NSError *error) {

                NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];

                if (error.code == MSErrorPreconditionFailed) {
                    QSUIAlertViewWithBlock *alert = [[QSUIAlertViewWithBlock alloc] initWithCallback:^(NSInteger buttonIndex) {
                        if (buttonIndex == 1) { // Client
                            NSMutableDictionary *adjustedItem = [operation.item mutableCopy];

                            [adjustedItem setValue:[serverItem objectForKey:MSSystemColumnVersion] forKey:MSSystemColumnVersion];
                            operation.item = adjustedItem;

                            [self doOperation:operation complete:completion];
                            return;

                        } else if (buttonIndex == 2) { // Server
                            NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
                            completion(serverItem, nil);
                        } else { // Cancel
                            [operation cancelPush];
                            completion(nil, error);
                        }
                    }];

                    NSString *message = [NSString stringWithFormat:@"Client value: %@\nServer value: %@", operation.item[@"text"], serverItem[@"text"]];

                    [alert showAlertWithTitle:@"Server Conflict"
                                      message:message
                            cancelButtonTitle:@"Cancel"
                            otherButtonTitles:[NSArray arrayWithObjects:@"Use Client", @"Use Server", nil]];
                } else {
                    completion(item, error);
                }
            }];
        }

### Prueba de la aplicación

Vamos a probar la aplicación con conflictos. Edite el mismo elemento en dos instancias diferentes de la aplicación que se ejecuta al mismo tiempo. Realice ahora el gesto de actualizar en las instancias de la aplicación arrastrando desde la parte superior. Verá un mensaje para conciliar el cambio ahora.

### Resumen

Para configurar el proyecto completado en [Introducción a los datos sin conexión][Introducción a los datos sin conexión] con el fin de detectar conflictos, primero agregó la posibilidad de editar y actualizar los elementos Todo.

Para ello, agregó un nuevo controlador de vista detallada de elementos, conectó el controlador de vista principal y el controlador de vista detallada y finalmente agregó la posibilidad de guardar las ediciones e insertarlas en la nube.

Luego, descubrió lo que pasa cuando hay un conflicto. Agregó compatibilidad con un controlador de conflictos mediante la implementación del protocolo **MSSyncContextDelegate**. También habilitó la compatibilidad con el delegado del contexto de sincronización a través de la clase de interfaz de servidor de **QSTodoService**, **QSTodoListViewController**, y las clases complementarias.

Por el camino, agregó una clase auxiliar **QSUIAlertViewWithBlock** para mostrar una alerta a los usuarios y, para finalizar, agregó código a **QSTodoListViewController** para pedir al usuario que concilie el conflicto de una de tres maneras.



  [Introducción a los datos sin conexión]: /es-es/documentation/articles/mobile-services-ios-get-started-offline-data/
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=AE564AB28
  [0]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-1.png
  [1]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png
  [2]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-1.png
  [3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-2.png
  [Adición de una escena a un guión gráfico]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
  [Adición de un objeto segue entre escenas en un guión gráfico]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
  [Crear una interfaz de usuario]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
  [Controles segmentados]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html
  [4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png
  [Creación de una conexión de salida]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
  [5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png
  [6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png
  [7]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png
  [8]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png
