<properties 
	pageTitle="Introducción a los datos (Xamarin.Android) - Servicios móviles de Azure" 
	description="Obtenga información acerca de cómo almacenar y acceder a datos desde su aplicación de Xamarin.Android para Servicios móviles de Azure." 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	manager="dwrede" 
	services="mobile-services" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="donnam"/>

# Incorporación de Servicios móviles a una aplicación existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]	

<p>Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de Xamarin.Android. En este tutorial descargará una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil en la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.</p>

> [AZURE.NOTE] Este tutorial está destinado a ayudarle a comprender mejor cómo los Servicios móviles le permiten usar Azure para almacenar y recuperar datos de una aplicación Xamarin.Android. Para ello, en este tema se recorren muchos de los pasos que se completan automáticamente en el inicio rápido de Servicios móviles. Si esta es la primera vez que usa Servicios móviles, considere la posibilidad de realizar antes el tutorial [Introducción a Servicios móviles](/es-es/develop/mobile/tutorials/get-started-xamarin-android).

Este tutorial le guiará a través de estos pasos básicos:

1. [Descargar el proyecto de la aplicación Xamarin.Android][GitHub].
2. [Crear el servicio móvil]
3. [Agregar una tabla de datos para almacenamiento]
4. [Actualizar la aplicación para usar Servicios móviles]
5. [Probar la aplicación en Servicios móviles]

> [AZURE.IMPORTANT] para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5"%20target="_blank).

Este tutorial requiere el [componente de Servicios móviles de Azure], [Xamarin.Android], y el SDK de Android 4.2 o una versión posterior. 

> [AZURE.NOTE] El proyecto GetStartedWithData descargado requiere Android 4.2 o una versión más reciente. No obstante, el SDK para Servicios móviles solo requiere Android 2.2 o una versión más reciente.

<h2><a name="download-app"></a>Descargar el proyecto GetStartedWithData</h2>

Este tutorial se ha creado sobre la [aplicación GetStartedWithData][GitHub], que es una aplicación Xamarin.Android. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de Android para Servicios móviles, salvo por los elementos agregados que se almacenan localmente en la memoria. 

1. Descargue la aplicación de ejemplo  `GetStartedWithData` y extraiga los archivos en su equipo. 

2. En Xamarin Studio, haga clic en **File**, luego en **Open**, vaya a donde extrajo el proyecto de ejemplo GetStartedWithData, seleccione **XamarinTodoQuickStart.Android.sln** y ábralo.

3. Busque y abra la clase **TodoActivity**.

   	Tenga en cuenta que hay comentarios `// TODO::` que especifican los pasos que debe realizar para que la aplicación funcione con su servicio móvil.

5. En el menú **Run** (Ejecutar), haga clic en **Start Without Debugging** (Iniciar sin depurar); se le pedirá elegir un emulador o un dispositivo Android USB conectado.

	> [AZURE.IMPORTANT] puede ejecutar este proyecto usando un teléfono Android o el emulador de Android. La ejecución con un teléfono Android requiere la descarga de un controlador USB específico del teléfono.
	> 
	> Para ejecutar el proyecto en el emulador de Android, debe definir por lo menos un dispositivo virtual Android (AVD). Use el administrador AVD para crear y administrar estos dispositivos.

6. En la aplicación, escriba un texto significativo, como "Realice el tutorial" y, a continuación, haga clic en **Agregar**.

   	![][13]

   	Tenga en cuenta que el texto guardado se almacena en una colección de la memoria y se muestra en la lista de abajo.

<h2><a name="create-service"></a>Creación de un servicio móvil nuevo en el Portal de administración</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Incorporación de una tabla nueva al servicio móvil</h2>

Para poder almacenar datos de aplicaciones en el nuevo servicio móvil, primero debe crear una tabla.  

1. En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2. Haga clic en la pestaña **Data** y, a continuación, en **+Create**.

   	![][5]

   	Esto muestra el cuadro de diálogo **Create new table**.

3. En **Nombre de tabla** escriba _TodoItem_ y, a continuación, haga clic en el botón de comprobación.

  	![][6]

  	Con esto se crea una tabla de almacenamiento **TodoItem** con el conjunto de permisos predeterminado, lo que significa que todo usuario de la aplicación podrá tener acceso a los datos de la tabla y modificarlos. 

    > [AZURE.NOTE] El mismo nombre de tabla se utiliza en la guía de inicio rápido de Servicios móviles. Sin embargo, cada tabla se crea en un esquema que es específico de un servicio móvil determinado. De esta manera, se evita que colisionen los datos cuando varios servicios móviles utilizan la misma base de datos.

4. Haga clic en la nueva tabla **TodoItem** y verifique que no haya filas de datos.

5. Haga clic en la pestaña **Columnas** y compruebe que hay solo una columna **id**, que se crea automáticamente.

  	Este es el requisito mínimo para una tabla en Servicios móviles. 

    > [AZURE.NOTE] Cuando está habilitado el esquema dinámico en su servicio móvil, se crean columnas automáticamente cuando se envían objetos JSON al servicio móvil mediante una operación de inserción o de actualización.

Ahora ya está listo para utilizar el nuevo servicio móvil como almacenamiento de datos para la aplicación.

<h2><a name="update-app"></a>Actualización de la aplicación para usar el servicio móvil para el acceso a datos</h2>

Ahora que el servicio móvil está listo, puede actualizar la aplicación a fin de almacenar elementos en Servicios móviles en lugar de en la colección local. 

1. Si todavía no aparece **Servicios móviles de Azure** en la carpeta de componentes, haga clic con el botón secundario en **Componentes**, elija **Obtener más componentes** y, a continuación, busque **Servicios móviles de Azure**.

  	De este modo se agrega el componente del SDK de Servicios móviles al proyecto.

2. Abra el archivo **AndroidManifest.xml** y asegúrese de que existe la siguiente línea de permisos:

		<uses-permission android:name="android.permission.INTERNET" />

  	Con esto, la aplicación puede tener acceso a Servicios móviles en Azure.

3. En la ventana **Solución**, abra la clase **TodoActivity** y quite la marca de comentario de la siguiente línea de código: 

		using Microsoft.WindowsAzure.MobileServices;
 
4. Eliminaremos la lista de la memoria que utiliza actualmente la aplicación, para que podamos reemplazarla por un servicio móvil. En la clase **TodoActivity**, comente la línea de código siguiente que define la lista **todoItemList** existente.

		public List<TodoItem> todoItemList = new ArrayList<TodoItem>();

5. Una vez que haya realizado el paso anterior, el proyecto indicará los errores de la compilación. Busque las tres ubicaciones restantes en las que se usa la variable  `todoItemList` y convierta en comentarios las secciones indicadas. 

6. Ahora agregamos nuestros servicios móviles. Quite la marca de comentario de las líneas de código siguientes:

        private MobileServiceClient client; // Mobile Service Client references
        private IMobileServiceTable<TodoItem> todoTable; // Mobile Service Table used to access data   

7. En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

8. Haga clic en la pestaña **Panel** y anote la **dirección URL del sitio**, a continuación haga clic en **Administrar claves** y anote la **clave de la aplicación**.

   	![][8]

  	Necesitará estos valores para obtener acceso al servicio móvil desde su código de aplicación.

9. En la clase **Constants**, quite la marca de comentario de las siguientes variables de miembro:

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

13. Quite la marca de comentario de estas líneas del método **CheckItem** method:

		try {
			await todoTable.UpdateAsync(item);
			if (item.Complete)
				adapter.Remove(item);
		} catch (Exception e) {
			CreateAndShowDialog(e, "Error");
		}

   	De este modo se envía una actualización del elemento al servicio móvil y se eliminan los elementos marcados del adaptador.
    
14. Quite la marca de comentario de estas líneas del método **AddItem** method:
	
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

15. Quite la marca de comentario de estas líneas del método **RefreshItemsFromTableAsync**:

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

<h2><a name="test-app"></a>Prueba de la aplicación con su servicio móvil nuevo</h2>

1. En el menú **Run** (Ejecutar), haga clic en **Start Without Debugging** (Iniciar sin depurar) para iniciar el proyecto. Se le pedirá elegir una imagen de emulador existente o un dispositivo Android USB conectado.

	De este modo se ejecuta su aplicación, que se ha creado con Xamarin.Android, y se usa la biblioteca de clientes para enviar una consulta que devuelve elementos desde su servicio móvil.

5. Como antes, escriba un texto descriptivo y, a continuación, haga clic en **Add**.

   	This sends a new item as an insert to the mobile service.

3. En el [Portal de administración], haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4. Haga clic en la pestaña **Datos** y, a continuación, en **Examinar**.

   	![][9]
  
   	Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.

Así concluye el tutorial **Introducción a los datos** para Xamarin.Android.

## Obtener un ejemplo completado
Descargue el [proyecto de ejemplo completado]. Asegúrese de actualizar las variables **applicationURL** y **applicationKey** con su propia configuración de Azure. 

## <a name="next-steps">Pasos siguientes</a>

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de Xamarin.Android para trabajar con datos en Servicios móviles. 

A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

* [Validar y modificar datos con scripts]
  <br/>Obtenga más información sobre el uso de scripts de servidor en Servicios móviles para validar y cambiar los datos enviados desde su aplicación.

* [Limitación de consultas con paginación]
  <br/>Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Cuando haya completado la serie de datos, pruebe estos otros tutoriales de Xamarin.Android:

* [Introducción a la autenticación] 
	<br/>Aprenda la manera de autenticar a los usuarios de la aplicación.

* [Introducción a las notificaciones de inserción]
  <br/>Aprenda la manera de enviar una notificación de inserción muy básica a la aplicación con Servicios móviles.

<!-- Anchors. -->

[Obtener la aplicación de la Tienda Windows]: #download-app
[Crear el servicio móvil]: #create-service
[Agregar una tabla de datos para almacenamiento]: #add-table
[Actualizar la aplicación para usar Servicios móviles]: #update-app
[Probar la aplicación en Servicios móviles]: #test-app
[Pasos siguientes]:#next-steps

<!-- Images. -->





[5]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-quickstart-startup-android.png

<!-- Direcciones URL. TODO:: update 'Download the Android app project' download link, 'GitHub', completed project, etc. -->
[Validar y modificar datos con scripts]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
[Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-xamarin-android
[Introducción a los servicios móviles]: /es-es/develop/mobile/tutorials/get-started-xamarin-android
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-xamarin-android

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Portal de administración]: https://manage.windowsazure.com/
[Componente de Servicios móviles de Azure]: http://components.xamarin.com/view/azure-mobile-services/
[Descarga del proyecto de la aplicación Android]: http://www.google.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[SDK de Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125

[proyecto de ejemplo completado]: http://go.microsoft.com/fwlink/p/?LinkId=331302







<!--HONumber=42-->
