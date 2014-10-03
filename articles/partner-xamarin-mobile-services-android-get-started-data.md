<properties linkid="develop-mobile-tutorials-get-started-with-data-xamarin-android" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Xamarin.Android) - Azure Mobile Services" metaKeywords="Azure Xamarin.Android data, Azure mobile services data" description="Learn how to store and access data from your Azure Mobile Services Xamarin.Android app." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services" documentationCenter="Mobile" authors="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Introducción a los datos en Servicios móviles

<div class="dev-center-tutorial-selector sublanding">    
    <a href="/en-us/develop/mobile/tutorials/get-started-with-data-dotnet" title="C# para Tienda Windows">C# para Tienda Windows</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-js" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de Xamarin.Android. En este tutorial descargará una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil en la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

<div class="dev-callout"><b>Nota:</b>
<p>Este tutorial est&aacute; destinado a ayudarle a comprender mejor c&oacute;mo los Servicios m&oacute;viles le permiten usar Azure para almacenar y recuperar datos de una aplicaci&oacute;n Xamarin.Android. Para ello, en este tema se recorren muchos de los pasos que se completan autom&aacute;ticamente en el inicio r&aacute;pido de Servicios m&oacute;viles. Si esta es la primera vez que usa los Servicios m&oacute;viles, considere la posibilidad de completar antes el tutorial <a href="/en-us/develop/mobile/tutorials/get-started-xamarin-android">Introducci&oacute;n a los Servicios m&oacute;viles</a>.</p>
</div>

Este tutorial le guiará a través de estos pasos básicos:

1.  [Descarga del proyecto de la aplicación Xamarin.Android][]
2.  [Creación del servicio móvil][]
3.  [Incorporación de una tabla de datos para almacenamiento][]
4.  [Actualización de la aplicación para usar Servicios móviles][]
5.  [Prueba de la aplicación en Servicios móviles][]

<div class="dev-callout"><strong>Nota:</strong> <p>para completar este tutorial, deber&aacute; tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluaci&oacute;n gratuita en tan solo unos minutos. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A9C9624B5" target="_blank">Evaluaci&oacute;n gratuita de Azure</a>.</p></div>

Este tutorial requiere el [componente Servicios móviles de Azure][] (en inglés), [Xamarin.Android] y el SDK de Android 4.2 o una versión posterior.

<div class="dev-callout"><b>Nota:</b>
<p>El proyecto GetStartedWithData descargado requiere Android 4.2 o una versi&oacute;n m&aacute;s reciente. No obstante, el SDK para Servicios m&oacute;viles solo requiere Android 2.2 o una versi&oacute;n m&aacute;s reciente.</p>
</div>

## <a name="download-app"></a><span class="short-header">Descarga del proyecto</span>Descarga del proyecto GetStartedWithData

Este tutorial se ha creado sobre la [aplicación GetStartedWithData][Descarga del proyecto de la aplicación Xamarin.Android], que es una aplicación Xamarin.Android. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de Android para Servicios móviles, salvo por los elementos agregados que se almacenan localmente en la memoria.

1.  Descargue la aplicación de ejemplo `GetStartedWithData` y extraiga los archivos en su equipo.

2.  En Xamarin Studio, haga clic en **File**, **Open**, vaya a donde extrajo el proyecto de ejemplo GetStartedWithData, seleccione **XamarinTodoQuickStart.Android.sln** y ábralo.

3.  Busque y abra la clase **TodoActivity**

    Tenga en cuenta que hay comentarios `// TODO::` que especifican los pasos que debe realizar para que la aplicación funcione con su servicio móvil.

4.  En el menú **Run**, haga clic en **Start Without Debugging**; se le pedirá elegir un emulador o un dispositivo Android USB conectado.

    <div class="dev-callout"><strong>Nota:</strong> <p>puede ejecutar este proyecto usando un tel&eacute;fono Android o el emulador de Android. La ejecuci&oacute;n con un tel&eacute;fono Android requiere la descarga de un controlador USB espec&iacute;fico del tel&eacute;fono.</p> <p>Para ejecutar el proyecto en el emulador de Android, debe definir por lo menos un dispositivo virtual Android (AVD). Use el administrador AVD para crear y administrar estos dispositivos.</p></div>

5.  En la aplicación, escriba un texto significativo, como *Realice el tutorial* y, a continuación, haga clic en **Add**.

    ![][]

    Tenga en cuenta que el texto guardado se almacena en una colección de la memoria y se muestra en la lista de abajo.

## <a name="create-service"></a><span class="short-header">Creación de un servicio móvil</span>Creación de un servicio móvil en el Portal de administración

[WACOM.INCLUDE [mobile-services-create-new-service-data][]]

## <a name="add-table"></a><span class="short-header">Incorporación de una tabla nueva</span>Incorporación de una tabla nueva al servicio móvil

Para poder almacenar datos de aplicaciones en el nuevo servicio móvil, primero debe crear una tabla.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  Haga clic en la pestaña **Data** y, a continuación, en **+Create**.

    ![][1]

    Esto muestra el cuadro de diálogo **Create new table**.

3.  En **Table name**, escriba *TodoItem* y, a continuación, haga clic en el botón de comprobación.

    ![][2]

    Con esto se crea una tabla de almacenamiento **TodoItem** con el conjunto de permisos predeterminado, lo que significa que todo usuario de la aplicación podrá tener acceso a los datos de la tabla y modificarlos.

    <div class="dev-callout"> 
<b>Nota:</b> 
<p>El mismo nombre de tabla se utiliza en la gu&iacute;a de inicio r&aacute;pido de Servicios m&oacute;viles. Sin embargo, cada tabla se crea en un esquema que es espec&iacute;fico de un servicio m&oacute;vil determinado. De esta manera, se evita que colisionen los datos cuando varios servicios m&oacute;viles utilizan la misma base de datos.</p> 
</div>

4.  Haga clic en la nueva tabla **TodoItem** y verifique que no haya filas de datos.

5.  Haga clic en la pestaña **Columns** y compruebe que hay solo una columna **id**, que se crea automáticamente.

    Este es el requisito mínimo para una tabla en Servicios móviles.

    <div class="dev-callout"><b>Nota:</b>
<p>Cuando est&aacute; habilitado el esquema din&aacute;mico en su servicio m&oacute;vil, se crean columnas autom&aacute;ticamente cuando se env&iacute;an objetos JSON al servicio m&oacute;vil mediante una operaci&oacute;n de inserci&oacute;n o de actualizaci&oacute;n.</p>
</div>

Ahora ya está listo para utilizar el nuevo servicio móvil como almacenamiento de datos para la aplicación.

## <a name="update-app"></a><span class="short-header">Actualización de la aplicación</span>Actualización de la aplicación para usar el servicio móvil para el acceso a datos

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

5.  Una vez que haya realizado el paso anterior, el proyecto indicará los errores de la compilación. Busque las tres ubicaciones restantes en las que se usa la variable `todoItemList` y convierta en comentarios las secciones indicadas.

6.  Ahora agregamos nuestros servicios móviles. Quite la marca de comentario de las líneas de código siguientes:

        private MobileServiceClient client; // Mobile Service Client references
        private IMobileServiceTable<TodoItem> todoTable; // Mobile Service Table used to access data   

7.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

8.  Haga clic en la pestaña **Panel** y anote la **dirección URL del sitio**, a continuación haga clic en **Administrar claves** y anote la **clave de la aplicación**.

    ![][3]

    Necesitará estos valores para obtener acceso al servicio móvil desde su código de aplicación.

9.  En la clase **Constants**, quite la marca de comentario de las siguientes variables de miembro:

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";

10. Reemplace **AppUrl** y **AppKey** de las variables anteriores por los valores recuperados del Portal de administración mencionado.

11. En el método **OnCreate**, quite la marca de comentario de las líneas de código siguientes que definen la variable **MobileServiceClient**:

        // Create the Mobile Service Client instance, using the provided
        // Mobile Service URL and key
        client = new MobileServiceClient(
            Constants.ApplicationURL,
            Constants.ApplicationKey).WithFilter(filter);

        // Get the Mobile Service Table instance to use
        todoTable = client.GetTable<TodoItem>();    

    De este modo, se crea la nueva instancia de MobileServiceClient que se usa para obtener acceso a su servicio móvil. Además, se crea la instancia de MobileServiceTable que se usa para el almacenamiento de datos de proxy en el servicio móvil.

12. Busque la clase ProgressFilter en la parte inferior del archivo y quite la marca de comentario. Esta clase muestra un indicador 'loading' mientras MobileServiceClient está ejecutando operaciones de red.

13. Quite la marca de comentario de estas líneas del método **CheckItem**:

        try {
            await todoTable.UpdateAsync(item);
            if (item.Complete)
                adapter.Remove(item);
        } catch (Exception e) {
            CreateAndShowDialog(e, "Error");
        }

    De este modo se envía una actualización del elemento al servicio móvil y se eliminan los elementos marcados del adaptador.

14. Quite la marca de comentario de estas líneas del método **AddItem**:

        try 
        {
            // Insert the new item
            await todoTable.InsertAsync(item);

            if (!item.Complete) 
                adapter.Add(item);          
        } 
        catch (Exception e) 
        {
            CreateAndShowDialog(e, "Error");
        }           

    Este código crea un elemento y lo inserta en la tabla del servicio móvil remoto.

15. Quite la marca de comentario de estas líneas del método **RefreshItemsFromTable**:

        try {
            // Get the items that weren't marked as completed and add them in the adapter
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

## <a name="test-app"></a><span class="short-header">Prueba de la aplicación</span>Prueba de la aplicación con su servicio móvil nuevo

1.  En el menú **Run**, haga clic en **Start Without Debugging** para iniciar el proyecto. Se le pedirá elegir una imagen de emulador existente o un dispositivo Android USB conectado.

    De este modo se ejecuta su aplicación, que se ha creado con Xamarin.Android, y se usa la biblioteca de clientes para enviar una consulta que devuelve elementos desde su servicio móvil.

2.  Como antes, escriba un texto descriptivo y, a continuación, haga clic en **Add**.

    Esto envía un elemento nuevo como inserción al servicio móvil.

3.  En el [Portal de administración][], haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4.  Haga clic en la pestaña **Data** y, a continuación, en **Browse**.

    ![][4]

    Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.

Así concluye el tutorial **Introducción a los datos** para Xamarin.Android.

## Obtener un ejemplo completado

Descargue el [proyecto de ejemplo completado][Descarga del proyecto de la aplicación Xamarin.Android]. Asegúrese de actualizar las variables **applicationURL** y **applicationKey** con su propia configuración de Azure.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de Xamarin.Android para trabajar con datos en Servicios móviles.

A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Validación y modificación de datos con scripts][]
    <br/>Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación][]
    <br/>Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Cuando haya completado la serie de datos, pruebe estos otros tutoriales de Xamarin.Android:

-   [Introducción a la autenticación][]
    <br/>Aprenda a autenticar a los usuarios de su aplicación.

-   [Introducción a las notificaciones de inserción][]
    <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación con Servicios móviles.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. TODO:: update 'Download the Android app project' download link, 'GitHub', completed project, etc. -->

  [C# para Tienda Windows]: /en-us/develop/mobile/tutorials/get-started-with-data-dotnet "C# para Tienda Windows"
  [JavaScript para Tienda Windows]: /en-us/develop/mobile/tutorials/get-started-with-data-js "JavaScript para Tienda Windows"
  [Windows Phone]: /en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone"
  [iOS]: /en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS"
  [Android]: /en-us/develop/mobile/tutorials/get-started-with-data-android "Android"
  [HTML]: /en-us/develop/mobile/tutorials/get-started-with-data-html "HTML"
  [Xamarin.iOS]: /en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android"
  [Introducción a los Servicios móviles]: /en-us/develop/mobile/tutorials/get-started-xamarin-android
  [Descarga del proyecto de la aplicación Xamarin.Android]: http://go.microsoft.com/fwlink/p/?LinkId=331302
  [Creación del servicio móvil]: #create-service
  [Incorporación de una tabla de datos para almacenamiento]: #add-table
  [Actualización de la aplicación para usar Servicios móviles]: #update-app
  [Prueba de la aplicación en Servicios móviles]: #test-app
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A9C9624B5
  [componente Servicios móviles de Azure]: http://components.xamarin.com/view/azure-mobile-services/
  []: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [1]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-data-tab-empty.png
  [2]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-create-todoitem-table.png
  [3]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-dashboard-tab.png
  [Portal de administración]: https://manage.windowsazure.com/
  [4]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
  [Validación y modificación de datos con scripts]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
  [Limitación de consultas con paginación]: /en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android
  [Introducción a la autenticación]: /en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android
  [Introducción a las notificaciones de inserción]: /en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android
