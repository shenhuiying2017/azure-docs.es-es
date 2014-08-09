<properties linkid="develop-mobile-tutorials-build-realtime-apps-with-pusher-ios" urlDisplayName="Build Realtime Apps with Pusher" pageTitle="Build Realtime Apps with Pusher (iOS) - Mobile Services" metaKeywords="" description="Learn how to use Pusher to send notifications to your Azure Media Services app on iOS." metaCanonical="" services="" documentationCenter="Mobile" title="Build Real-time Apps with Mobile Services and Pusher" authors="" solutions="" manager="" editor="" />

Generación de aplicaciones en tiempo real con Servicios móviles y Pusher
========================================================================

[iOS]( "iOS")

En este tema se muestra cómo puede agregar funcionalidad en tiempo real a su aplicación basada en Servicios móviles de Azure. Cuando se complete, los datos de TodoList se sincronizarán en tiempo real en todas las instancias en ejecución de la aplicación.

El tutorial [Notificaciones de inserción para usuarios](/es-es/develop/mobile/tutorials/push-notifications-to-users-ios) muestra cómo usar notificaciones de inserción para informar a los usuarios de nuevos elementos en la lista Todo. Las notificaciones de inserción son una manera fantástica de mostrar cambios ocasionales. Sin embargo, a veces una aplicación necesita notificaciones en tiempo real frecuentes. Las notificaciones en tiempo real se pueden agregar al servicio móvil con la API de Pusher. En este tutorial, usamos Pusher con Servicios móviles para mantener una lista Todo sincronizada cuando se realizan cambios en cualquier instancia en ejecución de la aplicación.

Pusher es un servicio basado en la nube, al igual que Servicios móviles, que facilita enormemente la generación de aplicaciones en tiempo real. Puede usar Pusher para generar rápidamente sondeos en vivo, salas de chat, juegos multijugador, aplicaciones de colaboración, y transmitir contenido y datos activos, entre otras tareas. Para obtener más información, consulte <http://pusher.com>.

En este tutorial se realiza un recorrido por estos pasos básicos para agregar colaboración en tiempo real a la aplicación de la lista Todo:

1.  [Creación de una cuenta Pusher](#sign-up)
2.  [Actualización de la aplicación](#update-app)
3.  [Instalación de los scripts de servidor](#install-scripts)
4.  [Prueba de la aplicación](#test-app)

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles](/es-es/develop/mobile/tutorials/get-started).

Creación de una cuenta Pusher
-----------------------------

[WACOM.INCLUDE [pusher-sign-up](../includes/pusher-sign-up.md)]

Actualización de la aplicación
------------------------------

Ahora que tiene configurada una cuenta Pusher, el próximo paso es modificar el código de la aplicación iOS para la nueva funcionalidad.

### Instalar la biblioteca libPusher

La biblioteca [libPusher](http://go.microsoft.com/fwlink/p?LinkId=276999) le permite obtener acceso a Pusher desde iOS.

1.  Descargue la biblioteca libPusher [desde aquí](http://go.microsoft.com/fwlink/p/?LinkId=276998).

2.  Cree un grupo llamado *libPusher* en su proyecto.

3.  En el Finder, descomprima el archivo descargado, seleccione las carpetas **libPusher-combined.a** y **/headers** y arrastre esos elementos al grupo **libPusher** del proyecto.

4.  Seleccione **Copy items in destination group's folder** y, a continuación, haga clic en **Finish**.

    ![](./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-files-to-group.png)

    De esta forma, se copian los archivos libPusher en su proyecto.

5.  En el proyecto raíz del explorador de proyectos, haga clic en **Build Phases** y, a continuación, en **Add Build Phase** y **Add Copy Files**.

6.  Arrastre el archivo **libPusher-combined.a** del explorador de proyectos a la nueva fase de compilación.

7.  Cambie el valor de **Destination** por **Frameworks** y haga clic en **Copy only when installing**.

    ![](./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-build-phase.png)

8.  En el área **Link Binary With Libraries**, agregue las siguientes bibliotecas:

    -   libicucore.dylib
    -   CFNetwork.framework
    -   Security.framework
    -   SystemConfiguration.framework

9.  Finalmente, en **Build Settings**, busque la configuración de compilación de destino **Other Linker Flags** y agregue la marca **-all\_load**.

    ![](./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-linker-flag.png)

    De esta forma, se muestra la marca **-all\_load** establecida para el destino de compilación de depuración.

La biblioteca está ahora instalada y lista para usar.

### Agregar código a la aplicación

1.  En Xcode, abra el archivo **QSTodoService.h** y agregue las siguientes declaraciones de método:

         // Permite la recuperación de elementos por identificador
         - (NSUInteger) getItemIndex:(NSDictionary *)item;

         // Puede llamarse cuando otros usuarios agregan los elementos
         - (NSUInteger) itemAdded:(NSDictionary *)item;
            
         // Puede llamarse cuando otros usuarios completan los elementos
         - (NSUInteger) itemCompleted:(NSDictionary *)item;

2.  Reemplace las declaraciones existentes de **addItem** y **completeItem** por lo siguiente:

         - (void) addItem:(NSDictionary *) item;
         - (void) completeItem: (NSDictionary *) item;

3.  En **QSTodoService.m**, agregue el código siguiente para implementar los nuevos métodos:

         // Permite la recuperación de elementos por identificador
         - (NSUInteger) getItemIndex:(NSDictionary *)item
         {
             NSInteger itemId = [[item objectForKey: @"id"] integerValue];

             return [items indexOfObjectPassingTest:^BOOL(id currItem, NSUInteger idx, BOOL *stop)
                  {
                      return ([[currItem objectForKey: @"identificador"] integerValue] == itemId);
                  }];
         }

         // Puede llamarse cuando otros usuarios agregan los elementos
         -(NSUInteger) itemAdded:(NSDictionary *)item
         {
             NSUInteger index = [self getItemIndex:item];

             // Solo completar la acción si el elemento no está ya en la lista
             if(index == NSNotFound)
             {
                NSUInteger newIndex = [items count];
                 [(NSMutableArray *)items insertObject:item atIndex:newIndex];
                 return newIndex;
             }
             else
                return -1;
         }

         // Puede llamarse cuando otros usuarios completan los elementos
         - (NSUInteger) itemCompleted:(NSDictionary *)item
         {
             NSUInteger index = [self getItemIndex:item];

             // Solo completar la acción si el elemento está en la lista de elementos
             if(index != NSNotFound)
             {
                 NSMutableArray *mutableItems = (NSMutableArray *) items;
                 [mutableItems removeObjectAtIndex:index];
             }          
             return index;
         }

    QSTodoService ahora le permite buscar elementos por **identificador** y agregar y completar elementos localmente sin enviar solicitudes explícitas al servicio remoto.

4.  Reemplace los métodos existentes **addItem** y **completeItem** por el siguiente código:

         -(void) addItem:(NSDictionary *)item
         {
             // Insertar el elemento en la tabla TodoItem y agregarlo a la matriz de elementos al terminar
             [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
                [self logErrorIfNotNil:error];
             }];
         }

         -(void) completeItem:(NSDictionary *)item
         {
             // Establecer el elemento que se vaya a completar (necesitamos una copia mutable).
             NSMutableDictionary *mutable = [item mutableCopy];
             [mutable setObject:@(YES) forKey:@"complete"];

             // Actualizar el elemento en la tabla TodoItem y quitarlo de la matriz de elementos al terminar
             [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {
                 [self logErrorIfNotNil:error];
             }];
         }

    Tenga en cuenta que ahora los elementos se agregan y completan, junto con las actualizaciones de la interfaz de usuario, cuando se reciben eventos de Pusher en lugar de cuando se actualiza la tabla de datos.

5.  En el archivo **QSTodoListViewController.h**, agregue las siguientes instrucciones de importación:

         #import "PTPusherDelegate.h"
         #import "PTPusher.h"
         #import "PTPusherEvent.h"
         #import "PTPusherChannel.h"

6.  Modifique la declaración de la interfaz para agregar **PTPusherDelegate** de forma que tenga el aspecto siguiente:

         @interface QSTodoListViewController : UITableViewController<UITextFieldDelegate, PTPusherDelegate>

7.  Agregue la propiedad nueva siguiente:

         @property (nonatomic, strong) PTPusher *pusher;

8.  Agregue el siguiente código que declara un nuevo método:

         // Configurar el cliente Pusher
         - (void) setupPusher;

9.  En **QSTodoListViewController.m**, agregue la siguiente línea debajo de las otras líneas **@synthesise** para implementar la nueva propiedad:

         @synthesize pusher = _pusher;

10. Ahora agregue el siguiente código para implementar el nuevo método:

         // Configurar el cliente Pusher
        - (void) setupPusher {
            
            // Crear un cliente Pusher con la clave de aplicación de Pusher como credencial.
            // TODO: mover la clave de aplicación de Pusher al archivo de configuración
            self.pusher = [PTPusher pusherWithKey:@"**su_clave_de_aplicación**" delegate:self encrypted:NO];
            self.pusher.reconnectAutomatically = YES;
                
            // Suscribirse al canal 'todo-updates'
            PTPusherChannel *todoChannel = [self.pusher subscribeToChannelNamed:@"todo-updates"];
                
            // Establecer la vinculación al evento 'todo-added'
            [todoChannel bindToEventNamed:@"todo-added" handleWithBlock:^(PTPusherEvent *channelEvent) {
                
                // Agregar el elemento a la lista todo
                NSUInteger index = [self.todoService itemAdded:channelEvent.data];
            
                // Si el elemento no está ya en la lista, agregar el elemento a la interfaz de usuario
                if(index != -1)
                {
                    NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
                    [self.tableView insertRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
                }
            }];

            // Establecer la vinculación al evento 'todo-completed'
            [todoChannel bindToEventNamed:@"todo-completed" handleWithBlock:^(PTPusherEvent *channelEvent) {
                
                // Actualizar el elemento que se vaya a completar
                NSUInteger index = [self.todoService itemCompleted:channelEvent.data];
                    
                // Siempre que el elemento salga de la lista, actualizar la interfaz de usuario
                if(index != NSNotFound)
                {
                    NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
                    [self.tableView deleteRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
                }               
            }];
        }

11. Reemplace el marcador de posición `**su_clave_de_aplicación**` por el valor de la clave de aplicación que copió del cuadro de diálogo de información de la conexión anteriormente.

12. Reemplace el método **onAdd** por el código siguiente:

        - (IBAction)onAdd:(id)sender
        {
            if (itemText.text.length  == 0) {
                return;
            }

            NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };
            [self.todoService addItem:item];

            itemText.text = @"";
        }

13. En el archivo **QSTodoListViewController.m**, busque el método (void)viewDidLoad y agregue una llamada al método **setupPusher** de manera que las primeras líneas sean:

        - (void)viewDidLoad
        {
            [super viewDidLoad];
            [self setupPusher];

14. Al final del método **tableView:commitEditingStyle:forRowAtIndexPath**, reemplace la llamada a **completeItem** por el siguiente código:

        // Solicitar a todoService que establezca el valor completo del elemento en YES
        [self.todoService completeItem:item];

Esta aplicación está ahora disponible para recibir eventos de Pusher y para actualizar la lista Todo como corresponda.

Instalación de los scripts de servidor
--------------------------------------

Lo único que queda es la configuración de los scripts de servidor. Insertaremos un script para cuando se inserte un elemento o se actualice en la tabla TodoList.

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.

2.  En el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItem**.

    ![](./media/mobile-services-ios-build-realtime-apps-pusher/mobile-portal-data-tables.png)

3.  En **TodoItem**, haga clic en la pestaña **Script** y seleccione **Insert**.

    ![](./media/mobile-services-ios-build-realtime-apps-pusher/mobile-insert-script-push2.png)

    Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

4.  Reemplace la función de inserción por el código siguiente:

         var Pusher = require('pusher');
            
         function insert(item, user, request) {   

             request.execute({
                success: function() {
                     // Una vez que se haya insertado el registro, realizar la activación de inmediato en el cliente
                    request.respond();

                     // Publicar el evento para todos los demás clientes activos
                     publishItemCreatedEvent(item);
                 }
             });
            
             function publishItemCreatedEvent(item) {
            
                 // Lo ideal es que estos valores se tomen de la configuración
                 var pusher = new Pusher({
                   appId: '**su_identificador_de_aplicación*',
                   key: '**su_clave_de_aplicación**',
                   secret: '**su_secreto_de_aplicación**'
                 });     
            
                 // Publicar el evento en el canal de Pusher
                 pusher.trigger( 'todo-updates', 'todo-added', item );  
             }
         }

5.  Reemplace los marcadores de posición en el script anterior por los valores que copió del cuadro de diálogo de información de la conexión anterior:

    -   **`**su_identificador_de_aplicación**`**: el valor de identificador de aplicación
    -   **`**su_clave_de_aplicación**`**: el valor de clave de aplicación
    -   **`**su_secreto_de_clave_de_aplicación**`**: el secreto de calve de aplicación

6.  Haga clic en el botón **Save**. Ahora ha configurado un script para publicar un evento en Pusher cada vez que se inserta un nuevo elemento en la tabla **TodoItem**.

7.  Seleccione **Update** en el cuadro desplegable **Operation**.

8.  Reemplace la función de actualización por el código siguiente:

         var Pusher = require('pusher');
            
         function update(item, user, request) {   

             request.execute({
                success: function() {
                     // Una vez que se haya actualizado el registro, realizar la activación de inmediato en el cliente
                    request.respond();

                     // Publicar el evento para todos los demás clientes activos
                     publishItemUpdatedEvent(item);
                 }
             });
            
             function publishItemUpdatedEvent(item) {
            
                 // Lo ideal es que estos valores se tomen de la configuración
                 var pusher = new Pusher({
                   appId: '**su_identificador_de_aplicación*',
                   key: '**su_clave_de_aplicación**',
                   secret: '**su_secreto_de_aplicación**'
                 });     
            
                 // Publicar el evento en el canal de Pusher
                 pusher.trigger( 'todo-updates', 'todo-completed', item );
            
             }
         }

9.  Repita el paso 5 para este script para reemplazar los marcadores de posición.

10. Haga clic en el botón **Save**. Ahora ha configurado un script para publicar un evento en Pusher cada vez que se actualiza un nuevo elemento.

Prueba de la aplicación
-----------------------

Para realizar una prueba de la aplicación, tendrá que ejecutar dos instancias. Puede ejecutar una instancia en un dispositivo iOS y otra en el simulador de iOS.

1.  Conecte el dispositivo iOS, presione el botón **Run** (o la tecla Command+R) para iniciar la aplicación en el dispositivo y, a continuación, detenga la depuración.

    Ahora ha instalado la aplicación en el dispositivo.

2.  Ejecute la aplicación en el simulador de iOS y, al mismo tiempo, inicie la aplicación en el dispositivo iOS.

    Ahora dispone de dos instancias de la aplicación en ejecución.

3.  Agregue un nuevo elemento Todo en una de las instancias de la aplicación.

    Compruebe que el elemento agregado aparezca en la otra instancia.

4.  Marque un elemento Todo para que aparezca como completado en una instancia de la aplicación.

    Compruebe que el elemento desaparezca de la otra instancia.

Enhorabuena, ha configurado correctamente la aplicación de servicios móviles para sincronizarla en todos los clientes en tiempo real.

Pasos siguientes
----------------

Ahora que ha visto lo fácil que es usar el servicio Pusher con Servicios móviles, siga estos vínculos para obtener más información sobre Pusher.

-   Documentación sobre la API de Pusher: <http://pusher.com/docs>
-   Tutoriales de Pusher: <http://pusher.com/tutorials>

Para obtener más información sobre el registro y uso de scripts de servidor, consulte [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/p/?LinkId=262293).

