<properties 
	pageTitle="Uso de datos sin conexión en Servicios móviles (Windows Phone) | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo usar Servicios móviles de Azure con datos sin conexión sincronizados en su aplicación de Windows Phone" 
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
	ms.date="12/10/2014" 
	ms.author="wesmc"/>

# Uso de la sincronización de datos sin conexión en servicios móviles

[AZURE.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


En este tema se muestra cómo usar las capacidades sin conexión de Servicios móviles de Azure. Las características sin conexión de Servicios móviles de Azure permiten interactuar con una base de datos local cuando el usuario se encuentra en un escenario sin conexión con su servicio móvil. Las características sin conexión le permiten sincronizar los cambios locales con el servicio móvil cuando vuelva a estar en línea. 

En este tutorial, la aplicación se actualizará desde el tutorial [Introducción a los datos] para permitir las características sin conexión de Servicios móviles de Azure. A continuación, agregará datos en un escenario sin conexión desconectado, sincronizará dichos elementos con la base de datos en línea e iniciará sesión en el Portal de administración de Azure para ver los cambios efectuados en los datos al ejecutar la aplicación.


>[AZURE.NOTE] Este tutorial está destinado a profundizar en el uso de Azure con los Servicios móviles para almacenar y recuperar datos en una aplicación de Windows Phone. Si esta es la primera vez que usa Servicios móviles, considere la posibilidad de completar antes el tutorial [Introducción a Servicios móviles] y el tutorial [Introducción a los datos]. 

Este tutorial le guiará a través de estos pasos básicos:

1. [Actualización de la aplicación para que admita características sin conexión]
2. [Prueba de la aplicación en un escenario sin conexión] 
3. [Actualización de la aplicación para volver a conectar el servicio móvil]
4. [Prueba de la aplicación conectada al servicio móvil]

Este tutorial requiere lo siguiente:

* Visual Studio 2012
* [SDK de Windows Phone 8]
* Finalización del tutorial [Introducción a los datos].
* [SDK de Servicios móviles de Azure versión 1.3.0 (o posterior)][NuGet del SDK de Servicios móviles]
* [Almacén de SQLite de Servicios móviles de Azure versión 1.0.0 (o posterior)][NuGet del almacén de SQLite]
* [SQLite para Windows Phone 8]

>[AZURE.NOTE] Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Evaluación gratuita de Azure</a>. 

## <a name="enable-offline-app"></a>Actualización de la aplicación para que admita características sin conexión

Las características sin conexión de Servicios móviles de Azure permiten interactuar con una base de datos local cuando el usuario se encuentra en un escenario sin conexión con su servicio móvil. Para usar estas características en la aplicación, inicialice  `MobileServiceClient.SyncContext` en un almacén local. A continuación, obtenga una referencia a la tabla mediante la interfaz  `IMobileServiceSyncTable`.

En esta sección se utiliza SQLite como almacén local de las características sin conexión.

>[AZURE.NOTE] Puede omitir esta sección y simplemente descargar una versión del proyecto de introducción que ya permite la funcionalidad sin conexión.  Para descargar un proyecto que admita la característica sin conexión, consulte [Introducción a ejemplos sin conexión para Windows Phone].


1. Instale SQLite para proyectos de Windows Phone 8. Puede instalarlo desde este vínculo, [SQLite para Windows Phone 8].

    >[AZURE.NOTE] Si utiliza Internet Explorer, es posible que al hacer clic en el vínculo para instalar SQLite se le solicite descargar .vsix como archivo .zip. Guarde el archivo en una ubicación del disco duro con la extensión .vsix en lugar de .zip. A continuación, haga doble clic en el archivo .vsix en el Explorador de Windows para ejecutar la instalación.

2. En Visual Studio, abra el proyecto que finalizó en el tutorial [Introducción a los Servicios móviles] o [Introducción a los datos]. En el Explorador de soluciones, haga clic con el botón derecho en **Referencias** en el proyecto y agregue una referencia a **SQLite para Windows Phone** en **Windows Phone**>**Extensiones**. 

    ![][1]

3. El tiempo de ejecución de SQLite requiere cambiar la arquitectura del proyecto que se va a crear a **x86**, **x64** o **ARM**. **No se admite cualquier CPU**. Cambie la arquitectura del procesador por una de las configuraciones admitidas que quiere probar.

    ![][11]

4. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto de la aplicación cliente y haga clic en **Administrar paquetes de NuGet** para ejecutar el Administrador de paquetes NuGet. Busque **SQLiteStore** para instalar el paquete **MicrosoftAzure.MobileServices.SQLiteStore**.

    ![][2]

5. En el Explorador de soluciones de Visual Studio, abra el archivo MainPage.xaml.cs. Agregue las siguientes instrucciones using en la parte superior del archivo:

        using Microsoft.MicrosoftAzure.MobileServices.SQLiteStore;
        using Microsoft.MicrosoftAzure.MobileServices.Sync;
        using Newtonsoft.Json.Linq;

6. En Mainpage.xaml.cs, reemplace la declaración de  `todoTable` por una declaración de tipo  `IMobileServicesSyncTable` que se inicializa llamando a  `MobileServicesClient.GetSyncTable()`.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();

7. En MainPage.xaml.cs, actualice la clase  `TodoItem` para que incluya la propiedad del sistema **Version** de la forma siguiente.

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


8. En MainPage.xaml.cs, actualice el controlador de eventos  `OnNavigatedTo` para que sea un método  `async` e inicialice el contexto de sincronización de cliente con un almacén SQLite. El almacén SQLite se crea con una tabla que coincide con el esquema de la tabla del servicio móvil pero debe contener la propiedad del sistema **Version** agregada en el paso anterior.

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

9. En el Explorador de soluciones de Visual Studio, abra el archivo MainPage.xaml. Busque la definición del botón para el botón **Actualizar**. Sustitúyalo por la siguiente definición del panel de pila. 

    Este código agrega dos controles de botón con controladores de eventos de clic para operaciones de **inserción** y **extracción**. Los botones están en horizontal donde se encuentra el botón Actualizar. Guarde el archivo.

        <StackPanel  Orientation="Horizontal" Grid.Row="3" Grid.ColumnSpan="2" HorizontalAlignment="Center">
          <Button Name="ButtonRefresh" Click="ButtonRefresh_Click" Width="160">Refresh</Button>
          <Button Name="ButtonPush" Click="ButtonPush_Click" Width="160">Push</Button>
          <Button Name="ButtonPull" Click="ButtonPull_Click" Width="160">Pull</Button>
        </StackPanel>

    Además, cambie el texto de los bloques de texto para que coincida con la siguiente captura de pantalla.

    ![][12]
        


10. En MainPage.xaml.cs, agregue los controladores de eventos de clic del botón para los botones de **inserción** y **extracción** y guarde el archivo.

        private async void ButtonPull_Click(object sender, RoutedEventArgs e)
        {
            Exception pullException = null;
            try
            {
                await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); // first param is query ID, used for incremental sync
                RefreshTodoItems();
            }
            catch (Exception ex)
            {
                pullException = ex;
            }
            if (pullException != null) {
                MessageBox.Show("Pull failed: " + pullException.Message +
                  "\n\nIf you are in an offline scenario, " + 
                  "try your Pull again when connected with your Mobile Serice.");
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
                MessageBox.Show(errorString + 
                  "\n\nIf you are in an offline scenario, " + 
                  "try your Push again when connected with your Mobile Serice.");
            }
        }

11. No ejecute todavía la aplicación. Presione la tecla **F7** para volver a compilar el proyecto. Compruebe que no se hayan producido errores de compilación.

## <a name="test-offline-app"></a>Prueba de la aplicación en un escenario sin conexión

En esta sección, se interrumpe la conexión con el servicio móvil para simular un escenario sin conexión. A continuación, se agregan algunos elementos de datos que se guardarán en el almacén local.

Observe que en esta sección la aplicación no debe estar conectada a ningún servicio móvil. De este modo, los botones **Inserción** y **Extracción** producirán excepciones al probarlos. En la siguiente sección, volverá a conectar la aplicación cliente al servicio móvil para probar las operaciones de **Inserción** y **Extracción** con el fin de sincronizar el almacén con la base de datos del servicio móvil.


1. En el Explorador de soluciones de Visual Studio, abra App.xaml.cs. Cambie la inicialización de **MobileServiceClient** con una dirección no válida. Para ello, reemplace "**azure-mobile.net**" con "**azure-mobile.xxx**" para la dirección URL. A continuación, guarde el archivo.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2. En Visual Studio, presione **F5** para compilar y ejecutar la aplicación. Escriba un nuevo elemento todo y haga clic en **Guardar**. Los nuevos elementos todo solo están en el almacén local hasta que se puedan insertar en el servicio móvil. La aplicación cliente se comporta como si estuviera conectada al servicio móvil y admite todas las operaciones de creación, lectura, actualización y eliminación (CRUD).

    ![][4]

3. Cierre la aplicación y reiníciela para comprobar que los nuevos elementos que creó se mantienen en el almacén local.

## <a name="update-online-app"></a>Actualización de la aplicación para volver a conectar el servicio móvil

En esta sección se vuelve a conectar la aplicación al servicio móvil. De este modo se simula que la aplicación pasa de un estado sin conexión a un estado en línea con el servicio móvil.


1. En el Explorador de soluciones de Visual Studio, abra App.xaml.cs. Cambie la inicialización de **MobileServiceClient** de nuevo a la dirección correcta. Para ello, reemplace "**azure-mobile.xxx**" con "**azure-mobile.net**" para la dirección URL. A continuación, guarde el archivo.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );


## <a name="test-online-app"></a>Prueba de la aplicación conectada al servicio móvil


En esta sección probará las operaciones de inserción y extracción para sincronizar el almacén local con la base de datos del servicio móvil.

1. En Visual Studio, presione la tecla **F5** para volver a realizar la compilación y ejecute la aplicación. Observe que los datos tienen el mismo aspecto que en el escenario sin conexión aunque la aplicación ahora está conectada con el servicio móvil. Esto se debe a que la aplicación siempre funciona con el elemento  `IMobileServiceSyncTable` que apunta al almacén local.

    ![][4]

2.  Inicie sesión en el Portal de administración de Azure y consulte la base de datos del servicio móvil. Si el servicio usa el back-end de JavaScript para servicios móviles, puede examinar los datos en la pestaña **Data** del servicio móvil. 

    Si usa el back-end de .NET para el servicio móvil, en Visual Studio, vaya a **Explorador de servidores** -> **Azure** -> **Bases de datos SQL**. Haga clic con el botón derecho en la base de datos y seleccione **Abrir en el Explorador de objetos de SQL Server**.

    Observe que los datos no se han sincronizado entre la base de datos y el almacén local.

    ![][6]

3. En la aplicación, presione el botón **Inserción**. Esto hace que la aplicación llame a  `MobileServiceClient.SyncContext.PushAsync` y luego a  `RefreshTodoItems` para actualizar la aplicación con los elementos del almacén local. Esta operación de inserción se traduce en que la base de datos del servicio móvil recibe los datos del almacén. Sin embargo, el almacén local no recibe los elementos de la base de datos del servicio móvil.

    Una operación de inserción se ejecuta de  `MobileServiceClient.SyncContext` y no de  `IMobileServicesSyncTable`, e inserta cambios en todas las tablas asociadas a dicho contexto de sincronización. Esto tiene por objeto incluir escenarios en los que existen relaciones entre tablas.

    ![][7]

4. En la aplicación, algunos elementos nuevos para el almacén local.

    ![][8]

5. Esta vez, presione el botón **Extracción** de la aplicación. La aplicación solo llama a  `IMobileServiceSyncTable.PullAsync()` y  `RefreshTodoItems`.  Observe que todos los datos de la base de datos del servicio móvil se extrajeron en el almacén local y se muestran en la aplicación. No obstante, observe también que todos los datos del almacén local seguían insertados en la base de datos del servicio móvil. Esto se debe a que toda **extracción siempre realiza primero una inserción**.
 
    En este ejemplo, recuperamos todos los registros en el  `todoTable` remoto, pero también es posible filtrar registros pasando una consulta. El primer parámetro para  `PullAsync` es un identificador de consulta que se utiliza en la sincronización incremental, que utiliza la marca de tiempo  `UpdatedAt` para obtener únicamente los registros modificados desde la última sincronización. El identificador de la consulta debe ser una cadena descriptiva que sea única para cada consulta lógica en la aplicación. Para la desactivación de la sincronización incremental, pase  `null` como identificador de la consulta. Así se recuperarán todos los registros de cada operación de extracción, lo cual es potencialmente ineficaz.

    >[AZURE.NOTE] Para admitir la sincronización de los registros eliminados con la sincronización de datos sin conexión, debe habilitar la [Eliminación temporal]. De lo contrario, es necesario llamar a `IMobileServiceSyncTable.PurgeAsync()` para purgar el almacén local.

 
    ![][9]

    ![][10] 
  

## Resumen

## Resumen

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../includes/mobile-services-offline-summary-csharp.md)]

## Pasos siguientes

* [Control de conflictos con compatibilidad sin conexión para Servicios móviles]

* [Uso de la eliminación temporal en Servicios móviles][Eliminación temporal]

<!-- Anchors. -->
[Actualización de la aplicación para que admita características sin conexión]: #enable-offline-app
[Prueba de la aplicación en un escenario sin conexión]: #test-offline-app
[Actualización de la aplicación para volver a conectar el servicio móvil]: #update-online-app
[Prueba de la aplicación conectada al servicio móvil]: #test-online-app
[Pasos siguientes]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-phone-get-started-data-vs2013/mobile-todoitem-data-browse.png
[1]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
[2]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-sqlitestore-nuget.png
[3]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-sqlitepcl-nuget.png
[4]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-offline-app-run1.png
[5]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-online-app-run1.png
[6]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse.png
[7]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse2.png
[8]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-online-app-run2.png
[9]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-online-app-run3.png
[10]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse3.png
[11]: ./media/mobile-services-windows-phone-get-started-offline-data/vs-select-processor-architecture.png
[12]: ./media/mobile-services-windows-phone-get-started-offline-data/ui-screenshot.png

<!-- URLs. -->
[Control de conflictos con compatibilidad sin conexión para servicios móviles]: /es-es/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data/ 
[Introducción a ejemplos sin conexión para Windows Phone]: http://go.microsoft.com/fwlink/?LinkId=397952
[Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-phone-get-started/
[Introducción a Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-phone-get-started/
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-windows-phone-get-started-data/
[SQLite para Windows Phone 8]: http://go.microsoft.com/fwlink/?LinkId=397953
[SDK de Windows Phone 8]: http://go.microsoft.com/fwlink/p/?linkid=268374
[Eliminación temporal]: /es-es/documentation/articles/mobile-services-using-soft-delete/

[Nuget del SDK de servicios móviles]: http://www.nuget.org/packages/MicrosoftAzure.MobileServices/1.3.0
[Nuget del almacén de SQLite]: http://www.nuget.org/packages/MicrosoftAzure.MobileServices.SQLiteStore/1.0.0


<!--HONumber=42-->
