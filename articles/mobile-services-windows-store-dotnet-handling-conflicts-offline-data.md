<properties 
	pageTitle="Control de conflictos con datos sin conexión en Servicios móviles (Tienda Windows) | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo usar Servicios móviles de Azure para gestionar los conflictos que se producen al sincronizar datos sin conexión en su aplicación de la Tienda Windows" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="wesmc"/>


# Control de conflictos con la sincronización de datos sin conexión en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-offline-conflicts](../includes/mobile-services-selector-offline-conflicts.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>En este tema se explica cómo sincronizar los datos y controlar conflictos cuando se usan las capacidades sin conexión de Servicios móviles de Azure.</p>
<p>Si prefiere ver un vídeo, el clip que aparece a la derecha muestra los mismos pasos que este tutorial.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">ver el tutorial</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir vídeo</span></a> <span class="time">14:36</span></div>
</div>

En este tutorial se descargará una solución universal C# de Windows para una aplicación que admite el control de conflictos de sincronización sin conexión. Integrará un servicio móvil con la aplicación y, a continuación, ejecutará los clientes de Tienda Windows 8.1 y Windows Phone 8.1 para generar un conflicto de sincronización y resolverlo.

Este tutorial se basa en los pasos y en la aplicación de ejemplo del tutorial anterior [Introducción a los datos sin conexión]. Antes de completar este tutorial, primero debe completar [Introducción a los datos sin conexión].


Este tutorial le guiará a través de estos pasos básicos:

1. [Descarga del proyecto de la aplicación de la Tienda Windows] 
2. [Prueba de la aplicación en un servicio móvil]
3. [Actualización manual de los datos en el back-end para crear un conflicto]

Para este tutorial se necesita Visual Studio 2013 en Windows 8.1.


## <a name="download-app"></a>Descarga del proyecto de ejemplo

![][0]

Este tutorial se refiere a cómo el [ejemplo de Todo sin conexión de Servicios móviles] controla conflictos de sincronización entre el almacén sin conexión local y la base de datos del Servicio móvil en Azure.

1. Descargue el archivo ZIP del [repositorio de muestras de GitHub para Servicios móviles] y extráigalo a un directorio de trabajo. 

2. Si todavía no ha instalado SQLite para Windows 8.1 y Windows Phone 8.1 según lo mencionado en el tutorial [Introducción a los datos sin conexión], instale ambos tiempos de ejecución.

3. En Visual Studio 2013, abra el archivo de solución  *mobile-services-samples\TodoOffline\WindowsUniversal\TodoOffline-Universal.sln*. Presione la tecla **F5** para volver a compilar y ejecutar el proyecto. Compruebe que se restauran los paquetes de NuGet y las referencias se establecen correctamente.

    >[AZURE.NOTE] Es posible que se requiera eliminar cualquier referencia anterior a SQLite en tiempo de ejecución y reemplazarla por la referencia actualizada, tal como se menciona en el tutorial [Introducción a los datos sin conexión].

4. En la aplicación, escriba algún texto en **Insertar TodoItem** y, a continuación, haga clic en **Guardar** para agregar algunos elementos al almacén local. A continuación, cierre la aplicación.

Tenga en cuenta que la aplicación aún no está conectada a ningún servicio móvil, por lo que los botones de **inserción** y **extracción** lanzarán excepciones.




## <a name="test-app"></a>Prueba de la aplicación en un servicio móvil

Ahora es el momento de probar la aplicación en los servicios móviles.

1. En el Portal de administración de Azure, busque la clave de la aplicación del servicio móvil; para ello, haga clic en **Administrar claves** en la barra de comandos de la pestaña **Panel**. Copie la **clave de aplicación**.

2. En el Explorador de soluciones de Visual Studio, abra el archivo App.xaml.cs en el proyecto de ejemplo cliente. Cambie la inicialización de **MobileServiceClient** para usar la URL del servicio móvil y la clave de aplicación:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3. En Visual Studio, presione la tecla **F5** para compilar y ejecutar la aplicación de nuevo.

    ![][0]


## <a name="handle-conflict"></a>Actualización de los datos en el back-end para crear un conflicto

En un escenario real, se produciría un conflicto de sincronización cuando una aplicación inserta actualizaciones en un registro de la base de datos y, a continuación, otra aplicación trata de insertar un cambio en el mismo registro usando un campo de versión obsoleta de dicho registro. Como se indicó en el tutorial [Introducción a los datos sin conexión], la propiedad del sistema de versión se requiere para admitir las características de sincronización sin conexión. Esta información de versión se examina con cada actualización de la base de datos. Si una instancia de la aplicación intenta actualizar un registro usando una versión obsoleta, se producirá un conflicto que se detectará como  `MobileServicePreconditionFailedException` en la aplicación. Si la aplicación no detecta la  `MobileServicePreconditionFailedException`, se arrojará una  `MobileServicePushFailedException` que describirá cuántos errores de sincronización se encontraron.

>[AZURE.NOTE] Para admitir la sincronización de los registros eliminados mediante la sincronización de datos sin conexión, debe habilitar la [Eliminación temporal](mobile-services-using-soft-delete.md). De lo contrario, deberá quitar manualmente los registros en el almacén local o llamar a  `IMobileServiceSyncTable::PurgeAsync()` para purgar el almacén local.


Los pasos siguientes muestran los clientes de Windows Phone 8.1 y Tienda Windows 8.1 que se ejecutan al mismo tiempo para provocar y resolver un conjunto usando el ejemplo.

1. En Visual Studio, haga clic con el botón derecho en el proyecto de Windows Phone 8.1 y, a continuación, haga clic en **Establecer como proyecto de inicio**. Luego presione las teclas **Ctrl+F5** para ejecutar el cliente de Windows Phone 8.1 sin depuración. Una vez que el cliente de Windows Phone 8.1 se ejecuta en el emulador, haga clic en el botón de **extracción** para sincronizar el almacén local con el estado actual de la base de datos.
 
    ![][3]
 
   
2. En Visual Studio, haga clic con el botón derecho en el proyecto de Windows 8.1 en tiempo de ejecución y, a continuación, haga clic en **Establecer como proyecto de inicio** para volver a establecerlo en el proyecto de inicio. Luego presione **F5** para ejecutarlo. Una vez que el cliente de Tienda Windows 8.1 se ejecuta, haga clic en el botón de **extracción** para sincronizar el almacén local con el estado actual de la base de datos

    ![][4]
 
3. En este punto, ambos clientes están sincronizados con la base de datos. El código para ambos clientes también usa la sincronización incremental, por lo que solo sincronizarán elementos todo incompletos. Se omitirán los elementos todo completados. Elija uno de los elementos y edite el texto del mismo elemento en ambos clientes a un valor distinto. Haga clic en el botón de **inserción** para sincronizar ambos cambios con la base de datos en el servidor.

    ![][5]

    ![][6]


4. El cliente cuya inserción se ejecutó en último lugar encuentra el conflicto y permite que el usuario decida qué valor confirmar en la base de datos. La excepción proporciona el valor de versión correcto que se usa para resolver el conflicto.

    ![][7]



## Revisión del código para controlar los conflictos de sincronización

Para usar las características sin conexión de los Servicios móviles, debe incluir la columna de versión en la base de datos local y en el objeto de transferencia de datos. Esto se logra mediante la actualización de la clase  `TodoItem` del siguiente miembro:

        [Version]
        public string Version { get; set; }

La columna '__version' está incluida en la base de datos local en el método  `OnNavigatedTo()` cuando la clase  `TodoItem` se utiliza para definir el almacén local.

Para controlar los conflictos de sincronización sin conexión en el código, se crea una clase que implemente  `IMobileServiceSyncHandler`. Transfiera un objeto de este tipo en la llamada a  `MobileServiceClient.SyncContext.InitializeAsync()`. Esto también ocurre en el método  `OnNavigatedTo()` del ejemplo.

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

La clase  `SyncHandler` en **SyncHandler.cs** implementa `IMobileServiceSyncHandler`. Se llama al método  `ExecuteTableOperationAsync` cuando se envía cada operación de inserción al servidor. Si se lanza una excepción del tipo  `MobileServicePreconditionFailedException`, significa que hay un conflicto entre las versiones local y remota de un elemento.

Para resolver conflictos a favor del elemento local, simplemente debe reintentar la operación. Cuando se ha producido un conflicto, la versión del elemento local se actualizará para coincidir con la versión del servidor, por lo que la ejecución de la operación de nuevo sobrescribirá los cambios del servidor con los cambios locales:

    await operation.ExecuteAsync(); 

Para resolver los conflictos a favor del elemento del servidor, simplemente vuelva de  `ExecuteTableOperationAsync`. La versión local del objeto se descartará y se reemplazará por el valor del servidor.

Para detener la operación de inserción (pero mantener los cambios en cola), use el método  `AbortPush()`:

    operation.AbortPush();

Esto detendrá la operación de inserción actual, pero mantendrá todos los cambios pendientes, incluida la operación actual, si se llama a  `AbortPush` desde  `ExecuteTableOperationAsync`. La próxima vez que se llame a  `PushAsync()`, estos cambios se enviarán al servidor. 

Cuando se cancela una inserción, `PushAsync` producirá un  `MobileServicePushFailedException` y la propiedad de excepción  `PushResult.Status` tendrá el valor  `MobileServicePushStatus.CancelledByOperation`. 


<!-- Anchors. -->
[Descarga del proyecto de la aplicación de la Tienda Windows] : #download-app
[Crear el servicio móvil]: #create-service
[Incorporación de una columna de fecha de vencimiento para la base de datos]: #add-column
[Actualización de la base de datos para servicios móviles back-end de .NET]: #dotnet-backend  
[Actualización de la base de datos para servicios móviles con JavaScript]: #javascript-backend
[Prueba de la aplicación en un servicio móvil]: #test-app
[Actualización manual de los datos en el back-end para crear un conflicto]: #handle-conflict
[Pasos siguientes]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
[1]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/javascript-backend-database.png
[2]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/dotnet-backend-database.png
[3]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/wp81-view.png
[4]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/win81-view.png
[5]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/wp81-edit-text.png
[6]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/win81-edit-text.png
[7]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/conflict.png




<!-- URLs -->
[Código de ejemplo de control de conflictos]: http://go.microsoft.com/fwlink/?LinkId=394787
[Introducción a los servicios móviles]: /es-es/documentation/articles/mobile-services-windows-store-get-started/
[Introducción a los datos sin conexión]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data
[SQLite para Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Control de conflictos de base de datos]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/#test-app
[Repositorio de muestras de GitHub para Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=512865
[Ejemplo de Todo sin conexión de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=512866



<!--HONumber=42-->
