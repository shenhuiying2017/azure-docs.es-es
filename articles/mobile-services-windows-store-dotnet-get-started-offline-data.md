<properties linkid="develop-mobile-tutorials-get-started-offline-data-dotnet" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Windows Store application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data sync in Mobile Services" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Introducción a la sincronización de datos sin conexión en Servicios móviles

<div class="dev-center-tutorial-selector sublanding">
<a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="C# para Tienda Windows" class="current">C# para Tienda Windows</a>
<a href="/es-es/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/es-es/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS">iOS</a>
<a href="/es-es/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/es-es/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>En este tutorial se muestra c&oacute;mo agregar compatibilidad sin conexi&oacute;n a una aplicaci&oacute;n de la Tienda Windows mediante Servicios m&oacute;viles de Azure, lo que permite interactuar con una base de datos local cuando el usuario se encuentra en un escenario sin conexi&oacute;n. Las caracter&iacute;sticas le permiten sincronizar los cambios locales con el servicio m&oacute;vil cuando vuelva a estar en l&iacute;nea. 
</p>
<p>Si prefiere ver un v&iacute;deo, el clip que aparece a la derecha muestra los mismos pasos que este tutorial.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">Ver el tutorial (en ingl&eacute;s)</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir v&iacute;deo (en ingl&eacute;s)</span></a> <span class="time">14:36:00</span></div>

</div>

En este tutorial, actualizará la aplicación desde el tutorial [Introducción a los Servicios móviles][Introducción a los Servicios móviles] o [Introducción a los datos][Introducción a los datos] para que admita las características sin conexión de Servicios móviles de Azure. A continuación, agregará datos en un escenario sin conexión desconectado, sincronizará dichos elementos con la base de datos en línea e iniciará sesión en el Portal de administración de Azure para ver los cambios efectuados en los datos al ejecutar la aplicación.

> [WACOM.NOTE] Este tutorial está destinado a profundizar en el uso de Azure con los Servicios móviles para almacenar y recuperar datos en una aplicación de la Tienda Windows. Para ello, en este tema se recorren muchos de los pasos que se completan automáticamente en el inicio rápido de Servicios móviles. Si esta es la primera vez que usa los Servicios móviles, considere la posibilidad de completar antes el tutorial [Introducción a los Servicios móviles][Introducción a los Servicios móviles].
>
> Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][Evaluación gratuita de Azure].

Este tutorial le guiará a través de estos pasos básicos:

1.  [Actualización de la aplicación para que admita características sin conexión][Actualización de la aplicación para que admita características sin conexión]
2.  [Prueba de la aplicación en un escenario sin conexión][Prueba de la aplicación en un escenario sin conexión]
3.  [Actualización de la aplicación para volver a conectar el servicio móvil][Actualización de la aplicación para volver a conectar el servicio móvil]
4.  [Prueba de la aplicación conectada al servicio móvil][Prueba de la aplicación conectada al servicio móvil]

Este tutorial requiere lo siguiente:

-   Visual Studio 2013 en Windows 8.1.
-   Finalización del tutorial [Introducción a los Servicios móviles][Introducción a los Servicios móviles] o [Introducción a los datos][Introducción a los datos].
-   [SDK de Servicios móviles de Azure versión 1.3.0-beta2 (o posterior)][SDK de Servicios móviles de Azure versión 1.3.0-beta2 (o posterior)]
-   [SDK de Servicios móviles de Azure SQLite Store versión 1.0.0-beta2 (o posterior)][SDK de Servicios móviles de Azure SQLite Store versión 1.0.0-beta2 (o posterior)]
-   SQLite para Windows 8.1

> [WACOM.NOTE] Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][Evaluación gratuita de Azure].

## <a name="enable-offline-app"></a>Actualización de la aplicación para que admita características sin conexión

Las características sin conexión de Servicios móviles de Azure permiten interactuar con una base de datos local cuando el usuario se encuentra en un escenario sin conexión con su servicio móvil. Para usar estas características en la aplicación, inicialice `MobileServiceClient.SyncContext` en un almacén local. A continuación, obtenga una referencia a la tabla mediante la interfaz `IMobileServiceSyncTable`.

En esta sección se utiliza SQLite como almacén local de las características sin conexión.

> [WACOM.NOTE] Puede omitir esta sección y simplemente descargar una versión del proyecto de introducción que ya permite la funcionalidad sin conexión. Para descargar un proyecto que admita la característica sin conexión, consulte [Getting Started Offline Sample][Getting Started Offline Sample].

1.  Instale SQLite. Puede instalarlo desde este vínculo, [SQLite para Windows 8.1][SQLite para Windows 8.1].

    > [WACOM.NOTE] Si utiliza Internet Explorer, es posible que al hacer clic en el vínculo para instalar SQLite se le solicite que descargue .vsix como archivo .zip. Guarde el archivo en una ubicación del disco duro con la extensión .vsix en lugar de .zip. A continuación, haga doble clic en el archivo .vsix en el Explorador de Windows para ejecutar la instalación.

2.  En Visual Studio, abra el proyecto que finalizó en el tutorial [Introducción a los Servicios móviles][Introducción a los Servicios móviles] o [Introducción a los datos][Introducción a los datos]. En el Explorador de soluciones, haga clic con el botón secundario en **Referencias** en el proyecto y agregue una referencia a **SQLite para Windows en tiempo de ejecución** en **Windows** \>**Extensiones**.

    ![][0]

3.  SQLite en tiempo de ejecución requiere que se cambie la arquitectura de procesador del proyecto que se compila a **x86**, **x64** o **ARM**. No se admite **cualquier CPU**. Cambie la arquitectura del procesador por una de las configuraciones admitidas que quiere probar.

4.  En el Explorador de soluciones de Visual Studio, haga clic con el botón secundario en el proyecto de la aplicación cliente y haga clic en **Administrar paquetes de NuGet** para ejecutar el Administrador de paquetes NuGet. Busque **SQLiteStore** para instalar el paquete **WindowsAzure.MobileServices.SQLiteStore**.

    ![][1]

5.  En el Explorador de soluciones de Visual Studio, abra el archivo MainPage.xaml.cs. Agregue las siguientes instrucciones using en la parte superior del archivo:

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Newtonsoft.Json.Linq;

6.  En Mainpage.xaml.cs, reemplace la declaración de `todoTable` por una declaración de tipo `IMobileServicesSyncTable` que se inicializa llamando a `MobileServicesClient.GetSyncTable()`.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();

7.  En MainPage.xaml.cs, actualice la clase `TodoItem` para que incluya la propiedad del sistema **Version** de la forma siguiente.

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

8.  En MainPage.xaml.cs, actualice el controlador de eventos `OnNavigatedTo` para que sea un método `async` e inicialice el contexto de sincronización de cliente con un almacén SQLite. El almacén SQLite se crea con una tabla que coincide con el esquema de la tabla del servicio móvil pero debe contener la propiedad del sistema **Version** agregada en el paso anterior.

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

9.  En el Explorador de soluciones de Visual Studio, abra el archivo MainPage.xaml. Busque el elemento de cuadrícula que contiene StackPanel con el título **Query and Update Data**. Agregue el siguiente código de la interfaz de usuario que reemplaza los elementos de las definiciones de fila hasta la etiqueta de inicio de ListView.

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
            Click="ButtonPush_Click" Width="80" Height="34">Push</Button>
        <Button Grid.Row="1" Grid.Column="1" Margin="5,5,0,0"  Name="ButtonPull" 
            Click="ButtonPull_Click" Width="80" Height="34">Pull</Button>
        <ListView Name="ListItems" SelectionMode="None" Margin="0,10,0,0" Grid.ColumnSpan="2" Grid.Row="2">

10. En MainPage.xaml.cs, agregue los controladores de eventos de clic del botón para los botones **Push** y **Pull**, y guarde el archivo.

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

11. No ejecute todavía la aplicación. Presione la tecla **F7** para volver a compilar el proyecto. Compruebe que no se hayan producido errores de compilación.

## <a name="test-offline-app"></a>Prueba de la aplicación en un escenario sin conexión

En esta sección, se interrumpe la conexión con el servicio móvil para simular un escenario sin conexión. A continuación, se agregan algunos elementos de datos que se guardarán en el almacén local.

Observe que en esta sección la aplicación no debe estar conectada a ningún servicio móvil. Por lo que, si se prueban, los botones **Push** y **Pull** lanzarán excepciones. En la siguiente sección, volverá a conectar la aplicación cliente al servicio móvil para probar las operaciones de **inserción** y **extracción** y sincronizar el almacén con la base de datos del servicio móvil.

1.  En el Explorador de soluciones de Visual Studio, abra App.xaml.cs. Cambie la inicialización de **MobileServiceClient** a una dirección no válida reemplazando "**azure-mobile.net**" por "**azure-mobile.xxx**" para su URL. A continuación, guarde el archivo.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2.  En Visual Studio, presione **F5** para compilar y ejecutar la aplicación. Escriba un nuevo elemento todo y haga clic en **Save**. Los nuevos elementos todo solo están en el almacén local hasta que se puedan insertar en el servicio móvil. La aplicación cliente se comporta como si estuviera conectada al servicio móvil y admite todas las operaciones de creación, lectura, actualización y eliminación (CRUD).

    ![][2]

3.  Cierre la aplicación y reiníciela para comprobar que los nuevos elementos que creó se mantienen en el almacén local.

## <a name="update-online-app"></a>Actualización de la aplicación para volver a conectar el servicio móvil

En esta sección se vuelve a conectar la aplicación al servicio móvil. De este modo se simula que la aplicación pasa de un estado sin conexión a un estado en línea con el servicio móvil.

1.  En el Explorador de soluciones de Visual Studio, abra App.xaml.cs. Vuelva a cambiar la inicialización de **MobileServiceClient** a la dirección correcta reemplazando "**azure-mobile.xxx**" por "**azure-mobile.net**" para su URL. A continuación, guarde el archivo.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

## <a name="test-online-app"></a>Prueba de la aplicación conectada al servicio móvil

En esta sección probará las operaciones de inserción y extracción para sincronizar el almacén local con la base de datos del servicio móvil.

1.  En Visual Studio, presione **F5** para volver a compilar y ejecutar la aplicación. Observe que los datos tienen el mismo aspecto que en el escenario sin conexión aunque la aplicación ahora está conectada con el servicio móvil. Esto se debe a que la aplicación siempre funciona con el elemento `IMobileServiceSyncTable` que apunta al almacén local.

    ![][3]

2.  Inicie sesión en el Portal de administración de Azure y consulte la base de datos del servicio móvil. Si el servicio usa el back-end de JavaScript para servicios móviles, puede examinar los datos en la pestaña **Data** del servicio móvil. Si usa el back-end de .NET para el servicio móvil, puede hacer clic en el botón **Manage** de su base de datos en la extensión de SQL Azure para ejecutar una consulta en la tabla.

    Observe que los datos no se han sincronizado entre la base de datos y el almacén local.

    ![][4]

3.  En la aplicación, presione el botón **Push**. Esto hace que la aplicación llame a `MobileServiceClient.SyncContext.PushAsync` y luego a `RefreshTodoItems` para actualizar la aplicación con los elementos del almacén local. Esta operación de inserción se traduce en que la base de datos del servicio móvil recibe los datos del almacén. Sin embargo, el almacén local no recibe los elementos de la base de datos del servicio móvil.

    Una operación de inserción se ejecuta de `MobileServiceClient.SyncContext` y no de `IMobileServicesSyncTable`, e inserta cambios en todas las tablas asociadas a dicho contexto de sincronización. Esto tiene por objeto incluir escenarios en los que existen relaciones entre tablas.

    ![][5]

4.  En la aplicación, algunos elementos nuevos para el almacén local.

    ![][6]

5.  Esta vez, presione el botón **Pull** de la aplicación. La aplicación solo llama a `IMobileServiceSyncTable.PullAsync()` y a `RefreshTodoItems`. Observe que todos los datos de la base de datos del servicio móvil se extrajeron en el almacén local y se muestran en la aplicación. No obstante, observe también que todos los datos del almacén local seguían insertados en la base de datos del servicio móvil. Esto se debe a que una **extracción siempre realiza primero una inserción**.

    ![][7]

    ![][8]

## Resumen

Para admitir las características sin conexión de servicios móviles, usamos la interfaz `IMobileServiceSyncTable` e inicializamos `MobileServiceClient.SyncContext` con un almacén local. En este caso, el almacén local era una base de datos SQLite.

Las operaciones CRUD normales para servicios móviles funcionan como si la aplicación siguiera conectada, pero todas las operaciones se producen en el almacén local.

Cuando queramos sincronizar el almacén local con el servidor, usaremos los métodos `IMobileServiceSyncTable.PullAsync` y `MobileServiceClient.SyncContext.PushAsync`.

-   Para insertar cambios en el servidor, llamamos a `IMobileServiceSyncContext.PushAsync()`. Este método es miembro de `IMobileServicesSyncContext` y no de la tabla de sincronización porque insertará cambios en todas las tablas:

    Solo los registros que se han modificado localmente de alguna forma (mediante operaciones CRUD) se enviarán al servidor.

-   Para extraer datos de una tabla del servidor en la aplicación, llamamos a `IMobileServiceSyncTable.PullAsync`.

    Una extracción siempre realiza primero una inserción.

    También existen sobrecargas de **PullAsync()** que permiten especificar una consulta. Observe que en la versión preliminar de compatibilidad sin conexión con Servicios móviles, **PullAsync** leerá todas las filas de la tabla (o consulta) correspondiente: por ejemplo, no intenta leer solo las filas posteriores a la última sincronización. Si las filas ya existen en la tabla de sincronización local, permanecerán intactas.

## Pasos siguientes

-   [Control de conflictos con compatibilidad sin conexión para Servicios móviles][Control de conflictos con compatibilidad sin conexión para Servicios móviles]

<!-- Anchors. --> 
<!-- Images --> 
<!-- URLs. -->

  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-store-get-started/
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=AE564AB28
  [Actualización de la aplicación para que admita características sin conexión]: #enable-offline-app
  [Prueba de la aplicación en un escenario sin conexión]: #test-offline-app
  [Actualización de la aplicación para volver a conectar el servicio móvil]: #update-online-app
  [Prueba de la aplicación conectada al servicio móvil]: #test-online-app
  [Getting Started Offline Sample]: http://go.microsoft.com/fwlink/?LinkId=394777
  [SQLite para Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
  [0]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png
  [2]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-offline-app-run1.png
  [3]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run1.png
  [4]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png
  [5]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png
  [6]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png
  [7]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run3.png
  [8]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png
  [Control de conflictos con compatibilidad sin conexión para Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/
