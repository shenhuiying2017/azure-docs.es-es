<properties 
	pageTitle="Gestión de conflictos con datos sin conexión en Servicios móviles (iOS) | Centro de desarrollo móvil" 
	description="Aprenda a usar Servicios móviles de Azure para gestionar los conflictos que se producen al sincronizar datos sin conexión en su aplicación iOS." 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="01/26/2015" 
	ms.author="krisragh,donnam"/>


# Control de conflictos con la sincronización de datos sin conexión en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-offline-conflicts](../includes/mobile-services-selector-offline-conflicts.md)]

En este tema se explica cómo sincronizar los datos y controlar conflictos cuando se usan las capacidades sin conexión de Servicios móviles de Azure. Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos sin conexión]. Antes de comenzar este tutorial, primero debe completar [Introducción a los datos sin conexión].

>[AZURE.NOTE] Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Evaluación gratuita de Azure.</a>

Este tutorial le guiará a través de estos pasos básicos:

1. [Actualización del proyecto de aplicación para permitir la edición]
2. [Actualización del controlador de vista de lista Todo]
3. [Incorporación del controlador de vista de elementos Todo]
4. [Incorporación del controlador de vista de elementos Todo y de un objeto Segue al guion gráfico]
5. [Incorporación de detalles de elementos al controlador de vista de elementos Todo]
6. [Incorporación de compatibilidad para guardar ediciones]
7. [Problema de gestión de conflictos]
8. [Actualización de QSTodoService para permitir la gestión de conflictos]
9. [Incorporación del ayudante de vistas de alertas de IU para permitir la gestión de conflictos]
10. [Incorporación de un controlador de conflictos al controlador de vista de lista Todo]
11. [Prueba de la aplicación]

## Realización del tutorial Introducción a los datos sin conexión

Siga las instrucciones que se describen en [Introducción a los datos sin conexión] y realice ese proyecto. Usaremos el proyecto acabado de ese tutorial como punto de partida para este otro.

## <a name="update-app"></a>Actualización del proyecto de aplicación para permitir la edición

Vamos a actualizar el proyecto terminado en [Introducción a los datos sin conexión] para permitir la edición de elementos. Actualmente, si ejecuta esta misma aplicación en dos teléfonos, cambia el mismo elemento en ambos teléfonos localmente e inserta los cambios de vuelta en el servidor, se provoca un conflicto.

Las características de sincronización sin conexión del SDK le permiten gestionar tales conflictos mediante código y decidir dinámicamente lo que hacer con los elementos en conflicto. Cambiar el proyecto de inicio rápido nos permite experimentar con esta característica.

### <a name="update-list-view"></a>Actualización del controlador de vista de lista Todo

1. Seleccione **MainStoryboard_iPhone.storyboard** en Project Navigator en Xcode y luego seleccione el **Controlador de vista de lista de tareas**. Seleccione la celda de la vista de tabla y establezca su modo Accesorio en **Disclosure indicator** (Indicador de divulgación). El indicador de divulgación indica a los usuarios que si tocan en el controlador de vista de tabla asociada, se mostrará una nueva vista. El indicador de divulgación no produce ningún evento.

      ![][update-todo-list-view-controller-2]

2. En **TodoListViewController.m**, elimine las siguientes operaciones y su contenido de forma conjunta. No los necesitamos:

        -(NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath

        -(UITableViewCellEditingStyle)tableView:(UITableView *)tableView editingStyleForRowAtIndexPath:(NSIndexPath *)indexPath

        -(void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle
         forRowAtIndexPath:(NSIndexPath *)indexPath

### <a name="add-view-controller"></a>Incorporación del controlador de vista de elementos Todo

1. Cree una nueva clase Cocoa Touch denominada **QSItemViewController**, derivada de **UIViewController**.

2. En **QSItemViewController.h** agregue la siguiente definición de tipo:

        typedef void (^ItemEditCompletionBlock) (NSDictionary *editedItem);

3. En **QSItemViewController.h**, agregue una propiedad para que contenga el elemento que desea modificar, y una propiedad para la devolución de llamada que se invoca cuando el usuario presiona el botón de volver en la vista de detalle:

        @property (nonatomic, weak) NSMutableDictionary *item;
        @property (nonatomic, strong) ItemEditCompletionBlock editCompleteBlock;

4. En **QSItemViewController.m**, agregue dos propiedades privadas para los dos campos del elemento de tareas que se van a editar: el estado de finalización y el texto del elemento de tareas propiamente dicho:

        @interface QSItemViewController ()

        @property (nonatomic, strong) IBOutlet UITextField *itemText;
        @property (nonatomic, strong) IBOutlet UISegmentedControl *itemComplete;

        @end

5. En **QSItemViewController.m**, actualice la implementación de código auxiliar de **viewDidLoad** al siguiente código:

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

6. En **QSItemViewController.m**, agregue cuatro métodos adicionales para gestionar eventos de control editados:

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

7. En **QSItemViewController**, agregue el siguiente método, al que se llama cuando el usuario presiona el botón **Back** (Atrás) en la barra de navegación. El método puede llamarse en otros eventos, por lo que primero comprobamos la vista primaria. Si ha habido ningún cambio en el elemento, **self.item** se modifica y se llama a la devolución de llamada **editCompleteBlock**:

        - (void)didMoveToParentViewController:(UIViewController *)parent
        {
            if (![parent isEqual:self.parentViewController]) {
                NSNumber *completeValue = [NSNumber numberWithBool:self.itemComplete.selectedSegmentIndex == 0];
                
                Boolean changed =
                    [self.item valueForKey:@"text"] != [self.itemText text] ||
                    [self.item valueForKey:@"complete"] != completeValue;
                
                if (changed) {
                    [self.item setValue:[self.itemText text] forKey:@"text"];
                    [self.item setValue:completeValue forKey:@"complete"];
                    
                    self.editCompleteBlock(self.item);
                }
            }
        }

### <a name="add-segue"></a>Incorporación del controlador de vista de elementos Todo y de un objeto Segue al guion gráfico

1. Vuelva al archivo **MainStoryboard_iPhone.storyboard** mediante Project Navigator.

2. Agregue un nuevo controlador de vista para el elemento de tareas al guión gráfico, a la derecha del **Controlador de vista de lista de tareas** existente. Establezca la clase personalizada de este nuevo controlador de vista en **QSItemViewController**. Para obtener más información, consulte [Incorporación de una escena a un guión gráfico].

3. Agregue un objeto segue para **mostrar** desde el **controlador de vista de lista de tareas** hasta el **controlador de vista de elementos de tareas**. A continuación, en el inspector de atributos, establezca el identificador del objeto segue en **detailSegue**. 

    No cree este objeto segue desde cualquier celda o botón del controlador de vista de origen. En su lugar, presione CTRL y arrastre desde el icono del controlador de vista que se muestra debajo del **controlador de vista de lista de tareas** en la interfaz del guión gráfico hasta el **controlador de vista de elementos de tareas**.

    ![][todo-list-view-controller-add-segue]

    Si por accidente aplica el objeto segue desde una celda, desencadenará la transición dos veces cuando ejecute la aplicación, lo que dará como resultado este error:

        Nested push animation can result in corrupted navigation bar

    Para obtener más información sobre los objetos segue, consulte [Incorporación de un objeto segue entre escenas en un guión gráfico]. 

4. Agregue un campo de texto para el texto del elemento y un control segmentado para el estado de finalización al nuevo **Controlador de vista de elementos de tareas**, también con etiquetas. En el control segmentado, establezca el título del **Segmento 0** a **Sí** y el título del **Segmento 1** a **No**. Conecte estos nuevos campos a las salidas en el código. Para obtener más información, consulte [Creación de una interfaz de usuario] y [Controles segmentados].

      ![][add-todo-item-view-controller-3]

5. Conecte estos nuevos campos a las salidas correspondientes que ya ha agregado a **QSItemViewController.m**. Conecte el campo de texto del elemento a la salida **itemText** y el control segmentado de estado de finalización a la salida **itemComplete**. Para obtener más información, consulte [Creación de una conexión de salida].

6. Establezca el delegado del campo de texto en el controlador de vista. Presione la tecla CTRL y arrastre desde el campo de texto al icono del controlador de vista que se muestra debajo del **controlador de vista de elementos Todo** en la interfaz del guión gráfico, y seleccione la salida del delegado; esta acción indica al guión gráfico que el delegado de este campo de texto es este controlador de vista.

7. Compruebe que la aplicación funciona con todos los cambios realizados hasta el momento. Ejecute ahora la aplicación en el simulador. Agregue elementos a la lista Todo y haga clic en ellos. Verá el controlador de vista de elementos (actualmente vacío).

      ![][add-todo-item-view-controller-4]          ![][add-todo-item-view-controller-5]

### <a name="add-item-details"></a>Incorporación de detalles de elementos al controlador de vista de elementos Todo

1. Haremos referencia a **QSItemViewController** desde **QSTodoListViewController.m**. Por lo tanto, en **QSTodoListViewController.m**, vamos a agregar una línea para importar **QSItemViewController.h**.

        #import "QSItemViewController.h"

2. Agregue una nueva propiedad a la interfaz **QSTodoListViewController** en **QSTodoListViewController.m** para almacenar el elemento que se va a editar:

        @property (strong, nonatomic)   NSDictionary *editingItem;

3. Implemente **tableView:didSelectRowAtIndexPath:** en **QSTodoListViewController.m** para guardar el elemento que se va a editar y luego llame al objeto segue para mostrar la vista detallada..

        - (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
            NSManagedObject *item = [self.fetchedResultsController objectAtIndexPath:indexPath];
            self.editingItem = [MSCoreDataStore tableItemFromManagedObject:item]; // map from managed object to dictionary
            
            [self performSegueWithIdentifier:@"detailSegue" sender:self];
        }

4. Implemente **prepareForSegue:sender:** en **QSTodoListViewController.m** para pasar el elemento al ** controlador de vista de elementos Todo** y especifique la devolución de llamada cuando el usuario sale de la vista de detalles:

        - (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
            if ([[segue identifier] isEqualToString:@"detailSegue"]) {
                QSItemViewController *ivc = (QSItemViewController *) [segue destinationViewController];
                ivc.item = [self.editingItem mutableCopy];
                
                ivc.editCompleteBlock = ^(NSDictionary *editedValue) {
                    [self.todoService updateItem:editedValue completion:^(NSUInteger index) {
                        self.editingItem = nil;
                    }];
                };
            }
        }

5. Compruebe que la aplicación funciona con todos los cambios realizados hasta el momento. Ejecute ahora la aplicación en el simulador. Agregue elementos a la lista Todo y haga clic en ellos. Verá que el controlador de vista de elementos ya no está vacío, sino que muestra los detalles de los elementos Todo.

      ![][add-todo-item-view-controller-6]

### <a name="saving-edits"></a>Incorporación de compatibilidad para guardar ediciones

1. Cuando hace clic en el botón "Atrás" en la vista de navegación, las ediciones se pierden. Hemos enviado los datos a la vista detallada, pero los datos no se reenvían a la vista maestra. Dado que ya pasamos un puntero a una copia del elemento, podemos usar ese puntero para recuperar la lista de actualizaciones realizadas en el elemento y actualizarlo en el servidor. Para comenzar, primero actualice la clase contenedora de servidor de **QSTodoService** en **QSTodoService.m** quitando la operación **completeItem** y agregando una nueva operación **updateItem**. El motivo es que **completeItem** solo marca los elementos como completados, mientras que **updateItem** los actualiza.

        - (void)updateItem:(NSDictionary *)item completion:(QSCompletionBlock)completion
        {
            // Set the item to be complete (we need a mutable copy)
            NSMutableDictionary *mutable = [item mutableCopy];
            
            // Update the item in the TodoItem table and remove from the items array when we mark an item as complete
            [self.syncTable update:mutable completion:^(NSError *error) {
                [self logErrorIfNotNil:error];
                
                if (completion != nil) {
                    dispatch_async(dispatch_get_main_queue(), completion);
                }
            }];
        }

2. Elimine la declaración de **completeItem** de **QSTodoService.h** y agregue esta declaración a **updateItem**:

        - (void)updateItem:(NSDictionary *)item completion:(QSCompletionBlock)completion;

3. Ahora vamos a probar la aplicación. Compruebe que la aplicación funciona con todos los cambios realizados hasta el momento. Ejecute ahora la aplicación en el simulador. Agregue elementos a la lista Todo y haga clic en ellos. Intente editar un elemento y vuelva. Compruebe que la descripción del elemento está actualizada en la vista maestra de la aplicación. Actualice la aplicación con el gesto de arrastrar abajo y compruebe que la edición se refleja en el servicio remoto.

### <a name="conflict-handling-problem"></a>Problema de gestión de conflictos

1. Vamos a examinar lo que pasa cuando dos clientes diferentes intentan modificar los mismos datos al mismo tiempo. En el ejemplo siguiente, hay un elemento que dice "Mobile Services is Cool!". Vamos a cambiar esto, por ejemplo, a "I love Mobile Services!" en un dispositivo y a "I love Azure!" en otro.

      ![][conflict-handling-problem-1]

2. Inicie la aplicación en dos lugares: en dos dispositivos iOS o en el simulador y en un dispositivo iOS. Si no tiene un dispositivo físico en el que probar, inicie una instancia en el simulador y, usando un cliente REST, envíe una solicitud PATCH al servicio móvil. La URL de la solicitud PATCH refleja el nombre del servicio móvil, el nombre de la tabla de elementos Todo y el Id. de la tabla de elementos Todo que va a editar, mientras que el encabezado de x-zumo-application es la clave de la aplicación:

        PATCH https://donnam-tutorials.azure-mobile.net/tables/todoitem/D265929E-B17B-42D1-8FAB-D0ADF26486FA?__systemproperties=__version
        Content-Type: application/json
        x-zumo-application: xuAdWVDcLuCNfkTvOfaqzCCSBVHqoy96

        {
            "id": "CBBF4464-E08A-47C9-B6FB-6DCB30ACCE7E",
            "text": "I love Azure!"
        }

3. Ahora, actualice los elementos en las dos instancias de la aplicación. Verá un error impreso en el registro de salida en Xcode:

        TodoList[1575:4a2f] ERROR Error Domain=com.Microsoft.MicrosoftAzureMobileServices.ErrorDomain Code=-1170 "Not all operations completed successfully" UserInfo=0x8dd6310 {com.Microsoft.MicrosoftAzureMobileServices.ErrorPushResultKey=(
            "The server's version did not match the passed version"
        ), NSLocalizedDescription=Not all operations completed successfully}

  Esto se debe a que en el bloque de finalización, en la llamada a **pullWithQuery:completion:**, el parámetro de error será distinto de nil, lo que provocará que se imprima el error en la salida a través de **NSLog**.

### <a name="service-add-conflict-handling"></a>Actualización de QSTodoService para permitir la gestión de conflictos

1. Vamos a dejar que el usuario decida cómo gestionar el conflicto tratándolo en el cliente. Para ello, vamos a implementar el protocolo **MSSyncContextDelegate**. En **QSTodoService.h** y **QSTodoService.m**, cambie la declaración del método de fábrica **(QSTodoService *)defaultService;** por la siguiente instrucción para recibir el delegado del contexto de sincronización como parámetro:

        + (QSTodoService *)defaultServiceWithDelegate:(id)delegate;

2. En **QSTodoService.m**, cambie la línea **init** como se muestra a continuación, para recibir de nuevo el delegado del contexto de sincronización como parámetro:

€

3. En **QSTodoService.m**, cambie la llamada **init** en **defaultServiceWithDelegate** por **initWithDelegate**:

        service = [[QSTodoService alloc] initWithDelegate:delegate];

4. De vuelta en **QSTodoService.m**, cambie la inicialización de **self.client.syncContext** para pasar **syncDelegate** en lugar de **nil** en el delegado:

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:syncDelegate dataSource:store callback:nil];

### <a name="add-alert-view"></a>Incorporación del ayudante de vistas de alertas de IU para permitir la gestión de conflictos

1. Si hay un conflicto, vamos a permitir que el usuario elija qué versión desea mantener:
  * mantener la versión de cliente (que anula la versión en el servidor),
  * mantener la versión de servidor (que actualiza la tabla local del cliente) o
  * no mantener ninguna versión (cancela la inserción y deja la operación pendiente).

  Dado que también puede tener lugar otra actualización mientras visualizamos el mensaje, seguiremos mostrando opciones hasta que el servidor deje de devolver una respuesta de error. En nuestro código, vamos a usar una clase auxiliar que muestra una vista de alertas y toma un delegado que se invoca cuando se muestra dicha vista. Vamos a definir primero la clase auxiliar **QSUIAlertViewWithBlock**.

2. Agregue esta nueva clase, **QSUIAlertViewWithBlock**, mediante Xcode, y sobrescriba **QSUIAlertViewWithBlock.h** con el siguiente contenido:

        #import <Foundation/Foundation.h>

        typedef void (^QSUIAlertViewBlock) (NSInteger index);

        @interface QSUIAlertViewWithBlock : NSObject <UIAlertViewDelegate>

        - (id) initWithCallback:(QSUIAlertViewBlock)callback;
        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles;

        @end

3. A continuación, sobrescriba **QSUIAlertViewWithBlock.m** con el siguiente archivo:

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

### <a name="add-conflict-handling"></a>Incorporación de un controlador de conflictos al controlador de vista de lista Todo

1. En **QSTodoListViewController.m**, edite **viewDidLoad**. Reemplace la llamada a **defaultService** por una llamada a **defaultServiceWithDelegate** en su lugar:

        self.todoService = [QSTodoService defaultServiceWithDelegate:self];

2. En **QSTodoListViewController.h**, agregue **&lt;MSSyncContextDelegate&gt;** a la declaración de la interfaz para que podamos implementar el protocolo **MSSyncContextDelegate**.

        @interface QSTodoListViewController : UITableViewController<MSSyncContextDelegate, NSFetchedResultsControllerDelegate>

3. Agregue la siguiente instrucción de importación en la parte superior de **QSTodoListViewController.m**:

        #import "QSUIAlertViewWithBlock.h"

4. Finalmente, vamos a agregar las dos operaciones siguientes a **QSTodoListViewController.m** para usar esta clase auxiliar y pedir al usuario que resuelva el conflicto de una de tres maneras.

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

### <a name="test-app"></a>Prueba de la aplicación

Vamos a probar la aplicación con conflictos. Edite el mismo elemento en dos instancias distintas de la aplicación que se ejecuten al mismo tiempo, o con la aplicación y un cliente REST. 

Realice el gesto de actualizar en las instancias de la aplicación arrastrando desde la parte superior. Ahora verá un símbolo del sistema para resolver el conflicto:

![][conflict-ui]


### Resumen

Para configurar el proyecto completado en [Introducción a los datos sin conexión] con el fin de detectar conflictos, primero agregó la posibilidad de editar y actualizar los elementos de tareas pendientes.

Para ello, agregó un nuevo controlador de vista detallada de elementos, conectó el controlador de vista principal y el controlador de vista detallada y finalmente agregó la posibilidad de guardar las ediciones e insertarlas en la nube.

Luego, descubrió lo que pasa cuando hay un conflicto. Agregó compatibilidad con un controlador de conflictos mediante la implementación del protocolo **MSSyncContextDelegate**. También habilitó la compatibilidad con el delegado del contexto de sincronización a través de la clase de interfaz de servidor de **QSTodoService**, **QSTodoListViewController** y las clases complementarias.

Por el camino, agregó una clase auxiliar **QSUIAlertViewWithBlock** para mostrar una alerta a los usuarios y, para finalizar, agregó código a **QSTodoListViewController** para pedir al usuario que concilie el conflicto de una de tres maneras.

<!-- URLs. -->

[Actualización del proyecto de aplicación para permitir la edición]: #update-app
[Actualización del controlador de vista de lista Todo]: #update-list-view
[Incorporación del controlador de vista de elementos Todo]: #add-view-controller
[Incorporación del controlador de vista de elementos Todo y de un objeto Segue al guion gráfico]: #add-segue
[Incorporación de detalles de elementos al controlador de vista de elementos Todo]: #add-item-details
[Incorporación de compatibilidad para guardar ediciones]: #saving-edits
[Problema de gestión de conflictos]: #conflict-handling-problem
[Actualización de QSTodoService para permitir la gestión de conflictos]: #service-add-conflict-handling
[Incorporación del ayudante de vistas de alertas de IU para permitir la gestión de conflictos]: #add-alert-view
[Incorporación de un controlador de conflictos al controlador de vista de lista Todo]: #add-conflict-handling
[Prueba de la aplicación]: #test-app


[add-todo-item-view-controller-3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png
[add-todo-item-view-controller-4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png
[add-todo-item-view-controller-5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png
[add-todo-item-view-controller-6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png
[todo-list-view-controller-add-segue]: ./media/mobile-services-ios-handling-conflicts-offline-data/todo-list-view-controller-add-segue.png
[update-todo-list-view-controller-2]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png
[conflict-handling-problem-1]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png
[conflict-ui]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-ui.png


[Controles segmentados]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html
[Ayuda del editor de modelos de Core Data]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creación de una conexión de salida]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Creación de una interfaz de usuario]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Incorporación de un elemento Segue entre escenas de un guión gráfico]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Incorporación de una escena a un guión gráfico]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
[Datos principales]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Descargue el SDK de versión preliminar aquí]: http://aka.ms/Gc6fex
[Cómo usar la biblioteca de cliente de servicios móviles para iOS]: /es-es/documentation/articles/mobile-services-ios-how-to-use-client-library/
[Ejemplo de tareas iniciales sin conexión con iOS]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Introducción a los datos sin conexión]: /es-es/documentation/articles/mobile-services-ios-get-started-offline-data/
[Introducción a los servicios móviles]: /es-es/documentation/articles/mobile-services-ios-get-started/
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-ios-get-started-data/


<!--HONumber=42-->
