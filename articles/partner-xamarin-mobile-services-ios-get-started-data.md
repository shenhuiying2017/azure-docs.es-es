<properties linkid="develop-mobile-tutorials-get-started-with-data-xamarin-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Xamarin.iOS) - Azure Mobile Services" metaKeywords="Azure Xamarin.iOS data, Azure mobile services data, " description="Learn how to store and access data from your Azure Mobile Services Xamarin.iOS app." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="" />

Introducción a los datos en Servicios móviles
=============================================

[C\# para Tienda Windows](/es-es/develop/mobile/tutorials/get-started-with-data-dotnet "C# para Tienda Windows")[JavaScript para Tienda Windows](/es-es/develop/mobile/tutorials/get-started-with-data-js "JavaScript para Tienda Windows")[Windows Phone](/es-es/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/es-es/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/es-es/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/es-es/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/es-es/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/es-es/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de Xamarin.iOS. En este tutorial descargará una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil en la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

**Nota:**

Este tutorial tiene como fin ayudarle a comprender cómo los Servicios móviles le permiten usar Azure para almacenar y recuperar datos de una aplicación Xamarin.iOS. Para ello, en este tema se recorren muchos de los pasos que se completan automáticamente en el inicio rápido de Servicios móviles. Si esta es la primera vez que usa los Servicios móviles, considere la posibilidad de completar antes el tutorial [Introducción a los Servicios móviles](/es-es/develop/mobile/tutorials/get-started-xamarin-ios).

Este tutorial le guiará a través de estos pasos básicos:

1.  [Descarga del proyecto de la aplicación Xamarin.iOS](http://go.microsoft.com/fwlink/p/?LinkId=331302)
2.  [Creación del servicio móvil](#create-service)
3.  [Incorporación de una tabla de datos para almacenamiento](#add-table)
4.  [Actualización de la aplicación para usar Servicios móviles](#update-app)
5.  [Prueba de la aplicación en Servicios móviles](#test-app)

Este tutorial requiere el [componente Servicios móviles de Azure](http://components.xamarin.com/view/azure-mobile-services/), [XCode 5.0](https://go.microsoft.com/fwLink/p/?LinkID=266532), [Xamarin.iOS] e iOS 5.0 o versiones posteriores.

**Nota:**

para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A643EE910&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-xamarin-ios%2F).

Descarga del proyectoDescarga del proyecto GetStartedWithData
-------------------------------------------------------------

Este tutorial se ha creado sobre la [aplicación GetStartedWithData](http://go.microsoft.com/fwlink/p/?LinkId=331302), que es una aplicación Xamarin.iOS. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de Xamarin.iOS para Servicios móviles, salvo por los elementos agregados que se almacenan localmente en la memoria.

1.  Descargue la [aplicación GetStartedWithData](http://go.microsoft.com/fwlink/p/?LinkId=331302).

2.  En Xamarin Studio, abra el proyecto descargado y examine la clase **TodoService**.

  Tenga en cuenta que hay varios comentarios **// TODO** que especifican los pasos que debe realizar para que la aplicación funcione con su servicio móvil.

3.  Vaya al menú **Run** y elija **Start Without Debugging** para iniciar la aplicación.

4.  En la aplicación, escriba algo de texto en el cuadro de texto y, a continuación, haga clic en el botón **+**.

  ![][0]  

  Observe que el texto guardado se muestra en la lista a continuación.

Creación de un servicio móvilCreación de un servicio móvil en el Portal de administración
-----------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

Incorporación de una tabla nuevaIncorporación de una tabla nueva al servicio móvil
----------------------------------------------------------------------------------

Para poder almacenar datos de aplicaciones en el nuevo servicio móvil, primero debe crear una tabla en la instancia de Base de datos SQL asociada.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  Haga clic en la pestaña **Data** y, a continuación, en **+Create**.

  ![][5]

  Se muestra el cuadro de diálogo **Crear nueva tabla**.

3.  En **Table name**, escriba *TodoItem* y, a continuación, haga clic en el botón de comprobación.

  ![](./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png)

   Con esto se crea una tabla de almacenamiento **TodoItem** con el conjunto de permisos predeterminado, lo que significa que todo usuario de la aplicación podrá tener acceso a los datos de la tabla y modificarlos.

    <div class="dev-callout"> 
    <b>Nota:</b> 
    <p>El mismo nombre de tabla se utiliza en la guía de inicio rápido de Servicios móviles. Sin embargo, cada tabla se crea en un esquema que es específico de un servicio móvil determinado. De esta manera, se evita que colisionen los datos cuando varios servicios móviles utilizan la misma base de datos.</p> 
    </div>

1.  Haga clic en la nueva tabla **TodoItem** y verifique que no haya filas de datos.

2.  Haga clic en la pestaña **Columns** y compruebe que hay solo una columna **id**, que se crea automáticamente.

    Este es el requisito mínimo para una tabla en Servicios móviles.

    **Nota:**

    Cuando está habilitado el esquema dinámico en su servicio móvil, se crean columnas automáticamente cuando se envían objetos JSON al servicio móvil mediante una operación de inserción o de actualización.

Ahora ya está listo para utilizar el nuevo servicio móvil como almacenamiento de datos para la aplicación.

Actualización de la aplicaciónActualización de la aplicación para usar el servicio móvil para el acceso a datos
---------------------------------------------------------------------------------------------------------------

Ahora que el servicio móvil está listo, puede actualizar la aplicación a fin de almacenar elementos en Servicios móviles en lugar de en la colección local.

1.  Si todavía no aparece **Servicios móviles de Azure** en la carpeta de componentes, haga clic con el botón secundario en **Components**, elija **Get More Components** y, a continuación, busque **Servicios móviles de Azure**.

2.  En la vista de soluciones de Xamarin Studio, abra la clase **TodoService** y quite la marca de comentario de la siguiente instrucción `using`:

         using Microsoft.WindowsAzure.MobileServices;

3.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

4.  Haga clic en la pestaña **Panel** y anote la **URL del sitio**, a continuación haga clic en **Manage keys** y anote la **clave de la aplicación**.

  ![][8]

5.  En la clase **Constants**, quite la marca de comentario de las siguientes constantes:

         public const string ApplicationURL = @"AppUrl";
         public const string ApplicationKey = @"AppKey";

6.  Reemplace **AppUrl** y **AppKey** de las constantes anteriores por los valores que encontró anteriormente en el Portal de administración.

7.  En la clase **TodoService**, quite la marca de comentario de la siguiente línea de código:

         private MobileServiceClient client;

  De esta manera se crea una propiedad que representa el MobileServiceClient que se conecta al servicio.

8.  En la clase **TodoService**, quite la marca de comentario de la siguiente línea de código:

         private IMobileServiceTable<TodoItem> todoTable;  

  Así se crea una representación de propiedad para su tabla de servicios móviles.

9.  Quite la marca de comentario de las siguientes líneas en el constructor **TodoService**:

         // TODO:: Quite la marca de comentario de estas líneas para utilizar Servicios móviles
         client = new MobileServiceClient(Constants.ApplicationURL, Constants.ApplicationKey).WithFilter(this); 
         todoTable = client.GetTable<TodoItem>(); 

    Con esto se crea una instancia del cliente de Servicios móviles y se crea la instancia de tabla TodoItem.

10. Quite la marca de comentario de las siguientes líneas en el método **RefreshDataAsync** en **TodoService**

         // TODO:: Quite la marca de comentario de estas líneas para utilizar Servicios móviles

	try
		{ 
			// Este código actualiza las entradas de la vista de lista al consultar la tabla TodoItems. 
			// Esta consulta excluye los elementos TodoItems 
			completados = await todoTable 
				.Where (todoItem =\> todoItem.Complete == false).ToListAsync(); 
		} 
		catch (MobileServiceInvalidOperationException e) 
		{ 
			Console.Error.WriteLine (@"ERROR {0}", e.Message); 
			return null; 
		}

    Así se crea una consulta para devolver todas las tareas que no se hayan completado todavía.

11. Busque el método **InsertTodoItemAsync** y quite la marca de comentario de la siguiente línea:

        await todoTable.InsertAsync(todoItem);

    Este código envía una solicitud de inserción al servicio móvil.

12. Busque el método **CompleteItemAsync** y quite la marca de comentario de la siguiente línea:

        await todoTable.UpdateAsync(item);

  Este código quita los elementos TodoItems una vez que se han marcado como completados.

Ahora que se ha actualizado la aplicación para utilizar Servicios móviles para almacenamiento back-end, es momento de probar la aplicación con Servicios móviles.

Prueba de la aplicaciónPrueba de la aplicación con su servicio móvil nuevo
--------------------------------------------------------------------------

1.  En Xamarin Studio, seleccione un emulador o un dispositivo en el que implementar desde uno de los cuadros combinados principales, luego vaya al menú **Run** y seleccione **Start WithoutDebugging** para iniciar la aplicación.

  Esta acción ejecuta el cliente de Servicios móviles de Azure, que se ha creado con Xamarin.iOS, que consulta elementos desde su servicio móvil.

2.  Al igual que anteriormente, escriba texto en el cuadro de texto y, a continuación, haga clic en el botón **+**.

  Esto envía un elemento nuevo como inserción al servicio móvil.

3.  En el [Portal de administración](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4.  Haga clic en la pestaña **Data** y, a continuación, en **Browse**.

  ![][9]
          
  Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para coincidir con la clase TodoItem en la aplicación.

Así concluye el tutorial **Introducción a los datos** para Xamarin.iOS.

Obtener un ejemplo completado
-----------------------------

Descargue el [proyecto de ejemplo completado](http://go.microsoft.com/fwlink/p/?LinkId=331302). Asegúrese de actualizar las variables **applicationURL** y **applicationKey** con su propia configuración de Azure.

Pasos siguientes
----------------

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de iOS para trabajar con datos en Servicios móviles.

A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Validación y modificación de datos con scripts](/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios)
  <br/>Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación](/es-es/develop/mobile/tutorials/add-paging-to-data-xamarin-ios)
  <br/>Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Una vez que haya completado la serie de datos, pruebe con uno de estos otros tutoriales para iOS:

-   [Introducción a la autenticación](/es-es/develop/mobile/tutorials/get-started-with-users-xamarin-ios)
  <br/>Aprenda a autenticar a los usuarios de su aplicación.

-   [Introducción a las notificaciones de inserción](/es-es/develop/mobile/tutorials/get-started-with-push-xamarin-ios)
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación con Servicios móviles.


<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png




[5]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png