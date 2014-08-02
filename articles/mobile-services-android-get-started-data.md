<properties linkid="develop-mobile-tutorials-get-started-with-data-android" urlDisplayName="Get Started with Data - Android" pageTitle="Get started with data (Android) | Mobile Dev Center" metaKeywords="Azure android data, Azure mobile services data, windows droid, windows android, microsoft droid, microsoft android" description="Learn how to get started using Mobile Services to leverage data in your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="ricksal,glenga" solutions="" manager="" editor="" />

Introducción a los datos en Servicios móviles
=============================================

[C\# para Tienda Windows](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet "C# para Tienda Windows")[JavaScript para Tienda Windows](/en-us/develop/mobile/tutorials/get-started-with-data-js "JavaScript para Tienda Windows")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de Android. En este tutorial descargará una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil en la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

[Ver el tutorial (en inglés)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services) [Reproducir vídeo (en inglés)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services)15:32

**Nota:**

Este tutorial está destinado a ayudarle a comprender cómo los Servicios móviles le permiten usar Azure para almacenar y recuperar datos de una aplicación Android. Para ello, en este tema se recorren muchos de los pasos que se completan automáticamente en el inicio rápido de Servicios móviles. Si esta es la primera vez que usa los Servicios móviles, considere la posibilidad de completar antes el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started-android).

Este tutorial le guiará a través de estos pasos básicos:

1.  [Descarga del proyecto de la aplicación Android](#download-app)
2.  [Creación del servicio móvil](#create-service)
3.  [Incorporación de una tabla de datos para almacenamiento](#add-table)
4.  [Actualización de la aplicación para usar Servicios móviles](#update-app)
5.  [Prueba de la aplicación en Servicios móviles](#test-app)

**Nota:**

Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AED8DE357).

Este tutorial requiere el [SDK de Android para Servicios móviles](http://go.microsoft.com/fwlink/p/?LinkID=280126); el [SDK de Android](https://go.microsoft.com/fwLink/p/?LinkID=280125), que incluye el entorno de desarrollo integrado de Eclipse (IDE) y el complemento de las herramientas del desarrollador de Android (ADT); y Android 4.2 o una versión más reciente.

**Nota:**

Este tutorial proporciona instrucciones para la instalación del SDK de Android y del SDK de Android para Servicios móviles. El proyecto GetStartedWithData descargado requiere Android 4.2 o una versión más reciente. No obstante, el SDK para Servicios móviles solo requiere Android 2.2 o una versión más reciente.

Descarga del proyectoDescarga del proyecto GetStartedWithData
-------------------------------------------------------------

### Obtención del código de ejemplo

Este tutorial se ha creado sobre la [aplicación GetStartedWithData](http://go.microsoft.com/fwlink/p/?LinkID=282122), que es una aplicación Android. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de Android para Servicios móviles, salvo por los elementos agregados que se almacenan localmente en la memoria. Agregará el código necesario para que los datos continúen en el almacenamiento.

1.  Descargue la aplicación de ejemplo `GetStartedWithData` y expanda los archivos de su equipo.

2.  En Eclipse, haga clic en **File**, **Import**, expanda **Android**, haga clic en **Existing Android Code into Workspace** y, por último, en **Next.**

	![](./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png)

1.  Haga clic en **Browse**, desplácese a la ubicación de los archivos de proyecto expandidos, haga clic en **OK** y asegúrese de que el proyecto TodoActivity esté marcado. Si desea copiar el proyecto en su área de trabajo, marque la casilla **Copy projects into workspace**. Finalmente, haga clic en **Finish**.

	![](./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png)

    De esta forma se importan los archivos del proyecto en el área de trabajo.

### Comprobación de la versión del SDK de Android

[WACOM.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version.md)]

### Inspección y ejecución del código de ejemplo

1.  En el explorador de paquetes, expanda **GetStartedWithData**, **src** y **.com.example.GetStartedWithData** y, a continuación, examine el archivo ToDoActivity.java.

     ![](./media/mobile-services-android-get-started-data/mobile-eclipse-project.png)

     Tenga en cuenta que hay comentarios `//TODO` que especifican los pasos que debe realizar para que la aplicación funcione con su servicio móvil.

2.  En el menú **Run**, haga clic en **Run** y, a continuación, en **Android Application** para iniciar el proyecto.

    **Nota:**

    puede ejecutar este proyecto usando un teléfono Android o el emulador de Android. La ejecución con un teléfono Android requiere la descarga de un controlador USB específico del teléfono.

    Para ejecutar el proyecto en el emulador de Android, debe definir por lo menos un dispositivo virtual Android (AVD). Use el administrador AVD para crear y administrar estos dispositivos.

3.  En la aplicación, escriba un texto significativo, como *Realice el tutorial* y, a continuación, haga clic en **Add**.

     ![](./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png)

     Tenga en cuenta que el texto guardado se almacena en una colección de la memoria y se muestra en la lista de abajo.

Creación de un servicio móvilCreación de un servicio móvil en el Portal de administración
-----------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

Incorporación de una tabla nuevaIncorporación de una tabla nueva al servicio móvil
----------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

Actualización de la aplicaciónActualización de la aplicación para usar el servicio móvil para el acceso a datos
---------------------------------------------------------------------------------------------------------------

Ahora que el servicio móvil está listo, puede actualizar la aplicación a fin de almacenar elementos en Servicios móviles en lugar de en la colección local.

1.  Si todavía no tiene el [SDK de Android para Servicios móviles](http://go.microsoft.com/fwlink/p/?LinkID=280126), descárguelo ya y expanda los archivos comprimidos.

2.  Copie los archivos `.jar` de la carpeta `mobileservices` del SDK en la carpeta `libs` del proyecto GetStartedWithData.

3.  En explorador de paquetes de Eclipse, haga clic con el botón secundario en la carpeta `libs`, haga clic en **Refresh** y aparecerán los archivos jar copiados.

	De este modo, se agrega la referencia del SDK de los Servicios móviles al área de trabajo.

4.  Abra el archivo AndroidManifest.xml y agregue la línea siguiente, que permite a la aplicación obtener acceso a los Servicios móviles de Azure.

		<uses-permission android:name="android.permission.INTERNET" />         

5.  En el explorador de paquetes, abra el archivo TodoActivity.java ubicado en el paquete com.example.getstartedwithdata y quite la marca de comentario de las siguientes líneas de código:

        import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
        import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
        import com.microsoft.windowsazure.mobileservices.NextServiceFilterCallback;
        import com.microsoft.windowsazure.mobileservices.ServiceFilter;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterRequest;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponseCallback;
        import com.microsoft.windowsazure.mobileservices.TableOperationCallback;
        import com.microsoft.windowsazure.mobileservices.TableQueryCallback;

        import java.net.MalformedURLException;

6.  Eliminaremos la lista de la memoria que utiliza actualmente la aplicación, para que podamos reemplazarla por un servicio móvil. En la clase **ToDoActivity**, convierta en comentario la línea de código siguiente que define la lista **toDoItemList** existente.

        public List toDoItemList = new ArrayList();

7.  Una vez que haya realizado el paso anterior, el proyecto indicará los errores de la compilación. Busque las tres ubicaciones restantes en las que se usa la variable `toDoItemList` y convierta en comentarios las secciones indicadas. Elimine también `import java.util.ArrayList`. De este modo se elimina por completo la lista de la memoria.

8.  Ahora agregamos nuestros servicios móviles. Quite la marca de comentario de las líneas de código siguientes:

        private MobileServiceClient mClient;
        private private MobileServiceTable mToDoTable;

9.  Busque la clase ProgressFilter en la parte inferior del archivo y quite la marca de comentario. Esta clase muestra un indicador 'loading' mientras MobileServiceClient está ejecutando operaciones de red.

10.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

11.  Haga clic en la pestańa **Panel** y tome nota de la dirección que aparece en **Site URL**; a continuación, haga clic en **Manage keys** y tome nota de la clave indicada en **Application key**.

	![](./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png)
	
	Necesitará estos valores al obtener acceso al servicio móvil desde el código de la aplicación.

12.  En el método **onCreate**, quite la marca de comentario de las líneas de código siguientes que definen la variable **MobileServiceClient**:

        try {
        // Crear la instancia de cliente del servicio móvil usando la URL
        // y la clave del servicio móvil proporcionadas.
            mClient = new MobileServiceClient(
                    "MobileServiceUrl",
                    "AppKey", 
                    this).withFilter(new ProgressFilter());

            // Obtenga la instancia de la tabla de Servicios móviles que haya que usar.
            mToDoTable = mClient.getTable(ToDoItem.class);
        } catch (MalformedURLException e) {
            createAndShowDialog(new Exception("Hubo un error al crear el servicio móvil. Compruebe la URL"), "Error");
        }

	De este modo, se crea la nueva instancia de MobileServiceClient que se usa para obtener acceso a su servicio móvil. Además, se crea la instancia de MobileServiceTable que se usa para el almacenamiento de datos de proxy en el servicio móvil.

13.  En el código de arriba, reemplace `MobileServiceUrl` y `AppKey` por la URL y la clave de la aplicación de su servicio móvil, por ese orden.

14.  Quite la marca de comentario de estas líneas del método **checkItem**:

        mToDoTable.update(item, new TableOperationCallback() { 
            public void onCompleted(ToDoItem entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    if (entity.isComplete()) {
                        mAdapter.remove(entity);
                    }
                } else {
                    createAndShowDialog(exception, "Error");    
                }
            }
        });

	De este modo se envía una actualización del elemento al servicio móvil y se eliminan los elementos marcados del adaptador.

1.  Quite la marca de comentario de estas líneas del método **addItem**:

        mToDoTable.insert(item, new TableOperationCallback() {
                
            public void onCompleted(ToDoItem entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    if (!entity.isComplete()) {
                        mAdapter.add(entity);
                    }
                } else {
                    createAndShowDialog(exception, "Error");
                }               
            }
        });

	Este código crea un elemento y lo inserta en la tabla del servicio móvil remoto.

1.  Quite la marca de comentario de estas líneas del método **refreshItemsFromTable**:

        mToDoTable.where().field("complete").eq(false)
        .execute(new TableQueryCallback() {
             public void onCompleted(List result, 
                     int count, Exception exception, 
                     ServiceFilterResponse response) {
                            
                        if(exception == null){
                            mAdapter.clear();

                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        } else {
                            createAndShowDialog(exception, "Error");
                        }
                    }
                }); 

    De este modo se consulta el servicio móvil y se devuelven todos los elementos que no se hayan marcado como completos. Los elementos se agregan al adaptador para enlace.

Prueba de la aplicaciónPrueba de la aplicación con su servicio móvil nuevo
--------------------------------------------------------------------------

Ahora que la aplicación se ha actualizado para usar los Servicios móviles para almacenamiento back-end, puede probarla con los Servicios móviles usando el emulador de Android o un teléfono Android.

1.  En el menú **Run**, haga clic en **Run** para iniciar el proyecto.

    De este modo se ejecuta su aplicación, que se ha creado con el SDK de Android, y se usa la biblioteca del cliente para enviar una consulta que devuelve los elementos desde su servicio móvil.

2.  Como antes, escriba un texto descriptivo y, a continuación, haga clic en **Add**.

    Esto envía un elemento nuevo como inserción al servicio móvil.

3.  En el [Portal de administración](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4.  Haga clic en la pestańa **Data** y, a continuación, en **Browse**.

     ![](./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png)
          
     Fíjese en que la tabla **TodoItem** ahora contiene datos, con algunos valores generados por los Servicios móviles y que las columnas se han agregado automáticamente a la tabla para que coincidan con la clase TodoItem de la aplicación.

Así concluye el tutorial **Introducción a los datos** para Android.

Pasos siguientes
----------------

Este tutorial muestra los aspectos básicos de la habilitación de una aplicación Android para que funcione con los datos de los Servicios móviles.

A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Validación y modificación de datos con scripts](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
    Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación](/en-us/develop/mobile/tutorials/add-paging-to-data-android)
    Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Cuando haya completado la serie de datos, pruebe estos otros tutoriales de Android:

-   [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-android)
    Aprenda a autenticar a los usuarios de su aplicación.

-   [Introducción a las notificaciones de inserción](/en-us/develop/mobile/tutorials/get-started-with-push-android)
    Aprenda a enviar una notificación de inserción muy básica a la aplicación con Servicios móviles.


