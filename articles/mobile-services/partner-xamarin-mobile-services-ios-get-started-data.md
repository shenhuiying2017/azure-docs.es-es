<properties
	pageTitle="Incorporación de Servicios móviles a una aplicación existente (Xamarin.iOS) - Servicios móviles de Azure"
	description="Obtenga información acerca de cómo almacenar y acceder a datos desde su aplicación de Xamarin.iOS para Servicios móviles de Azure."
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="dwrede"
	services="mobile-services"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/24/2015"
	ms.author="ggailey777"/>

# Incorporación de Servicios móviles a una aplicación existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de Xamarin.iOS. En este tutorial descargará una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil en la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

> [AZURE.NOTE]Este tutorial está destinado a ayudarle a comprender mejor cómo los Servicios móviles le permiten usar Azure para almacenar y recuperar datos de una aplicación Xamarin.iOS. Para ello, en este tema se recorren muchos de los pasos que se completan automáticamente en el inicio rápido de Servicios móviles. Si es la primera vez que usa los Servicios móviles, considere la posibilidad de completar antes el tutorial [Introducción a los Servicios móviles](/develop/mobile/tutorials/get-started-xamarin-ios).

Este tutorial le guiará a través de estos pasos básicos:

1. [Descarga del proyecto de la aplicación Xamarin.iOS][GitHub]
2. [Creación del servicio móvil]
3. [Agregar una tabla de datos para almacenamiento]
4. [Actualización de la aplicación para usar Servicios móviles]
5. [Prueba de la aplicación en Servicios móviles]

Este tutorial requiere el [Componente de Servicios móviles de Azure], [XCode 6.0][Install Xcode], [Xamarin.iOS] e iOS 7.0 o una versión posterior.

> [AZURE.IMPORTANT]Para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-xamarin-ios%2F"%20target="_blank).

## <a name="download-app"></a>Descarga del proyecto GetStartedWithData

Este tutorial se basa en la [aplicación GetStartedWithData][GitHub], que es una aplicación Xamarin.iOS. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de Xamarin.iOS para Servicios móviles, salvo por los elementos agregados que se almacenan localmente en la memoria.

1. Descargue la [aplicación GetStartedWithData][GitHub].

2. En Xamarin Studio, abra el proyecto descargado y examine la clase **TodoService**.

   	Tenga en cuenta que hay varios comentarios **// TODO** que especifican los pasos que debe seguir para que la aplicación funcione con su servicio móvil.

3. Vaya al menú **Run** (Ejecutar) y elija **Start Without Debugging** (Iniciar sin depurar) para iniciar la aplicación.

4. En la aplicación, escriba algo de texto en el cuadro de texto y luego haga clic en el botón **+**.

   	![][0]

   	Observe que el texto guardado se muestra en la lista a continuación.

## <a name="create-service"></a>Creación de un servicio móvil en el Portal de administración

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a>Agregar una tabla nueva al servicio móvil

Para poder almacenar datos de aplicaciones en el nuevo servicio móvil, primero debe crear una tabla en la instancia de Base de datos SQL asociada.

1. En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2. Haga clic en la pestaña **Datos** y luego en **+Crear**.

   	![][5]

   	Se muestra el cuadro de diálogo **Crear nueva tabla**.

3. En **Nombre de tabla** escriba _TodoItem_ y haga clic en el botón de comprobación.

  	![][6]

  	Con esto se crea una tabla de almacenamiento **TodoItem** con el conjunto de permisos predeterminado, lo que significa que cualquier usuario de la aplicación podrá acceder a los datos de la tabla y modificarlos.

    > [AZURE.NOTE]El mismo nombre de tabla se utiliza en la guía de inicio rápido de Servicios móviles. Sin embargo, cada tabla se crea en un esquema que es específico de un servicio móvil determinado. De esta manera, se evita que colisionen los datos cuando varios servicios móviles utilizan la misma base de datos.

4. Haga clic en la nueva tabla **TodoItem** y compruebe que no haya filas de datos.

5. Haga clic en la pestaña **Columnas** y compruebe que solo hay una columna **id**, que se crea automáticamente.

	  Este es el requisito mínimo para una tabla en Servicios móviles.

    > [AZURE.NOTE]Cuando está habilitado el esquema dinámico en su servicio móvil, se crean columnas automáticamente cuando se envían objetos JSON al servicio móvil mediante una operación de inserción o de actualización.

Ahora ya está listo para utilizar el nuevo servicio móvil como almacenamiento de datos para la aplicación.

## <a name="update-app"></a>Actualización de la aplicación para usar el servicio móvil para el acceso a datos

Ahora que el servicio móvil está listo, puede actualizar la aplicación a fin de almacenar elementos en Servicios móviles en lugar de en la colección local.

1. Si todavía no aparece **Servicios móviles de Azure** en la carpeta de componentes, haga clic con el botón secundario en **Componentes**, elija **Obtener más componentes** y busque **Servicios móviles de Azure**.

2. En la vista de soluciones de Xamarin Studio, abra la clase **TodoService** y quite la marca de comentario de la instrucción `using` siguiente:

        using Microsoft.WindowsAzure.MobileServices;

3. En el Portal de administración, haga clic en **Servicios móviles** y luego en el servicio móvil que acaba de crear.

4. Haga clic en la pestaña **Panel** y anote la **dirección URL del sitio**; a continuación, haga clic en **Administrar claves** y anote la **clave de la aplicación**.

   	![][8]

5. En la clase **Constants**, quite la marca de comentario de las siguientes constantes:

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";

6. Reemplace **AppUrl** y **AppKey** de las constantes anteriores por los valores que encontró anteriormente en el Portal de administración.

7. En la clase **TodoService**, quite la marca de comentario de la siguiente línea de código:

        private MobileServiceClient client;

   	De esta manera se crea una propiedad que representa el MobileServiceClient que se conecta al servicio.

8. En la clase **TodoService**, quite la marca de comentario de la siguiente línea de código:

        private IMobileServiceTable<TodoItem> todoTable;  

   	Así se crea una representación de propiedad para su tabla de servicios móviles.

9. Quite la marca de comentario de las siguientes líneas en el constructor **TodoService**:

		// TODO:: Uncomment these lines to use Mobile Services
		client = new MobileServiceClient(Constants.ApplicationURL, Constants.ApplicationKey).WithFilter(this);
		todoTable = client.GetTable<TodoItem>();

    Con esto se crea una instancia del cliente de Servicios móviles y se crea la instancia de tabla TodoItem.

10. Quite la marca de comentario de las siguientes líneas en el método **RefreshDataAsync** en **TodoService**.

			// TODO:: Uncomment these lines to use Mobile Services
			try
	    {
				// This code refreshes the entries in the list view by querying the TodoItems table.
				// The query excludes completed TodoItems
				Items = await todoTable
					.Where (todoItem => todoItem.Complete == false).ToListAsync();
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

13. Busque el método **CompleteItemAsync** y quite la marca de comentario de la siguiente línea:

		await todoTable.UpdateAsync(item);

   	Este código quita los elementos TodoItems una vez que se han marcado como completados.

Ahora que se ha actualizado la aplicación para utilizar Servicios móviles para almacenamiento back-end, es momento de probar la aplicación con Servicios móviles.

## <a name="test-app"></a>Prueba de la aplicación en su nuevo servicio móvil

1. En Xamarin Studio, seleccione un emulador o un dispositivo en el que implementar desde uno de los cuadros combinados principales, luego vaya al menú **Run** (Ejecutar) y seleccione **Start WithoutDebugging** (Iniciar sin depurar) para iniciar la aplicación.

   	Esta acción ejecuta el cliente de Servicios móviles de Azure, que se ha creado con Xamarin.iOS, que consulta elementos desde su servicio móvil.

2. Igual que antes, escriba texto en el cuadro de texto y haga clic en el botón **+**.

   	Esto envía un elemento nuevo como inserción al servicio móvil.

3. En el [Portal de administración], haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4. Haga clic en la pestaña **Datos** y, a continuación, en **Examinar**.

   	![][9]

   	Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.

Así concluye el tutorial **Introducción a los datos** para Xamarin.iOS.

## Obtener un ejemplo completado
Descargue el [proyecto de ejemplo completado]. Asegúrese de actualizar las variables **applicationURL** y **applicationKey** con su propia configuración de Azure.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de iOS para trabajar con datos en Servicios móviles.

A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

* [Validación y modificación de datos con scripts] <br/>Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

* [Limitación de consultas con paginación] <br/>Aprenda a usar la paginación en consultas para controlar la cantidad de datos que se gestionan en una única solicitud.

Una vez que haya completado la serie de datos, pruebe con uno de estos otros tutoriales para iOS:

* [Introducción a la autenticación] <br/>Aprenda a autenticar a los usuarios de su aplicación.

* [Introducción a las notificaciones de inserción] <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación con Servicios móviles.

<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Creación del servicio móvil]: #create-service
[Agregar una tabla de datos para almacenamiento]: #add-table
[Actualización de la aplicación para usar Servicios móviles]: #update-app
[Prueba de la aplicación en Servicios móviles]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png




[5]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png

<!-- URLs. TODO:: update download link, github link, and completed example project with new Xamarin.iOs projects -->
[Validación y modificación de datos con scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
[Limitación de consultas con paginación]: /develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introducción a la autenticación]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introducción a las notificaciones de inserción]: /develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Azure Management Portal]: https://manage.windowsazure.com/
[Portal de administración]: https://manage.windowsazure.com/
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[GitHub repo]: http://go.microsoft.com/fwlink/p/?LinkId=268784
[Componente de Servicios móviles de Azure]: http://components.xamarin.com/view/azure-mobile-services/

[proyecto de ejemplo completado]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[Xamarin.iOS]: http://xamarin.com/download
 

<!---HONumber=August15_HO6-->