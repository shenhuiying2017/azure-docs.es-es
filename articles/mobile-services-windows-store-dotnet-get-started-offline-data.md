
<properties linkid="develop-mobile-tutorials-get-started-offline-data-dotnet" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use offline data in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data in Mobile Services" authors="wesmc" />

Introducción a los datos sin conexión en Servicios móviles
==========================================================

[C\# para Tienda Windows](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data "C# para Tienda Windows")

En este tema se muestra cómo usar las capacidades sin conexión de Servicios móviles de Azure. Las características sin conexión de Servicios móviles de Azure permiten interactuar con una base de datos local cuando el usuario se encuentra en un escenario sin conexión con su servicio móvil. Las características sin conexión le permiten sincronizar los cambios locales con el servicio móvil cuando vuelva a estar en línea.

En este tutorial, actualizará la aplicación desde el tutorial [Introducción a los Servicios móviles](/en-us/documentation/articles/mobile-services-windows-store-get-started/) o [Introducción a los datos](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/) para que admita las características sin conexión de Servicios móviles de Azure. A continuación, agregará datos en un escenario sin conexión desconectado, sincronizará dichos elementos con la base de datos en línea e iniciará sesión en el Portal de administración de Azure para ver los cambios efectuados en los datos al ejecutar la aplicación.

> [WACOM.NOTE] Este tutorial está destinado a profundizar en el uso de Azure con los Servicios móviles para almacenar y recuperar datos en una aplicación de la Tienda Windows. Para ello, en este tema se recorren muchos de los pasos que se completan automáticamente en el inicio rápido de Servicios móviles. Si esta es la primera vez que usa los Servicios móviles, considere la posibilidad de completar antes el tutorial [Introducción a los Servicios móviles](/en-us/documentation/articles/mobile-services-windows-store-get-started/).

Este tutorial le guiará a través de estos pasos básicos:

1.  [Actualización de la aplicación para que admita características sin conexión](#enable-offline-app)
2.  [Prueba de la aplicación en un escenario sin conexión](#test-offline-app)
3.  [Actualización de la aplicación para volver a conectar el servicio móvil](#update-online-app)
4.  [Prueba de la aplicación conectada al servicio móvil](#test-online-app)

Este tutorial requiere lo siguiente:

-   Visual Studio 2013 en Windows 8.1.
-   Finalización del tutorial [Introducción a los Servicios móviles](/en-us/documentation/articles/mobile-services-windows-store-get-started/) o [Introducción a los datos](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/).
-   Paquete NuGet del SDK de Servicios móviles de Azure versión 1.3.0-alpha
-   Paquete NuGet del almacén SQLite de Servicios móviles de Azure 0.1.0-alpha
-   SQLite para Windows 8.1

> [WACOM.NOTE] Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28).

Actualización de la aplicación para que admita características sin conexión
---------------------------------------------------------------------------

Las características sin conexión de Servicios móviles de Azure permiten interactuar con una base de datos local cuando el usuario se encuentra en un escenario sin conexión con su servicio móvil. Para usar estas características en la aplicación, inicialice `MobileServiceClient.SyncContext` en un almacén local. A continuación, obtenga una referencia a la tabla mediante la interfaz `IMobileServiceSyncTable`.

En esta sección se utiliza SQLite como almacén local de las características sin conexión.

> [WACOM.NOTE] Puede omitir esta sección y simplemente descargar una versión del proyecto de introducción que ya permite la funcionalidad sin conexión. Para descargar un proyecto que admita la característica sin conexión, consulte [Getting Started Offline Sample](http://go.microsoft.com/fwlink/?LinkId=394777).

1.  Instale SQLite. Puede instalarlo desde este vínculo, [SQLite para Windows 8.1](http://go.microsoft.com/fwlink/?LinkId=394776).

    > [WACOM.NOTE] Si utiliza Internet Explorer, es posible que al hacer clic en el vínculo para instalar SQLite se le solicite que descargue .vsix como archivo .zip. Guarde el archivo en una ubicación del disco duro con la extensión .vsix en lugar de .zip. A continuación, haga doble clic en el archivo .vsix en el Explorador de Windows para ejecutar la instalación.

2.  En Visual Studio, abra el proyecto que finalizó en el tutorial [Introducción a los Servicios móviles](/en-us/documentation/articles/mobile-services-windows-store-get-started/) o [Introducción a los datos](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/). Agregue una referencia de **extensión de Windows** a **SQLite para Windows en tiempo de ejecución (Windows 8.1)**.

    ![](./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png)

    > [WACOM.NOTE] SQLite en tiempo de ejecución requiere que se cambie la arquitectura de procesador del proyecto que se compila a **x86**, **x64** o **ARM**. No se admite **cualquier CPU**.

3.  En el Explorador de soluciones de Visual Studio, haga clic con el botón secundario en el proyecto de la aplicación cliente y haga clic en **Administrar paquetes de NuGet** para ejecutar el Administrador de paquetes NuGet. Busque **SQLiteStore** para instalar el paquete **WindowsAzure.MobileServices.SQLiteStore**.

    ![](./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png)

4.  En el Explorador de soluciones de Visual Studio, abra el archivo MainPage.xaml.cs. Agregue las siguientes instrucciones using en la parte superior del archivo:

         using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
         using Microsoft.WindowsAzure.MobileServices.Sync;
         using Newtonsoft.Json.Linq;

5.  En Mainpage.xaml.cs, reemplace la declaración de `todoTable` por una declaración de tipo `IMobileServicesSyncTable` que se inicializa llamando a `MobileServicesClient.GetSyncTable()`.

         //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
         private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();

6.  En MainPage.xaml.cs, actualice la clase `TodoItem` para que incluya la propiedad del sistema **Version** de la forma siguiente.

         public class TodoItem
         {
           public string Id { get; set; }
           [JsonProperty(PropertyName = "text")]
           public string Text { get; set; }
           [JsonProperty(PropertyName = "complete")]
           public bool Complete { get; set; }
           [Version]
           public string Version { get; set; }
         }

7.  En MainPage.xaml.cs, actualice el controlador de eventos `OnNavigatedTo` para que inicialice el contexto de sincronización de cliente con un almacén SQLite. El almacén SQLite se crea con una tabla que coincide con el esquema de la tabla del servicio móvil pero debe contener la propiedad del sistema **Version** agregada en el paso anterior.

         protected async override void OnNavigatedTo(NavigationEventArgs e)
         {
             if (!App.MobileService.SyncContext.IsInitialized)
             {
                 var store = new MobileServiceSQLiteStore("localsync12.db");
                 store.DefineTable<TodoItem>();
                 await App.MobileService.SyncContext.InitializeAsync(store, new MobileServiceSyncHandler());
             }
             RefreshTodoItems();
         }

8.  En el Explorador de soluciones de Visual Studio, abra el archivo MainPage.xaml. Busque el elemento de cuadrícula que contiene StackPanel con el título **Query and Update Data**. Agregue el siguiente código de la interfaz de usuario que reemplaza los elementos de las definiciones de fila hasta la etiqueta de inicio de ListView.

    Este código agrega definiciones de fila y columna a la cuadrícula para distribuir los elementos. También agrega dos controles de botón con controladores de eventos de clic para operaciones de **inserción** y **extracción**. Los botones ocupan la posición inmediatamente encima del elemento `ListView` llamado ListItems. Guarde el archivo.

         <Grid.RowDefinitions>
             <RowDefinition Height="Auto" />
             <RowDefinition Height="Auto" />
             <RowDefinition Height="*" />
         </Grid.RowDefinitions>
         <Grid.ColumnDefinitions>
             <ColumnDefinition Width="Auto" />
             <ColumnDefinition Width="*" />
         </Grid.ColumnDefinitions>
         <StackPanel Grid.Row="0" Grid.ColumnSpan="2">
             <local:QuickStartTask Number="2" Title="Query, Update, and Synchronize Data" 
               Description="Use the Pull and Push buttons to synchronize the local store with the server" />
         </StackPanel>
         <Button Grid.Row="1" Grid.Column="0" Margin="5,5,0,0" Name="ButtonPush"
             Click="ButtonPush_Click" Width="80" Height="34">â¬† Push</Button>
         <Button Grid.Row="1" Grid.Column="1" Margin="5,5,0,0"  Name="ButtonPull" 
             Click="ButtonPull_Click" Width="80" Height="34">â¬‡ Pull</Button>
         <ListView Name="ListItems" SelectionMode="None" Margin="0,10,0,0" Grid.ColumnSpan="2" Grid.Row="2">

9.  En MainPage.xaml.cs, agregue los controladores de eventos de clic del botón para los botones **Push** y **Pull**, y guarde el archivo.

         private async void ButtonPull_Click(object sender, RoutedEventArgs e)
         {
             Exception pullException = null;
             try
             {
                 await todoTable.PullAsync();
                 RefreshTodoItems();
             }
             catch (Exception ex)
             {
                 pullException = ex;
             }
             if (pullException != null) {
                 MessageDialog d = new MessageDialog("Pull failed: " + pullException.Message +
                   "\n\nIf you are in an offline scenario, " + 
                   "try your Pull again when connected with your Mobile Serice.");
                 await d.ShowAsync();
             }
         }
         private async void ButtonPush_Click(object sender, RoutedEventArgs e)
         {
             string errorString = null;
             try
             {
                 await App.MobileService.SyncContext.PushAsync();
                 RefreshTodoItems();
             }
             catch (MobileServicePushFailedException ex)
             {
                 errorString = "Push failed because of sync errors: " + 
                   ex.PushResult.Errors.Count() + ", message: " + ex.Message;
             }
             catch (Exception ex)
             {
                 errorString = "Push failed: " + ex.Message;
             }
             if (errorString != null) {
                 MessageDialog d = new MessageDialog(errorString + 
                   "\n\nIf you are in an offline scenario, " + 
                   "try your Push again when connected with your Mobile Serice.");
                 await d.ShowAsync();
             }
         }

10. No ejecute todavía la aplicación. Presione la tecla **F7** para volver a compilar el proyecto. Compruebe que no se hayan producido errores de compilación.

Prueba de la aplicación en un escenario sin conexión
----------------------------------------------------

En esta sección, se interrumpe la conexión con el servicio móvil para simular un escenario sin conexión. A continuación, se agregan algunos elementos de datos que se guardarán en el almacén local.

Observe que en esta sección la aplicación no debe estar conectada a ningún servicio móvil. Por lo que, si se prueban, los botones **Push** y **Pull** lanzarán excepciones. En la siguiente sección, volverá a conectar la aplicación cliente al servicio móvil para probar las operaciones de **inserción** y **extracción** y sincronizar el almacén con la base de datos del servicio móvil.

1.  En el Explorador de soluciones de Visual Studio, abra App.xaml.cs. Cambie la inicialización de **MobileServiceClient** a una dirección no válida reemplazando "**azure-mobile.net**" por "**azure-mobile.xxx**" para su URL. A continuación, guarde el archivo.

          public static MobileServiceClient MobileService = new MobileServiceClient(
             "https://your-mobile-service.azure-mobile.xxx/",
             "AppKey"
         );

2.  En Visual Studio, presione **F5** para compilar y ejecutar la aplicación. Escriba un nuevo elemento todo y haga clic en **Save**. Los nuevos elementos todo solo están en el almacén local hasta que se puedan insertar en el servicio móvil. La aplicación cliente se comporta como si estuviera conectada al servicio móvil y admite todas las operaciones de creación, lectura, actualización y eliminación (CRUD).

    ![](./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-offline-app-run1.png)

3.  Cierre la aplicación y reiníciela para comprobar que los nuevos elementos que creó se mantienen en el almacén local.

Actualización de la aplicación para volver a conectar el servicio móvil
-----------------------------------------------------------------------

En esta sección se vuelve a conectar la aplicación al servicio móvil. De este modo se simula que la aplicación pasa de un estado sin conexión a un estado en línea con el servicio móvil.

1.  En el Explorador de soluciones de Visual Studio, abra App.xaml.cs. Vuelva a cambiar la inicialización de **MobileServiceClient** a la dirección correcta reemplazando "**azure-mobile.xxx**" por "**azure-mobile.net**" para su URL. A continuación, guarde el archivo.

          public static MobileServiceClient MobileService = new MobileServiceClient(
             "https://your-mobile-service.azure-mobile.net/",
             "Your AppKey"
         );

Prueba de la aplicación conectada al servicio móvil
---------------------------------------------------

En esta sección probará las operaciones de inserción y extracción para sincronizar el almacén local con la base de datos del servicio móvil.

1.  En Visual Studio, presione **F5** para volver a compilar y ejecutar la aplicación. Observe que los datos tienen el mismo aspecto que en el escenario sin conexión aunque la aplicación ahora está conectada con el servicio móvil. Esto se debe a que la aplicación siempre funciona con el elemento `IMobileServiceSyncTable` que apunta al almacén local.

    ![](./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run1.png)

2.  Inicie sesión en el Portal de administración de Azure y consulte la base de datos del servicio móvil. Si el servicio usa el back-end de JavaScript para servicios móviles, puede examinar los datos en la pestaña **Data** del servicio móvil. Si usa el back-end de .NET para el servicio móvil, puede hacer clic en el botón **Manage** de su base de datos en la extensión de SQL Azure para ejecutar una consulta en la tabla.

    Observe que los datos no se han sincronizado entre la base de datos y el almacén local.

    ![](./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png)

3.  En la aplicación, presione el botón **Push**. Esto hace que la aplicación llame a `MobileServiceClient.SyncContext.PushAsync` y luego a `RefreshTodoItems` para actualizar la aplicación con los elementos del almacén local. Esta operación de inserción se traduce en que la base de datos del servicio móvil recibe los datos del almacén. Sin embargo, el almacén local no recibe los elementos de la base de datos del servicio móvil.

    Una operación de inserción se ejecuta de `MobileServiceClient.SyncContext` y no de `IMobileServicesSyncTable`, e inserta cambios en todas las tablas asociadas a dicho contexto de sincronización. Esto tiene por objeto incluir escenarios en los que existen relaciones entre tablas.

    ![](./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png)

4.  En la aplicación, algunos elementos nuevos para el almacén local.

    ![](./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png)

5.  Esta vez, presione el botón **Pull** de la aplicación. La aplicación solo llama a `IMobileServiceSyncTable.PullAsync()` y a `RefreshTodoItems`. Observe que todos los datos de la base de datos del servicio móvil se extrajeron en el almacén local y se muestran en la aplicación. No obstante, observe también que todos los datos del almacén local seguían insertados en la base de datos del servicio móvil. Esto se debe a que una **extracción siempre realiza primero una inserción**.

    ![](./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run3.png)

    ![](./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png)

Resumen
-------

Para admitir las características sin conexión de servicios móviles, usamos la interfaz `IMobileServiceSyncTable` e inicializamos `MobileServiceClient.SyncContext` con un almacén local. En este caso, el almacén local era una base de datos SQLite.

Las operaciones CRUD normales para servicios móviles funcionan como si la aplicación siguiera conectada, pero todas las operaciones se producen en el almacén local.

Cuando queramos sincronizar el almacén local con el servidor, usaremos los métodos `IMobileServiceSyncTable.PullAsync` y `MobileServiceClient.SyncContext.PushAsync`.

-   Para insertar cambios en el servidor, llamamos a `IMobileServiceSyncContext.PushAsync()`. Este método es miembro de `IMobileServicesSyncContext` y no de la tabla de sincronización porque insertará cambios en todas las tablas:

    Solo los registros que se han modificado localmente de alguna forma (mediante operaciones CRUD) se enviarán al servidor.

-   Para extraer datos de una tabla del servidor en la aplicación, llamamos a `IMobileServiceSyncTable.PullAsync`.

    Una extracción siempre realiza primero una inserción.

    También existen sobrecargas de **PullAsync()** que permiten especificar una consulta. Observe que en la versión preliminar de compatibilidad sin conexión con Servicios móviles, **PullAsync** leerá todas las filas de la tabla (o consulta) correspondiente: por ejemplo, no intenta leer solo las filas posteriores a la última sincronización. Si las filas ya existen en la tabla de sincronización local, permanecerán intactas.

Pasos siguientes
----------------

-   [Control de conflictos con compatibilidad sin conexión para Servicios móviles](/en-us/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/)

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Test the app in an offline Scenario]: #test-offline-app
[Update the app to reconnect your mobile service]: #update-online-app
[Test the app connected to the Mobile Service]: #test-online-app
[Next Steps]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
[1]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
[2]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png
[3]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitepcl-nuget.png
[4]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-offline-app-run1.png
[5]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run1.png
[6]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png
[7]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png
[8]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png
[9]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run3.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png


<!-- URLs. -->
[Handling conflicts with offline support for Mobile Services]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/ 
[Getting Started Offline Sample]: http://go.microsoft.com/fwlink/?LinkId=394777
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Getting Started]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-windows-store-get-started/
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776


