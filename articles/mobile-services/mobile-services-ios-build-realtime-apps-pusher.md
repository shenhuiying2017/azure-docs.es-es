<properties
	pageTitle="Creación de aplicaciones en tiempo real con Pusher (iOS) - Servicios móviles"
	description="Obtenga información acerca de cómo usar Pusher para enviar notificaciones a su aplicación de Servicios multimedia de Azure en iOS."
	services="mobile-services"
	documentationCenter="ios"
	authors="lindydonna"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="09/28/2015" 
	ms.author="donnam"/>


# Generación de aplicaciones en tiempo real con Servicios móviles y Pusher

En este tema se muestra cómo puede agregar funcionalidad en tiempo real a su aplicación basada en Servicios móviles de Azure. Cuando lo complete, los datos de TodoList se sincronizarán en tiempo real en todas las instancias en ejecución de la aplicación.

El tutorial [Notificaciones de inserción para usuarios][] muestra cómo usar notificaciones de inserción para informar a los usuarios de nuevos elementos en la lista Todo. Las notificaciones de inserción son una manera fantástica de mostrar cambios ocasionales. Sin embargo, a veces una aplicación necesita notificaciones en tiempo real frecuentes. Las notificaciones en tiempo real se pueden agregar al servicio móvil con la API de Pusher. En este tutorial, usamos Pusher con Servicios móviles para mantener una lista Todo sincronizada cuando se realizan cambios en cualquier instancia en ejecución de la aplicación.

Pusher es un servicio basado en la nube, al igual que Servicios móviles, que facilita enormemente la generación de aplicaciones en tiempo real. Puede usar Pusher para generar rápidamente sondeos en vivo, salas de chat, juegos multijugador, aplicaciones de colaboración, y transmitir contenido y datos activos, entre otras tareas. Para obtener más información, consulte [http://pusher.com](http://pusher.com).

En este tutorial se realiza un recorrido por estos pasos básicos para agregar colaboración en tiempo real a la aplicación de la lista Todo:

1. [Creación de una cuenta Pusher][]
2. [Actualización de la aplicación][]
3. [Instalación de los scripts de servidor][]
4. [Prueba de la aplicación][]

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles][].

## <a name="sign-up"></a>Creación de una cuenta Pusher nueva

[AZURE.INCLUDE [pusher-sign-up](../../includes/pusher-sign-up.md)]

## <a name="update-app"></a>Actualización de la aplicación

Ahora que tiene configurada una cuenta Pusher, el próximo paso es modificar el código de la aplicación iOS para la nueva funcionalidad.

###Instalar la biblioteca libPusher

La biblioteca [libPusher][] permite obtener acceso a Pusher desde iOS.

1. Descargue la biblioteca libPusher [desde aquí][libPusherDownload].

2. Cree un grupo llamado _libPusher_ en su proyecto.

3. En el Finder, descomprima el archivo descargado, seleccione las carpetas **libPusher-combined.a** y **/headers** y arrastre esos elementos al grupo **libPusher** del proyecto.

4. Active la opción **Copy items in destination group's folder** (Copiar elementos en la carpeta del grupo de destino) y, a continuación, haga clic en **Finish** (Finalizar).

	![][add-files-to-group]

   De esta forma, se copian los archivos libPusher en su proyecto.

5. En el proyecto raíz del explorador de proyectos, haga clic en **Build Phases** (Generar fases) y, a continuación, en **Add Build Phase** (Agregar fase de compilación) y **Add Copy Files** (Agregar archivos de copia).

6. Arrastre el archivo **libPusher-combined.a** del explorador de proyectos a la nueva fase de compilación.

7. Cambie **Destination** (Destino) por **Frameworks** (Marco) y haga clic en **Copy only when installing** (Copiar solo al instalar).

	![][add-build-phase]

8. En el área **Link Binary With Libraries** (Vincular binario con bibliotecas), agregue las siguientes bibliotecas:

	- libicucore.dylib
	- CFNetwork.framework
	- Security.framework
	- SystemConfiguration.framework

9. Finalmente, en **Build Settings** (Configuración de compilación), busque la configuración de compilación del destino **Other Linker Flags** (Otras marcas de vinculador) y agregue la marca **-all\_load**.

	![][add-linker-flag]

	Esto muestra la marca **-all\_load** establecida para el destino de compilación de depuración.

La biblioteca está ahora instalada y lista para usar.

### Agregar código a la aplicación

1. En Xcode, abra el archivo **QSTodoService.h** y agregue las siguientes declaraciones de método:

        // Allows retrieval of items by id
        - (NSUInteger) getItemIndex:(NSDictionary *)item;

        // To be called when items are added by other users
        - (NSUInteger) itemAdded:(NSDictionary *)item;

        // To be called when items are completed by other users
        - (NSUInteger) itemCompleted:(NSDictionary *)item;

2. Reemplace las declaraciones actuales de **addItem** y **completeItem** por lo siguiente:

		- (void) addItem:(NSDictionary *) item;
		- (void) completeItem: (NSDictionary *) item;

3. En **QSTodoService.m**, agregue el código siguiente para implementar los nuevos métodos:

        // Allows retrieval of items by id
		- (NSUInteger) getItemIndex:(NSDictionary *)item
		{
		    NSInteger itemId = [[item objectForKey: @"id"] integerValue];

		    return [items indexOfObjectPassingTest:^BOOL(id currItem, NSUInteger idx, BOOL *stop)
                 {
                     return ([[currItem objectForKey: @"id"] integerValue] == itemId);
                 }];
		}

        // To be called when items are added by other users
        -(NSUInteger) itemAdded:(NSDictionary *)item
		{
		    NSUInteger index = [self getItemIndex:item];

		    // Only complete action if item not already in list
		    if(index == NSNotFound)
		    {
        		NSUInteger newIndex = [items count];
		        [(NSMutableArray *)items insertObject:item atIndex:newIndex];
		        return newIndex;
		    }
		    else
        		return -1;
		}

        // To be called when items are completed by other users
		- (NSUInteger) itemCompleted:(NSDictionary *)item
		{
		    NSUInteger index = [self getItemIndex:item];

		    // Only complete action if item exists in items list
		    if(index != NSNotFound)
		    {
		        NSMutableArray *mutableItems = (NSMutableArray *) items;
		        [mutableItems removeObjectAtIndex:index];
		    }
		    return index;
		}

	QSTodoService ahora permite buscar elementos por **id** y agregar y completar elementos localmente sin enviar solicitudes explícitas al servicio remoto.

4. Reemplace los métodos actuales **addItem** y **completeItem** por el siguiente código:

		-(void) addItem:(NSDictionary *)item
		{
		    // Insert the item into the TodoItem table and add to the items array on completion
		    [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
        		[self logErrorIfNotNil:error];
		    }];
		}

		-(void) completeItem:(NSDictionary *)item
		{
		    // Set the item to be complete (we need a mutable copy)
		    NSMutableDictionary *mutable = [item mutableCopy];
		    [mutable setObject:@(YES) forKey:@"complete"];

		    // Update the item in the TodoItem table and remove from the items array on completion
		    [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {
		        [self logErrorIfNotNil:error];
		    }];
		}


	Tenga en cuenta que ahora los elementos se agregan y completan, junto con las actualizaciones de la interfaz de usuario, cuando se reciben eventos de Pusher en lugar de cuando se actualiza la tabla de datos.

5. En el archivo **QSTodoListViewController.h**, agregue las siguientes instrucciones de importación:

		#import "PTPusherDelegate.h"
		#import "PTPusher.h"
		#import "PTPusherEvent.h"
		#import "PTPusherChannel.h"

6. Modifique la declaración de la interfaz para agregar **PTPusherDelegate** para que tenga el siguiente aspecto:

		@interface QSTodoListViewController : UITableViewController<UITextFieldDelegate, PTPusherDelegate>

7. Agregue la propiedad nueva siguiente:

		@property (nonatomic, strong) PTPusher *pusher;

8. Agregue el siguiente código que declara un nuevo método:

		// Sets up the Pusher client
		- (void) setupPusher;

9. En **QSTodoListViewController.m**, agregue la siguiente línea debajo de las otras líneas **@synthesise** para implementar la nueva propiedad:

		@synthesize pusher = _pusher;

10. Ahora agregue el siguiente código para implementar el nuevo método:

		// Sets up the Pusher client
		- (void) setupPusher {

			// Create a Pusher client, using your Pusher app key as the credential
		    // TODO: Move Pusher app key to configuration file
		    self.pusher = [PTPusher pusherWithKey:@"**your_app_key**" delegate:self encrypted:NO];
		    self.pusher.reconnectAutomatically = YES;

    		// Subscribe to the 'todo-updates' channel
		    PTPusherChannel *todoChannel = [self.pusher subscribeToChannelNamed:@"todo-updates"];

    		// Bind to the 'todo-added' event
		    [todoChannel bindToEventNamed:@"todo-added" handleWithBlock:^(PTPusherEvent *channelEvent) {

		    	// Add item to the todo list
        		NSUInteger index = [self.todoService itemAdded:channelEvent.data];

        		// If the item was not already in the list, add the item to the UI
		        if(index != -1)
        		{
		            NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
        		    [self.tableView insertRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
		        }
		    }];

    		// Bind to the 'todo-completed' event
		    [todoChannel bindToEventNamed:@"todo-completed" handleWithBlock:^(PTPusherEvent *channelEvent) {

		    	// Update the item to be completed
		        NSUInteger index = [self.todoService itemCompleted:channelEvent.data];

        		// As long as the item did exit in the list, update the UI
		        if(index != NSNotFound)
		        {
		            NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
        		    [self.tableView deleteRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
		        }
		    }];
		}

11. Reemplace el marcador de posición `**your_app_key**` por el valor de la clave de aplicación que copió del cuadro de diálogo de información de la conexión anteriormente.

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

		// Ask the todoService to set the item's complete value to YES
	    [self.todoService completeItem:item];

Esta aplicación está ahora disponible para recibir eventos de Pusher y para actualizar la lista Todo como corresponda.



##<a name="install-scripts"></a>Instalación de los scripts de servidor



Lo único que queda es la configuración de los scripts de servidor. Insertaremos un script para cuando se inserte un elemento o se actualice en la tabla TodoList.



1. Inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.


2. En el Portal de administración, haga clic en la pestaña **Datos** y luego en la tabla **TodoItem**.

	![][1]



3. En **TodoItem**, haga clic en la pestaña **Script** y seleccione **Insertar**.


	![][2]



	Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.


4. Reemplace la función de inserción por el código siguiente:


		var Pusher = require('pusher');

		function insert(item, user, request) {

			request.execute({
	    		success: function() {
			        // After the record has been inserted, trigger immediately to the client
	    		    request.respond();

			        // Publish event for all other active clients
			        publishItemCreatedEvent(item);
			    }
			});

			function publishItemCreatedEvent(item) {

				// Ideally these settings would be taken from config
			    var pusher = new Pusher({
				  appId: '**your_app_id**',
				  key: '**your_app_key**',
				  secret: '**your_app_secret**'
				});

				// Publish event on Pusher channel
			    pusher.trigger( 'todo-updates', 'todo-added', item );
			}
		}



5. Reemplace los marcadores de posición en el script anterior por los valores que copió del cuadro de diálogo de información de la conexión anterior:

	- **`**your_app_id**`**: el valor de app&#95;id
	- **`**your_app_key**`**: el valor de app&#95;key
	- **`**your_app_key_secret**`**: el valor de app&#95;key&#95;secret


6. Haga clic en el botón **Save** (Guardar). Ahora ha configurado un script para publicar un evento en Pusher cada vez que se inserta un nuevo elemento en la tabla **TodoItem**.


7. Seleccione **Update** (Actualizar) en el cuadro desplegable **Operation** (Operación).


8. Reemplace la función de actualización por el código siguiente:

		var Pusher = require('pusher');

		function update(item, user, request) {

			request.execute({
	    		success: function() {
			        // After the record has been updated, trigger immediately to the client
	    		    request.respond();

			        // Publish event for all other active clients
			        publishItemUpdatedEvent(item);
			    }
			});

			function publishItemUpdatedEvent(item) {

				// Ideally these settings would be taken from config
			    var pusher = new Pusher({
				  appId: '**your_app_id**',
				  key: '**your_app_key**',
				  secret: '**your_app_secret**'
				});

				// Publish event on Pusher channel
			    pusher.trigger( 'todo-updates', 'todo-completed', item );

			}
		}



9. Repita el paso 5 para este script para reemplazar los marcadores de posición.


10. Haga clic en el botón **Save** (Guardar). Ahora ha configurado un script para publicar un evento en Pusher cada vez que se actualiza un nuevo elemento.



##<a name="test-app"></a>Prueba de la aplicación



Para realizar una prueba de la aplicación, tendrá que ejecutar dos instancias. Puede ejecutar una instancia en un dispositivo iOS y otra en el simulador de iOS.

1. Conecte el dispositivo iOS, presione el botón **Run** (Ejecutar) (o la tecla Command+R) para iniciar la aplicación en el dispositivo y, a continuación, detenga la depuración.

	Ahora ha instalado la aplicación en el dispositivo.

2. Ejecute la aplicación en el simulador de iOS y, al mismo tiempo, inicie la aplicación en el dispositivo iOS.

	Ahora dispone de dos instancias de la aplicación en ejecución.

3. Agregue un nuevo elemento Todo en una de las instancias de la aplicación.

	Compruebe que el elemento agregado aparezca en la otra instancia.

4. Marque un elemento Todo para que aparezca como completado en una instancia de la aplicación.

	Compruebe que el elemento desaparezca de la otra instancia.

Enhorabuena, ha configurado correctamente la aplicación de servicios móviles para sincronizarla en todos los clientes en tiempo real.

## <a name="nextsteps"> </a>[Pasos siguientes]

Ahora que ha visto lo fácil que es usar el servicio Pusher con Servicios móviles, siga estos vínculos para obtener más información sobre Pusher.

-   Documentación sobre la API de Pusher: <http://pusher.com/docs>
-   Tutoriales de Pusher: <http://pusher.com/tutorials>

Para obtener más información sobre el registro y uso de scripts de servidor, consulte [Referencia del script de servidor de Servicios móviles].

<!-- Anchors. -->
[Creación de una cuenta Pusher]: #sign-up
[Actualización de la aplicación]: #update-app
[Instalación de los scripts de servidor]: #install-scripts
[Prueba de la aplicación]: #test-app

<!-- Images. -->
[1]: ./media/mobile-services-ios-build-realtime-apps-pusher/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-build-realtime-apps-pusher/mobile-insert-script-push2.png

[add-files-to-group]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-files-to-group.png
[add-build-phase]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-build-phase.png
[add-linker-flag]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-linker-flag.png

<!-- URLs. -->
[Notificaciones de inserción para usuarios]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Introducción a los Servicios móviles]: /develop/mobile/tutorials/get-started
[libPusher]: http://go.microsoft.com/fwlink/p?LinkId=276999
[libPusherDownload]: http://go.microsoft.com/fwlink/p/?LinkId=276998


[Portal de administración de Azure]: https://manage.windowsazure.com/

[Referencia del script de servidor de Servicios móviles]: http://go.microsoft.com/fwlink/p/?LinkId=262293

<!---HONumber=Oct15_HO1-->