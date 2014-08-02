<properties linkid="develop-mobile-tutorials-get-started-with-data-xamarin-android" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Xamarin.Android) - Azure Mobile Services" metaKeywords="Azure Xamarin.Android data, Azure mobile services data" description="Learn how to store and access data from your Azure Mobile Services Xamarin.Android app." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services" documentationCenter="Mobile" authors="" />

Introducción a los datos en Servicios móviles
=============================================

[C\# para Tienda Windows](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet "C# para Tienda Windows")[JavaScript para Tienda Windows](/en-us/develop/mobile/tutorials/get-started-with-data-js "JavaScript para Tienda Windows")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de Xamarin.Android. En este tutorial descargará una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil en la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

**Nota:**

Este tutorial está destinado a ayudarle a comprender mejor cómo los Servicios móviles le permiten usar Azure para almacenar y recuperar datos de una aplicación Xamarin.Android. Para ello, en este tema se recorren muchos de los pasos que se completan automáticamente en el inicio rápido de Servicios móviles. Si esta es la primera vez que usa los Servicios móviles, considere la posibilidad de completar antes el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started-xamarin-android).

Este tutorial le guiará a través de estos pasos básicos:

1.  [Descarga del proyecto de la aplicación Xamarin.Android](http://go.microsoft.com/fwlink/p/?LinkId=331302)
2.  [Creación del servicio móvil](#create-service)
3.  [Incorporación de una tabla de datos para almacenamiento](#add-table)
4.  [Actualización de la aplicación para usar Servicios móviles](#update-app)
5.  [Prueba de la aplicación en Servicios móviles](#test-app)

**Nota:**

para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A9C9624B5).

Este tutorial requiere el [componente Servicios móviles de Azure](http://components.xamarin.com/view/azure-mobile-services/) (en inglés), [Xamarin.Android] y el SDK de Android 4.2 o una versión posterior.

**Nota:**

El proyecto GetStartedWithData descargado requiere Android 4.2 o una versión más reciente. No obstante, el SDK para Servicios móviles solo requiere Android 2.2 o una versión más reciente.

Descarga del proyectoDescarga del proyecto GetStartedWithData
-------------------------------------------------------------

Este tutorial se ha creado sobre la [aplicación GetStartedWithData](http://go.microsoft.com/fwlink/p/?LinkId=331302), que es una aplicación Xamarin.Android. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de Android para Servicios móviles, salvo por los elementos agregados que se almacenan localmente en la memoria.

1.  Descargue la aplicación de ejemplo `GetStartedWithData` y extraiga los archivos en su equipo.

2.  En Xamarin Studio, haga clic en **File**, **Open**, vaya a donde extrajo el proyecto de ejemplo GetStartedWithData, seleccione **XamarinTodoQuickStart.Android.sln** y ábralo.

3.  Busque y abra la clase **TodoActivity**

  Tenga en cuenta que hay comentarios `// TODO::` que especifican los pasos que debe realizar para que la aplicación funcione con su servicio móvil.

4.  En el menú **Run**, haga clic en **Start Without Debugging**; se le pedirá elegir un emulador o un dispositivo Android USB conectado.

    **Nota:**

    puede ejecutar este proyecto usando un teléfono Android o el emulador de Android. La ejecución con un teléfono Android requiere la descarga de un controlador USB específico del teléfono.

    Para ejecutar el proyecto en el emulador de Android, debe definir por lo menos un dispositivo virtual Android (AVD). Use el administrador AVD para crear y administrar estos dispositivos.

5.  En la aplicación, escriba un texto significativo, como *Realice el tutorial* y, a continuación, haga clic en **Add**.

  ![][13]

  Tenga en cuenta que el texto guardado se almacena en una colección de la memoria y se muestra en la lista de abajo.

Creación de un servicio móvilCreación de un servicio móvil en el Portal de administración
-----------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

Incorporación de una tabla nuevaIncorporación de una tabla nueva al servicio móvil
----------------------------------------------------------------------------------

Para poder almacenar datos de aplicaciones en el nuevo servicio móvil, primero debe crear una tabla.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  Haga clic en la pestaña **Data** y, a continuación, en **+Create**.

  ![][5]

  Se muestra el cuadro de diálogo **Crear nueva tabla**.

3.  En **Table name**, escriba *TodoItem* y, a continuación, haga clic en el botón de comprobación.

  ![](./media/partner-xamarin-mobile-services-android-get-started-data/mobile-create-todoitem-table.png)

  Con esto se crea una tabla de almacenamiento **TodoItem** con el conjunto de permisos predeterminado, lo que significa que todo usuario de la aplicación podrá tener acceso a los datos de la tabla y modificarlos.

  <div class="dev-callout"> 
  <b>Nota:</b> 
  <p>El mismo nombre de tabla se utiliza en la guía de inicio rápido de Servicios móviles. Sin embargo, cada tabla se crea en un esquema que es específico de un servicio móvil determinado. De esta manera, se evita que colisionen los datos cuando varios servicios móviles utilizan la misma base de datos.</p> 
  </div>

1.  Haga clic en la nueva tabla **TodoItem** y verifique que no haya filas de datos.

2.  Haga clic en la pestaña **Columns** y compruebe que hay solo una columna **id**, que se crea automáticamente.

  Este es el requisito mínimo para una tabla en Servicios móviles.

  <div class="dev-callout"><b>Nota:</b>
  <p>Cuando está habilitado el esquema dinámico en su servicio móvil, se crean columnas automáticamente cuando se envían objetos JSON al servicio móvil mediante una operación de inserción o de actualización.</p>
  </div>

  Ahora ya está listo para utilizar el nuevo servicio móvil como almacenamiento de datos para la aplicación.

Actualización de la aplicaciónActualización de la aplicación para usar el servicio móvil para el acceso a datos
---------------------------------------------------------------------------------------------------------------

Ahora que el servicio móvil está listo, puede actualizar la aplicación a fin de almacenar elementos en Servicios móviles en lugar de en la colección local.

1.  Si todavía no aparece **Servicios móviles de Azure** en la carpeta de componentes, haga clic con el botón secundario en **Components**, elija **Get More Components** y, a continuación, busque **Servicios móviles de Azure**.

	De este modo se agrega el componente del SDK de Servicios móviles al proyecto.

2.  Abra el archivo **AndroidManifest.xml** y asegúrese de que existe la siguiente línea de permisos:

		<uses-permission android:name="android.permission.INTERNET" />

	Con esto, la aplicación puede tener acceso a Servicios móviles en Azure.

3.  En la ventana **Solution**, abra la clase **TodoActivity** y quite la marca de comentario de la siguiente línea de código:

         using Microsoft.WindowsAzure.MobileServices;

4.  Eliminaremos la lista de la memoria que utiliza actualmente la aplicación, para que podamos reemplazarla por un servicio móvil. En la clase **TodoActivity**, comente la línea de código siguiente que define la lista **todoItemList** existente.

         public List<TodoItem> todoItemList = new ArrayList<TodoItem>();

5.  Una vez que haya realizado el paso anterior, el proyecto indicará los errores de la compilación. Busque las tres ubicaciones restantes en las que se usa la variable `todoItemList` y comente las secciones indicadas.

6.  Ahora agregamos nuestros servicios móviles. Quite la marca de comentario de las líneas de código siguientes:

         private MobileServiceClient client; // Referencias de cliente del servicio móvil
         private IMobileServiceTable<TodoItem> todoTable; // Tabla del servicio móvil usada para el acceso a los datos   

7.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

8.  Haga clic en la pestaña **Panel** y anote la **URL del sitio**, a continuación haga clic en **Manage keys** y anote la **clave de la aplicación**.

  ![][8]

	Necesitará estos valores para obtener acceso al servicio móvil desde su código de aplicación.	

9.  En la clase **Constants**, quite la marca de comentario de las siguientes variables de miembro:

         public const string ApplicationURL = @"AppUrl";
         public const string ApplicationKey = @"AppKey";

10.  Reemplace **AppUrl** y **AppKey** de las variables anteriores por los valores recuperados del Portal de administración mencionado.

11.  En el método **OnCreate**, quite la marca de comentario de las líneas de código siguientes que definen la variable **MobileServiceClient**:

        // Crear la instancia de cliente del servicio móvil usando la URL
        // y la clave del servicio móvil proporcionadas.
        client = new MobileServiceClient(
            Constants.ApplicationURL,
            Constants.ApplicationKey).WithFilter(filter);

        // Obtenga la instancia de la tabla de Servicios móviles que haya que usar.
        todoTable = client.GetTable<TodoItem>();    

	De este modo, se crea la nueva instancia de MobileServiceClient que se usa para obtener acceso a su servicio móvil. Además, se crea la instancia de MobileServiceTable que se usa para el almacenamiento de datos de proxy en el servicio móvil.

12.  Busque la clase ProgressFilter en la parte inferior del archivo y quite la marca de comentario. Esta clase muestra un indicador 'loading' mientras MobileServiceClient está ejecutando operaciones de red.

13.  Quite la marca de comentario de estas líneas del método **CheckItem**:

        try {
            await todoTable.UpdateAsync(item);
            if (item.Complete)
                adapter.Remove(item);
        } catch (Exception e) {
            CreateAndShowDialog(e, "Error");
        }

	De este modo se envía una actualización del elemento al servicio móvil y se eliminan los elementos marcados del adaptador.

14.  Quite la marca de comentario de estas líneas del método **AddItem**:

        try 
        {
            // Insertar el elemento nuevo
            await todoTable.InsertAsync(item);

            if (!item.Complete) 
                adapter.Add(item);          
        } 
        catch (Exception e) 
        {
            CreateAndShowDialog(e, "Error");
        }           

	Este código crea un elemento y lo inserta en la tabla del servicio móvil remoto.

15.  Quite la marca de comentario de estas líneas del método **RefreshItemsFromTable**:

        try {
            // Obtener los elementos que no se marcaron como completados y agregarlos en el adaptador
            var list = await todoTable.Where(item => item.Complete == false).ToListAsync ();

            adapter.Clear();

            foreach (TodoItem current in list)
                adapter.Add(current);
        } 
        catch (Exception e) 
        {
            CreateAndShowDialog(e, "Error");
        }

    De este modo se consulta el servicio móvil y se devuelven todos los elementos que no se hayan marcado como completos. Los elementos se agregan al adaptador para enlace.

Ahora que se ha actualizado la aplicación para utilizar Servicios móviles para almacenamiento back-end, es momento de probar la aplicación con Servicios móviles.

Prueba de la aplicaciónPrueba de la aplicación con su servicio móvil nuevo
--------------------------------------------------------------------------

1.  En el menú **Run**, haga clic en **Start Without Debugging** para iniciar el proyecto. Se le pedirá elegir una imagen de emulador existente o un dispositivo Android USB conectado.

    De este modo se ejecuta su aplicación, que se ha creado con Xamarin.Android, y se usa la biblioteca de clientes para enviar una consulta que devuelve elementos desde su servicio móvil.

2.  Como antes, escriba un texto descriptivo y, a continuación, haga clic en **Add**.

  Esto envía un elemento nuevo como inserción al servicio móvil.

3.  En el [Portal de administración](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4.  Haga clic en la pestaña **Data** y, a continuación, en **Browse**.

  ![][9]
          
  Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para coincidir con la clase TodoItem en la aplicación.

Así concluye el tutorial **Introducción a los datos** para Xamarin.Android.

Obtener un ejemplo completado
-----------------------------

Descargue el [proyecto de ejemplo completado](http://go.microsoft.com/fwlink/p/?LinkId=331302). Asegúrese de actualizar las variables **applicationURL** y **applicationKey** con su propia configuración de Azure.

Pasos siguientes
----------------

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de Xamarin.Android para trabajar con datos en Servicios móviles.

A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Validación y modificación de datos con scripts](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android)
  <br/>Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android)
  <br/>Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Cuando haya completado la serie de datos, pruebe estos otros tutoriales de Xamarin.Android:

-   [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android)
  <br/>Aprenda a autenticar a los usuarios de su aplicación.

-   [Introducción a las notificaciones de inserción](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android)
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación con Servicios móviles.


<!-- Images. -->





[5]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-quickstart-startup-android.png