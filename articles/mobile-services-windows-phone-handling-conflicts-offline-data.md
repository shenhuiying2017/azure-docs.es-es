<properties 
	pageTitle="Control de conflictos con datos sin conexión en Servicios móviles (Windows Phone) | Centro de desarrollo móvil" 
	descripción="Obtenga información acerca de cómo usar Servicios móviles de Azure para gestionar los conflictos que se producen al sincronizar datos sin conexión en su aplicación de Windows Phone" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="wesmc"/>


# Control de conflictos con la sincronización de datos sin conexión en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-offline-conflicts](../includes/mobile-services-selector-offline-conflicts.md)]

<p>En este tema se explica cómo sincronizar los datos y controlar conflictos cuando se usan las capacidades sin conexión de Servicios móviles de Azure. En este tutorial, descargará una aplicación que admite datos sin conexión y con conexión, integrará el servicio móvil con la aplicación y, a continuación, iniciará sesión en el Portal de administración de Azure para ver y actualizar la base de datos cuando ejecute la aplicación.
</p>

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos sin conexión]. Antes de comenzar este tutorial, primero debe completar [Introducción a los datos sin conexión].


Este tutorial le guiará a través de estos pasos básicos:

1. [Descarga del proyecto de Windows Phone] 
2. [Incorporación de una columna de fecha de vencimiento para la base de datos]
  * [Actualización de la base de datos para servicios móviles back-end de .NET]  
  * [Actualización de la base de datos para servicios móviles con JavaScript]  
3. [Prueba de la aplicación en un servicio móvil]
4. [Actualización manual de los datos en el back-end para crear un conflicto]

Este tutorial requiere Visual Studio 2012 y el [SDK de Windows Phone 8].


## <a name="download-app"></a>Descarga del proyecto de ejemplo



Este tutorial se basa en el [código de ejemplo de control de conflictos], que es un proyecto de Windows Phone 8 para Visual Studio 2012.  
La interfaz de usuario de esta aplicación es similar a la aplicación en el tutorial [Introducción a los datos sin conexión], excepto en que hay una nueva columna de fecha para cada TodoItem.

![][0]


1. Descargue la versión para Windows Phone del [código de ejemplo de control de conflictos]. 

2. Instale [SQLite para Windows Phone 8] si no se ha instalado.

3. En Visual Studio 2012, abra el proyecto descargado. Agregue una referencia a **SQLite para Windows Phone** en **Windows Phone** > **Extensiones**.

4. En Visual Studio 2012, presione la tecla **F5** para compilar y ejecutar la aplicación en el depurador.
 
5. En la aplicación, escriba texto para algunos nuevos elementos todo y luego haga clic **Guardar** para guardarlos. También puede modificar la fecha de vencimiento de las tareas pendientes incorporadas.


Tenga en cuenta que la aplicación aún no está conectada a ningún servicio móvil, por lo que los botones de **inserción** y **extracción** lanzarán excepciones.


## <a name="add-column"></a>Incorporación de una columna al modelo de datos

En esta sección, va a actualizar la base de datos para incluir una tabla TodoItem con una columna de fecha de vencimiento. La aplicación le permite cambiar la fecha de vencimiento de un elemento en tiempo de ejecución para que pueda generar conflictos de sincronización más adelante en este tutorial. 

La clase  `TodoItem` en el ejemplo se define en MainPage.xaml.cs. Observe que la clase tiene el siguiente atributo que dirigirá las operaciones de sincronización a dicha tabla.

        [DataTable("TodoWithDate")]

Actualice la base de datos para incluir esta tabla.

### <a name="dotnet-backend"></a>Actualización de la base de datos para servicios móviles back-end de .NET 

Si usa el back-end de .NET para el servicio móvil, siga estos pasos para actualizar el esquema de la base de datos.

1. Abra el proyecto del servicio móvil de back-end de .NET en Visual Studio.
2. En el Explorador de soluciones de Visual Studio, en el proyecto del servicio, expanda la carpeta **Modelos** y abra ToDoItem.cs. Agregue la propiedad  `DueDate` como sigue.

          public class TodoItem : EntityData
          {
            public string Text { get; set; }
            public bool Complete { get; set; }
            public System.DateTime DueDate { get; set; }
          }


3. En el Explorador de soluciones de Visual Studio, expanda la carpeta **App_Start** y abra el archivo WebApiConfig.cs. 

    En el archivo WebApiConfig.cs, observe que la clase del inicializador de base de datos predeterminado procede de la clase  `DropCreateDatabaseIfModelChanges`. Esto significa que cualquier cambio en el modelo tendrá como resultado la anulación y la recreación de la tabla para adaptarse al modelo nuevo. De esta manera, los datos de la tabla se pierden y la tabla se reinicializa. Modifique el método Seed del inicializador de base de datos para que la inicialización de  `Seed()` funcione como sigue para inicializar la nueva columna DueDate. Guarde el archivo WebApiConfig.cs.

    >[AZURE.NOTE] Al usar el inicializador de base de datos predeterminado, Entity Framework eliminará la base de datos y la volverá a crear siempre que detecte un cambio del modelo de datos en la definición del modelo de Code First. Para realizar este cambio en el modelo de datos y mantener los datos existentes en la base de datos, debe utilizar Migraciones de Code First. Para obtener más información, consulte [Uso de Migraciones de Code First para actualizar el modelo de datos](/es-es/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations).


        new TodoItem { Id = "1", Text = "First item", Complete = false, DueDate = DateTime.Today },
        new TodoItem { Id = "2", Text = "Second item", Complete = false, DueDate = DateTime.Today },

          

4. En el Explorador de soluciones de Visual Studio, expanda la carpeta **Controladores** y abra ToDoItemController.cs. Cambie el nombre de la clase  `TodoItemController` a  `TodoWithDateController`. Esto cambiará el extremo de REST para las operaciones de tabla. 

        public class TodoWithDateController : TableController<TodoItem>
    

5. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto del servicio móvil back-end de .NET y haga clic en **Publicar** para publicar los cambios.


### <a name="javascript-backend"></a>Actualización de la base de datos para servicios móviles back-end de JavaScript

Para los servicios móviles back-end de JavaScript, agregará una nueva tabla llamada **TodoWithDate**. Para agregar la tabla **TodoWithDate** para los servicios móviles back-end de JavaScript, siga estos pasos.

  1. Inicie sesión en el [Portal de administración de Azure]. 

  2. Vaya a la pestaña **Datos** del servicio móvil. 

  3. Haga clic en **Crear** en la parte inferior de la página y cree una nueva tabla llamada **TodoWithDate**. 


## <a name="test-app"></a>Prueba de la aplicación en un servicio móvil

Ahora es el momento de probar la aplicación en los servicios móviles.

1. En el Portal de administración de Azure, busque la clave de la aplicación del servicio móvil; para ello, haga clic en **Administrar claves** en la barra de comandos de la pestaña **Panel**. Copie la **clave de aplicación**.

2. En el Explorador de soluciones de Visual Studio, abra el archivo App.xaml.cs en el proyecto de ejemplo cliente. Cambie la inicialización de **MobileServiceClient** para usar la URL del servicio móvil y la clave de aplicación:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3. En Visual Studio, presione la tecla **F5** para compilar y ejecutar la aplicación.

4. Como hiciera anteriormente, escriba texto en el cuadro de texto y luego haga clic en **Guardar** para guardar algunos de los nuevos elementos todo. De esta forma, los datos se guardan en la tabla de sincronización local, pero no en el servidor.

    ![][0]

5. Para ver el estado actual de la base de datos, inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.

  * Si usa el back-end de JavaScript para el servicio móvil, haga clic en la pestaña **Datos** y, a continuación, haga clic en la tabla **TodoWithDate**. Haga clic en **Examinar** para ver si la tabla aún está vacía, ya que no hemos insertado los cambios de la aplicación en el servidor.

        ![][1]

  *  Si usa el back-end de .NET para el servicio móvil, haga clic en la pestaña **Configurar** y, a continuación, haga clic en la base de datos SQL. Haga clic en **Administrar** en la parte inferior de la pantalla para iniciar sesión en el Portal de administración de Azure en SQL para ver la base de datos. Para ello, ejecute una consulta SQL parecida a la siguiente:
    
            SELECT * FROM todolist.todowithdate

        ![][2]

   	 

7. De nuevo en la aplicación, haga clic en el botón de **inserción**.

8. En el Portal de administración, haga clic en **Actualizar** en la tabla **TodoItem**. Ahora debe ver los datos que ha escrito en la aplicación.

   	![][3]

9. Deje la opción **Emulator WVGA 512MB** activada para la siguiente sección donde ejecutará la aplicación en dos emuladores para generar un conflicto.

## <a name="handle-conflict"></a>Actualización de los datos en el back-end para crear un conflicto

En un escenario real, se produciría un conflicto de sincronización cuando una aplicación inserta actualizaciones en un registro de la base de datos y, a continuación, otra aplicación trata de insertar un cambio en el mismo registro usando un campo de versión obsoleta de dicho registro. Si una instancia de la aplicación trata de actualizar el mismo registro, sin extraer el registro actualizado, se produciría un conflicto y se detectaría como una excepción  `MobileServicePreconditionFailedException` en la aplicación.  

En esta sección, ejecutará dos instancias de la aplicación al mismo tiempo para generar un conflicto. 


1. Si la opción **Emulator WVGA 512MB** no está aún activada, presione **Ctrl+F5** para reiniciarla.

2. En Visual Studio, cambie el dispositivo de salida a **Emulator WVGA** y ejecute una segunda instancia de la aplicación en el nuevo emulador con **F5**.
 
    ![][5]
 
   
3. En la segunda instancia de la aplicación, haga clic en el botón de **extracción** para sincronizar el almacén local con la base de datos del servicio móvil. Ambas instancias de la aplicación deben tener los mismos datos.
 
    ![][6]

4. En la segunda instancia de la aplicación, active la casilla para completar uno de los elementos y luego haga clic en el botón de **inserción** para insertar el cambio en la base de datos remota. En la siguiente captura de pantalla, **Pick up James** se ha completado, lo que indica que ya se ha recogido a James. La primera instancia de la aplicación tiene ahora un registro obsoleto.

    ![][9]

5. En la primera instancia de la aplicación, intente cambiar la fecha del registro obsoleto y luego haga clic en el botón de **inserción** para intentar actualizar la base de datos remota con el registro obsoleto. En la siguiente captura de pantalla, vamos a intentar programar la recogida de James el **10/05/2014**.

    ![][7]

6. Cuando hace clic en el botón de **inserción** para efectuar el cambio de fecha, verá un cuadro de diálogo que indica que se ha detectado el conflicto. Se le preguntará cómo va a resolver el conflicto. Elija una de las opciones para resolver el conflicto.

    En la situación que se muestra a continuación, James ya ha sido recogido. Así que no es necesario programar su recogida el **10/05/2014**. Se seleccionaría la opción **Usar versión de servidor** así que la primera instancia de la aplicación tendría actualizado ese registro con el registro del servidor. 

    ![][8]

## Revisión del código para controlar los conflictos de sincronización

Para configurar la característica sin conexión para detectar conflictos, debe incluir una columna de versión en la base de datos local y en el objeto de transferencia de datos. La clase  `TodoItem` tiene el siguiente miembro:

        [Version]
        public string Version { get; set; }

La columna `__version` también se especifica en la base de datos local configurada en el método  `OnNavigatedTo()`.

Para controlar los conflictos de sincronización sin conexión en el código, se crea una clase que implemente  `IMobileServiceSyncHandler`. Transfiera un objeto de este tipo en la llamada a  `InitializeAsync`:

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

La clase  `SyncHandler` de **MainPage.xaml.cs** implementa  `IMobileServiceSyncHandler`. Se llama al método  `ExecuteTableOperationAsync` cuando se envía cada operación de inserción al servidor. Si se lanza una excepción del tipo  `MobileServicePreconditionFailedException`, significa que hay un conflicto entre las versiones local y remota de un elemento.

Para resolver conflictos a favor del elemento local, simplemente debe reintentar la operación. Cuando se ha producido un conflicto, la versión del elemento local se actualizará para coincidir con la versión del servidor, por lo que la ejecución de la operación de nuevo sobrescribirá los cambios del servidor con los cambios locales:

    await operation.ExecuteAsync(); 

Para resolver los conflictos a favor del elemento del servidor, simplemente vuelva de  `ExecuteTableOperationAsync`. La versión local del objeto se descartará y se reemplazará por el valor del servidor.

Para detener la operación de inserción (pero mantener los cambios en cola), use el método  `AbortPush()`:

    operation.AbortPush();

Esto detendrá la operación de inserción actual, pero mantendrá todos los cambios pendientes, incluida la operación actual, si se llama a  `AbortPush` desde  `ExecuteTableOperationAsync`. La próxima vez que se llame a  `PushAsync()`, estos cambios se enviarán al servidor. 

Cuando se cancela una inserción,  `PushAsync` producirá un  `MobileServicePushFailedException` y la propiedad de excepción  `PushResult.Status` tendrá el valor  `MobileServicePushStatus.CancelledByOperation`. 


<!-- Anchors. -->
[Descarga del proyecto de Windows Phone]: #download-app
[Crear el servicio móvil]: #create-service
[Incorporación de una columna de fecha de vencimiento para la base de datos]: #add-column
[Actualización de la base de datos para servicios móviles back-end de .NET]: #dotnet-backend  
[Actualización de la base de datos para servicios móviles con JavaScript]: #javascript-backend
[Prueba de la aplicación en un servicio móvil]: #test-app
[Actualización manual de los datos en el back-end para crear un conflicto]: #handle-conflict
[Pasos siguientes]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
[1]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-empty.png
[2]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-empty-sql.png
[3]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-push1.png
[5]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/vs-emulator-wvga.png
[6]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-synced.png
[7]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-date-change.png
[8]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-conflict-detected.png
[9]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-item-completed.png



<!-- URLs -->
[Código de ejemplo de control de conflictos]: http://go.microsoft.com/fwlink/?LinkId=398257
[Introducción a los servicios móviles]: /es-es/documentation/articles/mobile-services-windows-phone-get-started/
[Introducción a los datos sin conexión]: /es-es/documentation/articles/mobile-services-windows-phone-get-started-offline-data
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Control de conflictos de base de datos]: /es-es/documentation/articles/mobile-services-windows-phone-handle-database-conflicts/#test-app
[SDK de Windows Phone 8]: http://go.microsoft.com/fwlink/p/?linkid=268374
[SQLite para Windows Phone 8]: http://go.microsoft.com/fwlink/?LinkId=397953
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-windows-phone-get-started-data/


<!--HONumber=42-->
