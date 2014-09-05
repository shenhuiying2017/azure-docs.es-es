<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet" urlDisplayName="Get Started with Data" pageTitle="Get started with data - Azure Mobile Services" metaKeywords="" description="Learn how to get started using data with Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="" />

Introducción a los datos en Servicios móviles con Visual Studio 2012
====================================================================

<div class="dev-center-tutorial-selector sublanding">
<a href="/en-us/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012" title="Windows Store C#" class="current">Windows Store C#</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-js-vs2012" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-android" title="Android">Android</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de la Tienda Microsoft. En este tutorial descargará un proyecto de Visual Studio 2012 para una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil a la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación. Puede ver la versión en vídeo de este tutorial haciendo clic en el clip de la derecha.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">Ver el tutorial (en inglés)</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-data-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir vídeo (en inglés)</span></a> <span class="time">15:33</span></div>
</div>

<div class="dev-callout"><b>Nota:</b>

<p>Este tutorial agrega la funcionalidad de Servicios móviles a una aplicación de la Tienda Windows creada en Visual Studio 2012. Visual Studio 2013 incluye nuevas características que facilitan la conexión de su aplicación de la Tienda Windows a Servicios móviles. Para obtener más información, consulte <a href="/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/">Introducción a los datos en Servicios móviles</a>.</p>
</div>

Este tutorial le guiará a través de estos pasos básicos:

1.  [Descarga del proyecto de la aplicación de la Tienda Windows](#download-app)
2.  [Creación del servicio móvil](#create-service)
3.  [Incorporación de una tabla de datos para almacenamiento](#add-table)
4.  [Actualización de la aplicación para usar Servicios móviles](#update-app)
5.  [Prueba de la aplicación en Servicios móviles](#test-app)

<div class="dev-callout"><b>Nota:</b>

<p>Para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Evaluación gratuita de Azure</a>.</p>
</div>

Descarga del proyectoDescarga del proyecto GetStartedWithData
-------------------------------------------------------------

Este tutorial se basa en la [aplicación GetStartedWithData](http://go.microsoft.com/fwlink/?LinkId=262308), que es una aplicación de la Tienda Windows. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de Servicios móviles, con la excepción de que los elementos agregados se almacenan localmente en la memoria.

1.  Descargue la versión de C\# de la aplicación de ejemplo GetStartedWithData desde el [sitio de código de ejemplo para desarrolladores](http://go.microsoft.com/fwlink/?LinkId=262308) (en inglés).

	![][10]

2.  En Visual Studio 2012 Express para Windows 8, abra el proyecto descargado y examine el archivo MainPage.xaml.cs.

  Observe que los objetos **TodoItem** agregados se almacenan en **ObservableCollection<TodoItem>** en memoria.

3.  Presione la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

4.  En la aplicación, escriba algún texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

	![][0]  

  Observe que el texto guardado aparece en la segunda columna bajo **Query and update data**.

Creación de un servicio móvilCreación de un servicio móvil en el Portal de administración
-----------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

Incorporación de una tabla nuevaIncorporación de una tabla nueva al servicio móvil
----------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

Actualización de la aplicaciónActualización de la aplicación para usar el servicio móvil para el acceso a datos
---------------------------------------------------------------------------------------------------------------

Ahora que el servicio móvil está listo, puede actualizar la aplicación a fin de almacenar elementos en Servicios móviles en lugar de en la colección local.

1.  En el **Explorador de soluciones** de Visual Studio, haga clic con el botón secundario en el nombre del proyecto y, a continuación, seleccione **Manage NuGet Packages**.

2.  En el panel izquierdo, seleccione la categoría **En línea**, seleccione **Include Prerelease**, busque `WindowsAzure.MobileServices`, haga clic en **Instalar** en el paquete **Servicios móviles de Azure** y acepte el contrato de licencia.

	![](./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-add-nuget-package-dotnet.png) 

	De este modo, se agrega la biblioteca de cliente de Servicios móviles al proyecto.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  Haga clic en la pestaña **Panel** y anote la **URL del sitio**, a continuación haga clic en **Manage keys** y anote la **clave de la aplicación**.

	![][8]

    Necesitará estos valores para obtener acceso al servicio móvil desde el código de la aplicación.

1.  En Visual Studio, abra el archivo App.xaml.cs y agregue o quite la marca de comentario en la siguiente instrucción `using`:

         using Microsoft.WindowsAzure.MobileServices;

2.  En este mismo archivo, quite la marca de comentario del código que define la variable **MobileService** y proporcione la dirección URL y la clave de aplicación del servicio móvil en el constructor **MobileServiceClient**, en ese orden.

         //public static MobileServiceClient MobileService = new MobileServiceClient( 
         //    "AppUrl", 
         //    "AppKey" 
         //); 

  De esta forma, se crea una nueva instancia de **MobileServiceClient** que se usa para obtener acceso al servicio móvil.

1.  En el archivo MainPage.xaml.cs, agregue o quite la marca de comentario en las siguientes instrucciones `using`:

         using Microsoft.WindowsAzure.MobileServices;
         using Newtonsoft.Json;

2.  En este mismo archivo, reemplace la definición de clase **TodoItem** por el siguiente código:

         public class TodoItem
         {
             public string Id { get; set; }

             [JsonProperty(PropertyName = "text")]
             public string Text { get; set; }

             [JsonProperty(PropertyName = "complete")]
             public bool Complete { get; set; }
         }

3.  Comente la línea que define la colección **items** existente y luego quite la marca de comentario de las siguientes líneas:

         private MobileServiceCollection<TodoItem, TodoItem> items;
         private IMobileServiceTable<TodoItem> todoTable = 
             App.MobileService.GetTable<TodoItem>();

	Este código crea una colección de enlaces (**elementos**) compatible con los servicios móviles y una clase proxy para la tabla de Base de datos SQL **TodoItem** (**todoTable**). 

4.  En el método **InsertTodoItem**, quite la línea de código que define la propiedad **TodoItem**.**Id**, agregue el modificador **async** al método y quite la marca de comentario de la siguiente línea de código:

         await todoTable.InsertAsync(todoItem);

  Este código inserta un elemento nuevo en la tabla.

1.  En el método **RefreshTodoItems**, agregue el modificador **async** al método y quite la marca de comentario en la siguiente línea de código:

         items = await todoTable.ToCollectionAsync();

  Esto define el enlace a la colección de elementos en todoTable, que contiene todos los objetos TodoItem devueltos por el servicio móvil. 

2.  En el método **UpdateCheckedTodoItem**, agregue el modificador **async** al método y quite la marca de comentario en la siguiente línea de código:

          await todoTable.UpdateAsync(item);

	Esto envía una actualización de elemento al servicio móvil.

Ahora que se ha actualizado la aplicación para utilizar Servicios móviles para almacenamiento back-end, es momento de probar la aplicación con Servicios móviles.

Prueba de la aplicaciónPrueba de la aplicación con su servicio móvil nuevo
--------------------------------------------------------------------------

1.  En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

2.  Al igual que anteriormente, escriba texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

  Esto envía un elemento nuevo como inserción al servicio móvil.

3.  En el [Portal de administración](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4.  Haga clic en la pestaña **Data** y, a continuación, en **Browse**.

	![][9]
          
	Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para coincidir con la clase TodoItem en la aplicación.

5.  En la aplicación, marque uno de los elementos de la lista, vuelva a la pestaña Browse del portal y haga clic en **Refresh**.

	Observe que el valor completo ha cambiado de **false** a **true**.

6.  En el archivo de proyecto MainPage.xaml.cs, reemplace el método **RefreshTodoItems** existente por el siguiente código que filtra los elementos completados:

         private async void RefreshTodoItems()
         {                       
             // Esta consulta filtra TodoItems completados. 
             items = await todoTable
                .Where(todoItem => todoItem.Complete == false)
                .ToCollectionAsync();

             ListItems.ItemsSource = items;            
         }

2.  En la aplicación, marque otro de los elementos de la lista y, a continuación, haga clic en el botón **Refresh**.

	Observe que el elemento marcado ahora desaparece de la lista. Cada actualización resulta en un recorrido de ida y vuelta al servicio móvil, que ahora devuelve datos filtrados.

Con esto concluye el tutorial **Introducción a los datos**.

Pasos siguientes
----------------

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de la Tienda Windows para trabajar con datos en Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Validación y modificación de datos con scripts](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
  <br/>Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet)
  <br/>Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Una vez que haya completado la serie de datos, intente con uno de estos otros tutoriales:

-   [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)
  <br/>Aprenda a autenticar a los usuarios de su aplicación.

-   [Introducción a las notificaciones de inserción](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/)
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

-   [Referencia conceptual de Servicios móviles con .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)
  <br/>Obtenga más información sobre cómo utilizar Servicios móviles con .NET.


<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-quickstart-startup.png




[7]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-add-nuget-package-dotnet.png
[8]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2012/mobile-data-sample-download-dotnet.png