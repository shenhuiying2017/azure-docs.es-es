<properties urlDisplayName="Using Offline Data" pageTitle="Uso de datos sin conexión en Servicios móviles (Tienda Windows) | Centro de desarrollo móvil" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Windows Store application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data sync in Mobile Services" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="wesmc" />

# Uso de la sincronización de datos sin conexión en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Este tutorial muestra cómo agregar compatibilidad sin conexión para una aplicación universal de la Tienda Windows con Servicios móviles de Azure. La compatibilidad sin conexión le permitirá interactuar con una base de datos local cuando la aplicación está en un escenario sin conexión. Una vez que la aplicación está en línea con la base de datos back-end, se sincronizan los cambios locales mediante las características sin conexión. 
</p>
<p>Si prefiere ver un vídeo, el clip que aparece a la derecha muestra los mismos pasos que este tutorial.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">Ver el tutorial</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir video</span></a> <span class="time">14:36:00</span></div>
</div>


En este tutorial, se actualizará el proyecto de aplicación universal del tutorial [Introducción a Servicios móviles] para admitir las características sin conexión de Servicios móviles de Azure. A continuación, agregará datos en un escenario sin conexión desconectado, sincronizará dichos elementos con la base de datos en línea e iniciará sesión en el Portal de administración de Azure para ver los cambios efectuados en los datos al ejecutar la aplicación.


>[WACOM.NOTE] Este tutorial está destinado a profundizar en el uso de Azure con Servicios móviles para almacenar y recuperar datos en una aplicación de la Tienda Windows. Si esta es la primera vez que usa los Servicios móviles, debe completar el tutorial [Introducción a Servicios móviles] primero.
>
>Necesita una cuenta de Azure para completar este tutorial. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 servicios móviles gratuitos que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Evaluación gratuita de Azure</a>. 
>
>El tutorial anterior de Windows Phone 8 para Visual Studio 2012 sigue estando disponible aquí, [Tutorial de Windows Phone 8 para Visual Studio 2012].


Este tutorial le guiará a través de estos pasos básicos:

1. [Actualización de la aplicación para que admita características sin conexión]
2. [Prueba de la aplicación en un escenario sin conexión] 
3. [Actualización de la aplicación para volver a conectar el servicio móvil]
4. [Prueba de la aplicación conectada al servicio móvil]

Este tutorial requiere lo siguiente:

* Visual Studio 2013 en Windows 8.1.
* Finalización del tutorial [Introducción a Servicios móviles].
* [SDK de Servicios móviles de Azure versión 1.3.0-beta2 (o posterior)][Mobile Services SDK Nuget]
* [Almacén SQLite de Servicios móviles de Azure versión 1.0.0-beta2 (o posterior)][SQLite store nuget]
* SQLite para Windows 8.1

>[AZURE.NOTE]Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Evaluación gratuita de Azure</a>. 

## <a name="enable-offline-app"></a>Actualización de la aplicación para que admita características sin conexión

Las características sin conexión de Servicios móviles de Azure permiten interactuar con una base de datos local cuando el usuario se encuentra en un escenario sin conexión con su servicio móvil. Para usar estas características en la aplicación, inicialice `MobileServiceClient.SyncContext` en un almacén local. A continuación, obtenga una referencia a la tabla mediante la interfaz `IMobileServiceSyncTable`. En este tutorial se usa SQLite como el almacén local.

>[AZURE.NOTE] Puede omitir esta sección y solo obtener el proyecto de ejemplo que ya tiene compatibilidad sin conexión desde el repositorio de muestras de GitHub para Servicios móviles. El proyecto de ejemplo con compatibilidad sin conexión habilitada está ubicado aquí, [muestra sin conexión TodoList].


1. Instale SQLite en tiempo de ejecución para Windows 8.1 y Windows Phone 8.1. 

    * **Windows 8.1 en tiempo de ejecución:** Instale SQLite en tiempo de ejecución para Windows 8.1 desde este vínculo, [SQLite para Windows 8.1].
    * **Windows Phone 8,1:** Instale SQLite en tiempo de ejecución para Windows Phone 8.1 desde este vínculo, [SQLite para Windows Phone 8.1].

    >[AZURE.NOTE] Si utiliza Internet Explorer, es posible que al hacer clic en el vínculo para instalar SQLite se le solicite que descargue .vsix como archivo .zip. Guarde el archivo en una ubicación del disco duro con la extensión .vsix en lugar de .zip. A continuación, haga doble clic en el archivo .vsix en el Explorador de Windows para ejecutar la instalación.

2. En Visual Studio, abra el proyecto que completó en el tutorial [Introducción a Servicios móviles]. En el Explorador de soluciones, haga clic con el botón secundario en **Referencias** para los proyectos de plataforma de Windows Phone 8.1 y Windows 8.1 en tiempo de ejecución y agregue una referencia a SQLite, que está ubicado en la sección **Extensiones**. 

    ![][1]
    </br>**Windows 8.1 en tiempo de ejecución**

    ![][11]
    </br>**Windows Phone 8,1**

3. SQL en tiempo de ejecución requiere que se cambie la arquitectura de procesador del proyecto que se compila a **x86**, **x64** o **ARM**. No se admite **cualquier CPU** . En el Explorador de soluciones de Visual Studio, haga clic en Solución que aparece en la parte superior, luego cambie el cuadro desplegable de la arquitectura del procesador a uno de la configuración compatible que desea probar.

    ![][13]

4. Instale el paquete NuGet **WindowsAzure.MobileServices.SQLiteStore** para proyectos de Windows 8.1 en tiempo de ejecución y Windows Phone 8.1.

    * **Windows 8.1:** En el Explorador de soluciones, haga clic con el botón secundario en el proyecto de Windows 8.1 y haga clic en **Administrar paquetes de NuGet** para ejecutar el administrador de paquetes de NuGet. Busque **SQLiteStore** para instalar el paquete WindowsAzure.MobileServices.SQLiteStore.
    * **Windows Phone 8,1:** Haga clic con el botón secundario en el proyecto de Windows Phone 8.1 y, a continuación, haga clic en **Administrar paquetes de NuGet** para ejecutar el administrador de paquetes de NuGet. Busque **SQLiteStore** para instalar el paquete WindowsAzure.MobileServices.SQLiteStore.     

    >[WACOM.NOTE] Si la instalación crea una referencia a una versión anterior de SQLite, puede simplemente eliminar esa referencia duplicada. 

    ![][2]

5. En el Explorador de soluciones para Visual Studio, en el proyecto compartido, abra el archivo MainPage.cs. Agregue las siguientes instrucciones using en la parte superior del archivo.

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;

6. En el archivo compartido, Mainpage.cs, reemplace la declaración de `todoTable` por una declaración de tipo `IMobileServicesSyncTable` que se inicializa llamando a `MobileServicesClient.GetSyncTable()`.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();


7. En el archivo compartido, MainPage.cs, actualice el controlador de eventos `OnNavigatedTo` para que inicialice el contexto de sincronización de cliente con un almacén SQLite. El almacén SQLite se crea con una tabla que coincide con el esquema de la tabla del servicio móvil y debe incluir la propiedad del sistema **Version** que agregará en el paso siguiente.

        protected async override void OnNavigatedTo(NavigationEventArgs e)
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localsync.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store);
            }
            await RefreshTodoItems();
        }


8. En el Explorador de soluciones para Visual Studio, en el proyecto compartido, expanda **DataModel** y abra TodoItem.cs. Agregue una instrucción `using` para el espacio de nombres MobileServices. Esto es necesario para resolver el atributo de versión para la propiedad de sistema de versión.

        using Microsoft.WindowsAzure.MobileServices;

      Luego actualice la clase `TodoItem` para que incluya la propiedad del sistema **Version** de la manera siguiente. El esquema de tabla debe incluir la propiedad del sistema **Version**, como se muestra aquí para admitir las características sin conexión.

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




9. A continuación, actualice la interfaz de usuario para los proyectos de Windows 8.1 y Windows Phone 8.1 a fin de incluir botones que admitirán las operaciones de sincronización sin conexión entre la base de datos local sin conexión y la base de datos de Servicios móviles en Azure. 

    * **Windows 8.1:** En el Explorador de soluciones para Visual Studio, en el proyecto de Windows 8.1, abra MainPage.xaml. Encuentre el botón llamado **ButtonRefresh**. Reemplace ese elemento de botón por el siguiente panel StackPanel de botones. 

            <StackPanel Orientation="Horizontal">
                <Button Margin="72,0,0,0" Height="44" Name="ButtonRefresh" Click="ButtonRefresh_Click">
                	Refresh
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPull" Click="ButtonPull_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Sync"/>
                    </StackPanel>
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPush" Click="ButtonPush_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
            </StackPanel>

    * **Windows Phone 8,1:** En el Explorador de soluciones para Visual Studio, en el proyecto de Windows Phone 8.1, abra MainPage.xaml. Encuentre el botón llamado **ButtonRefresh**. Reemplace ese elemento de botón por el siguiente panel StackPanel de botones. 

            <StackPanel Orientation="Horizontal" Grid.Row="3" Grid.ColumnSpan="2">
                <Button Name="ButtonRefresh" Click="ButtonRefresh_Click" Margin="10,0,0,0">
                    Refresh
                </Button>
                <Button Name="ButtonPull" Click="ButtonPull_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Sync"/>
                    </StackPanel>
                </Button>
                <Button Name="ButtonPush" Click="ButtonPush_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
        </StackPanel>
        


10. En el archivo MainPage.cs compartido, agregue controladores de eventos de clic para los botones de **inserción** y **extracción**. A continuación, guarde el archivo. 
        
    Observe que la `MobileServicePushFailedException` puede producirse para una operación de inserción y una operación de extracción. Puede producirse para una extracción debido a que la operación de extracción ejecuta de manera interna una inserción para asegurarse de que todas las tablas en cualquier relación están en sincronía. En el siguiente tutorial, [Control de conflictos con compatibilidad sin conexión para Servicios móviles], muestra cómo manera estas excepciones relacionadas de sincronización.

    Para admitir la sincronización de los registros eliminados con la sincronización de datos sin conexión, debe habilitar [Eliminación temporal](/es-es/documentation/articles/mobile-services-using-soft-delete/). De lo contrario, tendrá que eliminar manualmente registros del almacén local o llamar a `IMobileServiceSyncTable::PurgeAsync()` para purgar el almacén local.

    En este ejemplo, se pasa una consulta a la llamada al método `PullAsync` para admitir la sincronización incremental. Esto resulta útil en casos donde realmente no desea el gasto que implica extraer toda la tabla durante la sincronización. En este escenario no nos preocupan los cambios de texto en los todoitems, solo queremos extraer elementos completados para marcarlos como fuera de nuestra todolist.

        private async void ButtonPull_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            string errorString = null;

            // Prevent extra clicking while Pull is in progress
            // and visibly show the action is in progress. 
            ButtonPull.Focus(FocusState.Programmatic);
            ButtonPull.IsEnabled = false;

            try
            {
                // In this example scenario we are demonstrating incremental sync. We only want to 
                // sync the uncomplete todoitems. If another client updated the text or any other 
                // field after completing the item, it doesn't concern us. So we pass a query to the 
                // PullAsync() method to pull uncomplete todoitems instead of the entire table.
                await todoTable.PullAsync(todoTable.Where(todoItem => todoItem.Complete == false));

                await RefreshTodoItems();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Internal Push operation during pull request failed because of sync errors: " +
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Pull failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Pull again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }

            ButtonPull.IsEnabled = true; 
        }


        private async void ButtonPush_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            string errorString = null;

            // Prevent extra clicking while Push is in progress
            // and visibly show the action is in progress. 
            ButtonPush.Focus(FocusState.Programmatic);
            ButtonPush.IsEnabled = false;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " + 
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Push failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Push again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }

            ButtonPush.IsEnabled = true;
        }

11. Compile la solución y compruebe que no se produjeron errores de compilación en ninguno de los proyectos.


## <a name="test-offline-app"></a>Prueba de la aplicación en un escenario sin conexión

En esta sección, se interrumpe la conexión con el servicio móvil para simular un escenario sin conexión. A continuación, se agregan algunos elementos de datos que se guardarán en el almacén local.

Observe que en esta sección la aplicación debe estar conectado a ningún servicio móvil. Por lo que, si se prueban, los botones **Push** y **Pull** lanzarán excepciones. En la siguiente sección, volverá a conectar la aplicación cliente al servicio móvil para probar las operaciones de **inserción** y **extracción** y sincronizar el almacén con la base de datos del servicio móvil.


1. En el Explorador de soluciones para Visual Studio, abra App.xaml.cs en el proyecto compartido. Cambie la inicialización de **MobileServiceClient** a una dirección no válida reemplazando "**azure-mobile.net**" por "**azure-mobile.xxx**" en la dirección URL. A continuación, guarde el archivo.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2. En Visual Studio, presione **F5** para compilar y ejecutar la aplicación. Escriba algunos todoitems nuevos y haga clic en **Guardar** para cada uno de ellos. Los nuevos elementos todo solo están en el almacén local hasta que se puedan insertar en el servicio móvil. La aplicación cliente se comporta como si estuviera conectada al servicio móvil y admite todas las operaciones de creación, lectura, actualización y eliminación (CRUD).

    ![][4]

3. Cierre la aplicación y reiníciela para comprobar que los nuevos elementos que creó se mantienen en el almacén local.

## <a name="update-online-app"></a>Actualización de la aplicación para volver a conectar el servicio móvil

En esta sección se vuelve a conectar la aplicación al servicio móvil. De este modo se simula que la aplicación pasa de un estado sin conexión a un estado en línea con el servicio móvil.


1. En el Explorador de soluciones para Visual Studio, abra App.xaml.cs en el proyecto compartido. Vuelva a cambiar la inicialización de **MobileServiceClient** a la dirección correcta reemplazando "**azure-mobile.xxx**" por "**azure-mobile.net**" en la dirección URL. A continuación, guarde el archivo.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );


## <a name="test-online-app"></a>Prueba de la aplicación conectada al servicio móvil


En esta sección probará las operaciones de inserción y extracción para sincronizar el almacén local con la base de datos del servicio móvil. Puede probar el cliente de Tienda Windows 8.1 o el cliente de Windows Phone 8.1. Las capturas de pantalla que aparecen a continuación muestran el cliente de Tienda Windows 8.1. 

1. En Visual Studio, presione la tecla **F5** para volver a compilar y ejecutar la aplicación. Observe que los datos tienen el mismo aspecto que en el escenario sin conexión aunque la aplicación ahora está conectada con el servicio móvil. Esto se debe a que esta aplicación siempre funciona con `IMobileServiceSyncTable` que apunta al almacén local.

    ![][4]

2. Inicie sesión en el Portal de administración de Azure y consulte la base de datos del servicio móvil. Si el servicio usa el back-end de JavaScript para servicios móviles, puede examinar los datos en la pestaña **Data** del servicio móvil. Si usa el back-end de .NET para el servicio móvil, puede hacer clic en el botón **Administrar** de su base de datos en la extensión de SQL Azure para ejecutar una consulta en la tabla.

    Observe que los datos no se han sincronizado entre la base de datos y el almacén local.

    ![][6]

3. En la aplicación, presione el botón  **Inserción**. Esto hace que la aplicación llame a `MobileServiceClient.SyncContext.PushAsync`. Esta operación de inserción se traduce en que la base de datos del servicio móvil recibe los datos del almacén. Sin embargo, el almacén local no recibe los elementos de la base de datos del servicio móvil.

    Una operación de inserción se ejecuta de `MobileServiceClient.SyncContext` y no de `IMobileServicesSyncTable`, e inserta cambios en todas las tablas asociadas a dicho contexto de sincronización. Esto tiene por objeto incluir escenarios en los que existen relaciones entre tablas.

    ![][7]

4. En la aplicación, haga clic en la casilla situada junto a algunos elementos para completarlos en el almacén local. 

    ![][8]

5. Esta vez, presione el botón **Extracción** de la aplicación. La aplicación llama a `IMobileServiceSyncTable.PullAsync()` y `RefreshTodoItems`.  Observe que todos los datos de la base de datos del servicio móvil se extrajeron en el almacén local y se muestran en la aplicación. No obstante, observe también que todos los datos del almacén local seguían insertados en la base de datos del servicio móvil. Esto se debe a que una **extracción siempre realiza primero una inserción**. Esto es para asegurarse de que todas las tablas en el almacén local con relaciones estén en sincronía.
 
    ![][9]

    ![][10] 
  

##Resumen

Para admitir las características sin conexión de servicios móviles, usamos la interfaz `IMobileServiceSyncTable` e inicializamos `MobileServiceClient.SyncContext` con un almacén local. En este caso, el almacén local era una base de datos SQLite.

Las operaciones CRUD normales para servicios móviles funcionan como si la aplicación siguiera conectada, pero todas las operaciones se producen en el almacén local.

Cuando queramos sincronizar el almacén local con el servidor, usaremos los métodos `IMobileServiceSyncTable.PullAsync` y `MobileServiceClient.SyncContext.PushAsync`.

*  Para insertar cambios en el servidor, llamamos a `IMobileServiceSyncContext.PushAsync()`. Este método es miembro de `IMobileServicesSyncContext` y no de la tabla de sincronización porque insertará cambios en todas las tablas:

    Solo los registros que se han modificado localmente de alguna forma (mediante operaciones CRUD) se enviarán al servidor.
   
* Para extraer datos de una tabla del servidor en la aplicación, llamamos a `IMobileServiceSyncTable.PullAsync`.

    Una extracción siempre realiza primero una inserción. Esto es para asegurarse de que todas las tablas en el almacén local con relaciones estén en sincronía.

    También existen sobrecargas de `PullAsync()` que permiten especificar una consulta para admitir sincronización incremental. Si no se transmite una consulta, `PullAsync()` extraerá todas las filas de la tabla (o consulta) correspondiente. Puede transmitir la consulta para filtrar solo los cambios con los que su aplicación debe sincronizarse.


## Pasos siguientes

* [Control de conflictos con compatibilidad sin conexión para Servicios móviles]

<!-- Anchors. -->
[Actualización de la aplicación para que admita características sin conexión]: #enable-offline-app
[Prueba de la aplicación en un escenario sin conexión]: #test-offline-app
[Actualización de la aplicación para volver a conectar el servicio móvil]: #update-online-app
[Prueba de la aplicación conectada al servicio móvil]: #test-online-app
[Pasos siguientes]:#next-steps

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
[11]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-wp81-reference-sqlite-dialog.png
[12]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/new-synchandler-class.png
[13]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Control de conflictos con compatibilidad sin conexión para Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/ 
[Muestra sin conexión de TodoList]: http://go.microsoft.com/fwlink/?LinkId=394777
[Introducción a Servicios móviles]: /es-es/develop/mobile/tutorials/get-started/#create-new-service
[Tareas iniciales]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Introducción a Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-store-get-started/
[SQLite para Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite para Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953
[Tutorial de Windows Phone 8 para Visual Studio 2012]: /es-es/documentation/articles/mobile-services-windows-phone-get-started-offline-data/


[NuGet de SDK de Servicios móviles]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-beta2
[NuGet de almacén de SQLite]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-beta2
